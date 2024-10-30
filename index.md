---
layout: lesson
carpentry: "swc"
venue: 
address: 
country: "UK"
language: "English"
latlng: 
humandate: 
humantime: 
startdate: 
enddate: 
instructor: ["William Lucas"]
helper: []
email: ["w.lucas@epcc.ed.ac.uk"]
collaborative_notes: 
eventbrite: 
root: .
---

<h2>Description</h2>

This lesson provides an introduction to using ARCHER2 for users who:
  - have already used other HPC systems; and
  - want to compile (and possibly) develop HPC software on ARCHER2.

The lesson aims to answer the following questions:
  - What hardware is available on ARCHER2?
    + What does it consist of (login nodes, compute nodes, file systems, backup)?
    + How does this impact me as a user?
  - How can I access ARCHER2 interactively and transfer data?
  - What does the ARCHER2 application development environment look like and how do I use it?
  - How do I write job submission scripts and submit them to the ARCHER2 scheduler?
  - How can I be a good ARCHER2 citizen?
  - How can I debug code on ARCHER2 to fix problems?
  - How can I profile code on ARCHER2 to determine its performance?
  - How can I check what resources I am using and look at historical usage?
  - What are the next steps for me using ARCHER2 and how can I get more help?

<hr/>

<h2 id="general">General Information</h2>

{% comment %}
  LOCATION

  This block displays the address and links to maps showing directions
  if the latitude and longitude of the workshop have been set.  You
  can use https://itouchmap.com/latlong.html to find the lat/long of an
  address.
{% endcomment %}
{% if page.latlng %}
<p id="where">
  <strong>Where:</strong>
  {{page.address}}.
  Get directions with
  <a href="//www.openstreetmap.org/?mlat={{page.latlng | replace:',','&mlon='}}&zoom=16">OpenStreetMap</a>
  or
  <a href="//maps.google.com/maps?q={{page.latlng}}">Google Maps</a>.
</p>
{% endif %}

{% comment %}
  DATE

  This block displays the date and links to Google Calendar.
{% endcomment %}
{% if page.humandate %}
<p id="when">
  <strong>When:</strong>
  {{page.humandate}}.
  {% include workshop_calendar.html %}
</p>
{% endif %}

{% comment %}
  SPECIAL REQUIREMENTS

  Modify the block below if there are any special requirements.
{% endcomment %}
<p id="requirements">
  <strong>Requirements:</strong> Participants must bring a laptop with a
  Mac, Linux, or Windows operating system (not a tablet, Chromebook, etc.) that they have administrative privileges
  on. They should have a few specific software packages installed (listed
  <a href="#setup">below</a>). They are also required to abide by the <a href="https://www.archer2.ac.uk/training/code-of-conduct/">ARCHER2 Training Code of Conduct</a>.
</p>

{% comment %}
  ACCESSIBILITY

  Modify the block below if there are any barriers to accessibility or
  special instructions.
<p id="accessibility">
  <strong>Accessibility:</strong> We are committed to making this workshop
  accessible to everybody.
  The workshop organizers have checked that:
</p>
<ul>
  <li>The room is wheelchair / scooter accessible.</li>
  <li>Accessible restrooms are available.</li>
</ul>
<p>
  Materials will be provided in advance of the lesson and
  large-print handouts are available if needed by notifying the
  organizers in advance.  If we can help making learning easier for
  you (e.g. sign-language interpreters, lactation facilities) please
  get in touch (using contact details below) and we will
  attempt to provide them.
</p>
{% endcomment %}

{% comment %}
  CONTACT EMAIL ADDRESS

  Display the contact email address set in the configuration file.
{% endcomment %}
<p id="contact">
  <strong>Contact</strong>:
  Please email
  {% if page.email %}
    {% for email in page.email %}
      {% if forloop.last and page.email.size > 1 %}
        or
      {% else %}
        {% unless forloop.first %}
        ,
        {% endunless %}
      {% endif %}
      <a href='mailto:{{email}}'>{{email}}</a>
    {% endfor %}
  {% else %}
    to-be-announced
  {% endif %}
  for more information.
</p>

<hr/>

> ## Prerequisites
> You should be familiat working with the Linux command line interface. You
> should be able to connect to remote machines using SSH; if you are a Windows
> user, this means you will need to set up an SSH client. Though there are many
> options, we would strongly recommend using MobaXterm or working in WSL. You
> will also need to edit files remotely over SSH, so experience with `nano`,
> `emacs` or `vim` will be helpful. An alternative here is to edit files locally
> and copy them remotely with `scp` or `rsync`.
{: .prereq}

<hr/>

{% include links.md %}

