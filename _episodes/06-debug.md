---
title: "Debugging on ARCHER2"
teaching: 30
exercises: 30
start: True
questions:
- "What debugging tools are available on ARCHER2 and how can I access them?"
- "Where can I find more documentation on and get help with these tools?"
objectives:
- "Know what tools are available to help you debug  parallel applications on ARCHER2."
- "Know where to get further help."
keypoints:
- "Several debugging tools are available on ARCHER2."
- "The main debugging tool is *gdb4hpc*"
---

ARCHER2 has a range of debugging software available. In this section we will briefly list the tools
available on the system and then run through the process of debugging a small MPI program with
gdb4hpc. For more information, the ARCHER2 [debugging](https://docs.archer2.ac.uk/user-guide/debug/)
documentation will also be useful.

## Debugging tools overview

The following debugging tools are available on ARCHER2:

* **gdb4hpc** is a command-line tool working similarly to [gdb](https://www.gnu.org/software/gdb/)
  that allows users to debug parallel programs. It can launch parallel programs or attach to ones
  already running and allows the user to step through the execution to identify the causes of any
  unexpected behaviour. Available via ``module load gdb4hpc``.
* **valgrind4hpc** is a parallel memory debugging tool that aids in detection of memory leaks and
  errors in parallel applications. It aggregates like errors across processes and threads to simply
  debugging of parallel appliciations. Available via ``module load valgrind4hpc``.
* **STAT** generate merged stack traces for parallel applications. Also has visualisation tools.
  Available via ``module load cray-stat``.
* **ATP** scalable core file and backtrace analysis when parallel programs crash. Available via
  ``module load atp``.
* **CCDB** Cray Comparative Debugger. Compare two versions of code side-by-side to analyse differences.
  Available via ``module load cray-ccdb``.
* **ARM DDT** (previously known as Allinea DDT) will be available soon for ARCHER2 users.

## Using gdb4hpc to debug an application

For this exercise, we'll be debugging a short program using gdb4hpc. To start, we'll grab a copy of a buggy code from ARCHER2:

```
wget {{site.url}}{{site.baseurl}}/files/gdb4hpc_exercise.c
```
{: .bash}

You can look at the code if you want -- you might even be able to debug it by inspection (but that defeats the purpose of this exercise). When you're ready, compile the code using the C compiler wrappers and the debugging flag `-g`:

```
 cc -g gdb4hpc_exercise.c -o gdb_exercise
```
{: .bash}

You can choose a different name for your executable, but I'll be using `gdb_exercise` through this exercise for consistency -- if you use a different name, make the appropriate change wherever you see `gdb_exercise`.

We'll be using ``gdb4hpc`` to go through this program and see where errors might arise.

Setup your environment, load and launch ``gdb4hpc``:

```
 module load gdb4hpc
 gdb4hpc
```
{: .bash}

You will get some information about this version of the program and, eventually, you will get a command prompt:

```
gdb4hpc 4.12 - Cray Line Mode Parallel Debugger
With Cray Comparative Debugging Technology.
Copyright 2007-2021 Hewlett Packard Enterprise Development LP.
Copyright 1996-2016 University of Queensland. All Rights Reserved.

Type "help" for a list of commands.
Type "help <cmd>" for detailed help about a command.
dbg all>
```
{: .output}

We will use ``launch`` to start an application within gdb4hpc. For now, we want to run our simulation on a single process, so we will type:

```
 dbg all> launch --launcher-args="--account={{site.gid}} --partition=standard --qos=short --time=0:10:0 --tasks-per-node=1 --cpus-per-task=1 --exclusive --export=ALL" $my_prog{1} ./gdb_exercise
```
{: .bash}

This will launch an ``srun`` job for ``gdb_exercise`` on one of the compute nodes. The name ``my_prog`` will be used by ``gdb4hpc`` as a reference to this particular run of the program -- you will not be able to launch another program using this name, but you can use any name you want instead. Once the run is started, you can reference it by prepending it with a ``$`` sign, so ``$my_prog`` in this case. The number in the curly brackets ``{1}`` indicates the number of processes this job will be using (it's  1 here). You could use a larger number if you wanted. If you call for more processes than available on a single compute node, ``gdb4hpc`` will launch the program on an appropriate number of nodes. Note though that the more cores you ask for, the slower ``gdb4hpc`` will be to launch the tasks once the job has begun. We use ``--launcher-args`` to pass all the ``SBATCH`` options we would normally provide in a job script through to the job launcher.

Once the program is launched, gdb4hpc will load up the program and begin to run it. You will get output to screen something that looks like:

```
Starting application, please wait...
Creating MRNet communication network...
Waiting for debug servers to attach to MRNet communications network...
Timeout in 400 seconds. Please wait for the attach to complete.
Number of dbgsrvs connected: [1];  Timeout Counter: [0]
Finalizing setup...
Launch complete.
my_prog{0}: Initial breakpoint, main at /PATH/TO/gdb4hpc_exercise.c:9
```
{: .output}

The line number at which the initial breakpoint is made (in the above example,
line 9) corresponds to the first line within the `main` function.

Once the code is loaded, you can use various commands to move through your code. The following lists and describes some of the most useful ones:

* ``help`` -- Lists all gdb4hpc commands. You can run ``help COMMAND_NAME`` to learn more about a specific command (*e.g.* ``help launch`` will tell you about the launch command
* ``list`` -- Will show the current line of code and the 9 lines following. Repeated use of ``list`` will move you down the code in ten-line chunks.
* ``next`` -- Will jump to the next step in the program for each process and output which line of code each process is on. It will not enter subroutines. Note that there is no reverse-step in gdb4hpc.
* ``step`` -- Like ``next``, but this will step into subroutines.
* ``break source.c:10`` -- Set a breakpoint at line 10 of source code file ``source.c``.
* ``continue`` -- Run until a breakpoint is reached or execution is halted.
* ``halt`` -- Pause the program so you can examine it. Useful if you suspect e.g. that it may be e.g. stuck in blocking communication or in a loop.
* ``up``/``down`` -- Go up/down one stack frame in the program (*e.g.* from a subroutine back to main).
* ``print var`` -- Prints the value of variable ``var`` at this point in the code.
* ``watch var`` -- Like print, but will print whenever a variable changes value.
* ``backtrace`` -- Prints the stack trace for each process.
* ``quit`` -- Exits gdb4hpc.

For now, we will look at `list`, `next`, `print`, and `watch`. Running:

```
 dbg all> list
```
{: .bash}

should output the first 10 lines of `main`:

```
 my_prog{0}: 9
 my_prog{0}: 10	  // Initiallise MPI environment
 my_prog{0}: 11	  MPI_Init(NULL,NULL);
 my_prog{0}: 12
 my_prog{0}: 13	  // Get processor rank
 my_prog{0}: 14	  int rank;
 my_prog{0}: 15	  MPI_Comm_rank(MPI_COMM_WORLD,&rank);
 my_prog{0}: 16
 my_prog{0}: 17	  int count = rank + 1;
 my_prog{0}: 18
```
{: .output}

Repeating `list` will bring show you the next 10 lines, *etc.*.

At the moment, we are at the start of the program. By running `next`, we will move to the next executable part of the program

```bash
 dbg all> next
```
{: .bash}
```
 my_prog{0}: main at /PATH/TO/gdb4hpc_exercise.c:13
```
{: .output}

Running `list` again will output the ten lines from 11-20. We can jump forward multiple lines by running `next N` -- by replacing *N* with a number, we will jump down *N* executable lines within our code. The `next` command will not allow us to move from one subroutine or function to another.

We can see on line 15 that there is a variable `count` about to be set. If we type:

```
 dbg all> print count
```
{: .bash}

The current value of variable `count` is printed to screen. If we progress the code past line 15 and print this variable value again, it has changed to 1. If we wanted, we could have used the `watch` command to get a notification whenever the value of the variable changes.

> ## Exercise
> What happens if you keep using `next` and `list`?
> > ## Solution
> > The program will move from executable line to executable line until it reaches line 18, at which point the program is exited due to an MPI error
> {: .solution}
{: .challenge}

Let's now ``quit`` gdb4hpc, start it again, and try launching across multiple processes:

```
 dbg all> launch --launcher-args="--account={{site.gid}} --partition=standard --qos=short --time=0:10:0 --tasks-per-node=2 --cpus-per-task=1 --exclusive --export=ALL" $my_prog{2} ./gdb_exercise
```
{: .bash}

> ## Exercise
> The code seems to be trying to send the variable `count` from one process to another. Follow `count` (using `watch`) and see how it changes throughout the code. What happens?
> > ## Solution
> > Eventually, both processes will hang: process 0 hangs at an `MPI_Barrier` on line 19 and is stuck waiting for process 1 to reach its barrier. Process 1 is stuck at an `MPI_Recv` on line 21. Further investigation shows that it is waiting for an `MPI_Send` that does not exist -- the source is process 1 (which has not sent anything) and the tag is `1` (there is no MPI_Send with this tag).
> {: .solution}
{: .challenge}

Let's `quit` our program, fix that bug, and go back into `gdb4hpc`. Again, we'll launch our program on 2 processes, and again, we'll watch the variable `count`. This time, both processes are able to get the same value for the variable `count`. There is one final part of the code to look at -- process 0 will try to get the sum of all even numbers between 0 and 20. However, the program begins to hang when process 0 reached line 28 (process 1 also hangs, but it's already at the `MPI_Finalize` part of the routine so we don't need to worry about it). Once we reach this hang, we can't easily keep going. Let's stop this debugging session and restart it by using `release`:

```
 dbg all> release $my_prog
 dbg all> launch --launcher-args="--account={{site.gid}} --partition=standard --qos=short --tasks-per-node=2 --cpus-per-task=1 --exclusive --export=ALL" $my_new_prog{2} ./gdb_exercise
```
{: .bash}

This time, instead of `next`, we will use `step` -- this does the same as `next` with the added feature that we can go into functions and subroutines where applicable. As the new bug appears to come from the `sum_even` function, let's see where exactly the program hangs.

> ## Exercise
> Having `step`ed into the `sum_even` function, can you find where the code hangs and fix the problem?
> > ## Solution
> > The `i++` should be brought outside of the `if` part of the `while` loop. Changing this will make the code work fully.
> {: .solution}
{: .challenge}

After fixing this bug, you should be able to run this program to completion.

## Running from within an interactive job

When spending time doing a lot of debugging, you may find that you spend a lot of your time waiting for debugging jobs to start.
Rather than having each launch of your program within gdb4hpc start as a new job, you can start an interactive job with `salloc`.
Once the job starts, you can then run execute your program many times directly on the nodes you've reserved without having to
wait each time. Firstly you would start the interactive job

```
auser@ln01:/work/{{site.gid}}/{{site.gid}}/auser> salloc --partition=standard --qos=standard --nodes=2 --ntasks-per-node=128 --cpus-per-task=1 --distribution=block:block --hint=nomultithread --time=0:10:0 --account={{site.gid}}
```
{: .bash}

```
salloc: Pending job allocation 1445231
salloc: job 1445231 queued and waiting for resources
salloc: job 1445231 has been allocated resources
salloc: Granted job allocation 1445231
salloc: Waiting for resource configuration
salloc: Nodes nid[002090,002099] are ready for job
```
{: .output}

and then load and start gdb4hpc

```
auser@ln01:/work/{{site.gid}}/{{site.gid}}/auser> module load gdb4hpc
auser@ln01:/work/{{site.gid}}/{{site.gid}}/auser> gdb4hpc
```
{: .bash}

```
gdb4hpc 4.12 - Cray Line Mode Parallel Debugger
With Cray Comparative Debugging Technology.
Copyright 2007-2021 Hewlett Packard Enterprise Development LP.
Copyright 1996-2016 University of Queensland. All Rights Reserved.

Type "help" for a list of commands.
Type "help <cmd>" for detailed help about a command.
```
{: .output}

Now you can launch without having to specify the launcher arguments or waiting for Slurm to start the job.

```
dbg all> launch $my_prog{256} ./gdb_exercise
```
{: .bash}

```
Starting application, please wait...
Creating MRNet communication network...
Waiting for debug servers to attach to MRNet communications network...
Timeout in 400 seconds. Please wait for the attach to complete.
Number of dbgsrvs connected: [3];  Timeout Counter: [0]
Number of dbgsrvs connected: [111];  Timeout Counter: [0]
Number of dbgsrvs connected: [122];  Timeout Counter: [0]
Number of dbgsrvs connected: [256];  Timeout Counter: [0]
Finalizing setup...
Launch complete.
a{0..255}: Initial breakpoint, in main
```
{: .output}


## Attaching to a running job

If you find that a running job appears to be having problems, you can attach gd4hpc to it to examine the state
of execution. Firstly, find its job ID using `squeue`. Then we would use `sstat` to find the job step running the
code. A job step indicates which `srun` launch invoked the executable. A job script containing 3 `sruns` would have three
job steps. This means that most jobs with a single launch will have a job step of the form `<jobid>.0`.

At this point, you can load the gdb4hpc module if you haven't already done so and start it up. If we assume that you found
your job and step to be `12345.0`, you would then do

```
dbg all> attach $my_prog 12345.0
```
{: .bash}

and start debugging as before. If you attach to the wrong job, or just want to let it run freely again, run

```
dbg all> release $my_prog
```
{: .bash}

## Getting help with debugging tools

You can find more information on the debugging tools available on ARCHER2 in the ARCHER2 Documentation
and the HPE Cray documentation:

* [ARCHER2 Documentation](https://docs.archer2.ac.uk)
* [HPE Cray Technical Documentation](https://support.hpe.com/)

If the documentation does not answer your questions then please contact
[the ARCHER2 Service Desk](https://www.archer2.ac.uk/support-access/servicedesk.html) and they
will be able to assist you.

{% include links.md %}
