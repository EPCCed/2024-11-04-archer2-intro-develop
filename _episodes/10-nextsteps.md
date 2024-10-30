---
title: "Bootstrapping your use of ARCHER2"
teaching: 10
exercises: 65
questions:
- "How can I get started with using ARCHER?"
objectives:
- "Get help to get your work up and running on the ARCHER2 system"
keypoints:
- "Understand the next steps for you in using ARCHER2."
---

Now you know enough about ARCHER2 to explore how to use it for your work or to understand
what its potential benefits are you. You may also have ideas around where the 
barriers and difficulties may lie and have further questions on how you can 
start using and/or trying ARCHER2 for your work.

This session is designed to give you the opportunity to explore these questions and
issues. The instructors and helpers on the course will be on hand to answer your
questions and discuss next steps with you.

> ## Potential discussions
>
> Things you could discuss with the instructors and helpers could include:
>
> - Your computational workflow and where ARCHER2 could help
> - How to get access to ARCHER2 for your work
> - How to get help and support to get your work running using ARCHER2.
>   For example, software development, further training, access to local expertise
{: .callout}

## Options for this session

There are a number of different options for practical work during this session. The
challenges below include: exploring your own work; and an extended example using a parallel
HPC application. If you have something else you want to use the session for (e.g. to
discuss things with the instructors/helpers as described above) then please feel free
to do this. The idea of the session is to help you bootstrap your use of ARCHER2
and this will differ from individual to individual!

> ## Exploring your work using ARCHER2
>
> If you have a practical example of something from your area of work that you would like
> help with getting up and running on an ARCHER2 or exploring the performance of
> on an ARCHER2, this is great! Please feel free to discuss this with us and ask
> questions (both technical and non-technical).
{: .challenge}

> ## Investigate the HPC-UK build instruction repository
>
> The HPC-UK [build instructions repository on
> GitHub](https://github.com/hpc-uk/build-instructions) contains instructions on
> how to manually build many software packages on ARCHER2 and other systems.
> Among these is WRF, an application you may be familiar with. Take a look at
> this repository and, if you like, have a go at building and running one of
> these packages on ARCHER2. *N.B.* some packages, including WRF, may take
> several hours to build. In situations like this, it can be a good idea to
> build in a job on the compute nodes. Can you write a script to do so?
{: .challenge}

> ## Run the OSU Micro Benchmarks
>
> The [OSU Micro Benchmarks](https://mvapich.cse.ohio-state.edu/benchmarks/) 
> are a suite of small test programs which can be used to benchmark the
> performance of communication with MPI. You can try to compile and run the
> benchmarks on ARCHER2. When it comes to compiling, remember what we've said
> about the Cray Programming Environment, and when it comes to running the
> benchmarks use both the micro benchmarks' and ARCHER2's documentation to write
> appropriate job scripts. To start with, why not try running the `osu_latency`
> benchmark? This is a ping-pong test that measures how long it takes for a
> process to send messages of various sizes and then receive an answer. As such,
> it uses two MPI processes: try running the benchmark with both on the same
> node, then on two different nodes. What do you see in the results?
{: .challenge}

> ## Exploring the performance of GROMACS
>
> [GROMACS](http://www.gromacs.org) is a world-leading biomolecular modelling package
> that is heavily used on HPC systems around the world. Choosing the best resources
> for GROMACS calculations is non-trivial as it depends on may factors, including:
>
> - The underlying hardware of the HPC system being used
> - The actual system being modelled by the GROMACS package
> - The balance of processes to threads used for the parallel calculation
>
> In this exercise, you should try and decide on a good choice of resources and settings
> on ARCHER2 for a typical biomolecular system. This will involve:
>
> - Downloading the [input file for GROMACS]({{site.github.repository_url}}/blob/gh-pages/files/ion_channel.tpr?raw=true)
> - Writing a job submission script to run GROMACS on ARCHER2 using the system documentation
> - Varying the number of nodes (from 1 to 16 nodes is a good starting point) used for the GROMACS job
>   and benchmarking the performance (in ns/day)
> - Using the results from this study to propose a good resource choice for this GROMACS calculation
>
> If you want to explore further than this initial task then there are a number of 
> different interesting ways to do this. For example:
> 
> - Vary the number of threads used per process
> - Reduce the number of cores used per node
> - Allow the calculation to use Symmetric Mutithreading (SMT)
>
> Please ask for more information on these options from a helper!
{: .challenge}


{% include links.md %}


