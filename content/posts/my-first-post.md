---
title: 'Next-level automation with Huginn: Self-hosted & free using GCP'
date: 2020-02-21T03:39:52.000+00:00
categories:
- tech
- automation
tags:
- automation
- tech

---
![](/uploads/automation_small.jpg)

I'm a huge fan of productivity tools, especially when they allow me to automate certain things or organize things better. I was delighted to come across a recent  [thread on Hacker News](https://news.ycombinator.com/item?id=21772610 "Huginn") discussing a supercharged automation tool: [Huginn](https://github.com/huginn/huginn "Huginn"). This open-source software performs automated tasks by watching for 'events', and triggering 'actions' based on these events.

For example, if there's a sudden spike in discussion on Twitter with the terms "San Francisco Earthquake", Huginn can send a text to my phone. Or, if an amazing, time-sensitive flight deal is posted on one of the many deal-finding websites out there, Huginn can send me an email with the price and a link to Google Flights.

Huginn seemed to be a pretty powerful automation tool, but I wanted to take this a step further and introduce some organization - I wanted all notifications to be cataloged & delivered in a centralized way. A personal Slack workspace seemed like the perfect solution for this - I can have a `#flights` channel for flight deals, or a `#trending` channel for the, er, pending San Francisco emergencies.

Another hard requirement I had was that I wanted all of this to be free. Huginn is self-hosted, and has pretty lax runtime resource requirements (even able to run on a [Raspberry Pi](https://github.com/huginn/huginn/wiki/Running-Huginn-on-minimal-systems-with-low-RAM-&-CPU-e.g.-Raspberry-Pi)), so a GCP micro tier instance (1 instance free per month) was perfect for this.

## Getting Started

The easiest way to [install Huginn](https://github.com/huginn/huginn/blob/master/doc/docker/install.md "Huginn installation") is via Docker. Luckily, Google Compute Engine supports deploying Docker containers natively on a lean [container-optimized OS](https://cloud.google.com/container-optimized-os/docs "Container optimized GCP OS").

f1-micro instances have 614MB of memory. This is not enough to run Huginn out of the box - doing so will cause Docker to encounter `Error Code 137` [(out of memory)](https://success.docker.com/article/what-causes-a-container-to-exit-with-code-137) errors. To solve this, we need to create a swap file in the VM.

Head over to GCP, create a new project, and create a new instance.

On the instance creation page, use the following settings:

* `f1-micro` machine type (1 free per month)
* Check 'Deploy a container image to this VM instance'
* Container image URL is `docker.io/huginn/huginn`

Disk-based swap is [disabled](https://stackoverflow.com/questions/58210222/how-to-enable-swap-swapfile-on-google-container-optimized-os-on-gce) by default in container-optimized OS. To enable and set the swap file every time the VM is booted, we can use add a custom startup script:

    #! /bin/bash
    sysctl vm.disk_based_swap=1
    fallocate -l 2G /var/swapfile
    chmod 600 /var/swapfile
    mkswap /var/swapfile
    swapon /var/swapfile

After the VM is created, head to your VM's external URL (port 3000) and you should be greeted with the default Huginn login page!

![](/uploads/Screen Shot 2020-02-23 at 3.45.38 PM.png)