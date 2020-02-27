+++
categories = ["tech"]
date = 2020-02-25T00:00:00Z
description = "Huginn supercharges your productivity. Learn how to deploy it on GCP for free, and organize all notifications in a centralized Slack workspace."
tags = ["tech", "automation"]
title = "Personal automation with Huginn using Slack, Docker & GCP"
[menu.main]

+++
Productivity tools are great - especially when they allow me to automate certain things in an organized way. I was delighted to come across a recent  [thread on Hacker News](https://news.ycombinator.com/item?id=21772610 "Huginn") discussing a supercharged automation tool: [Huginn](https://github.com/huginn/huginn "Huginn"). This open-source software performs automated tasks by using 'agents' to watch for 'events', and triggering 'actions' based on these events.

For example, if there's a sudden spike in discussion on Twitter with the terms "San Francisco Earthquake", Huginn can send a text to my phone. Or, if an amazing, time-sensitive flight deal is posted on one of the many deal-finding websites out there, Huginn can send me an email with the price and a link to Google Flights.

![](/uploads/automation_small.jpg)

Compared to other popular automation tools (IFTTT, Zapier), Huginn has the following benefits:

* Self-hosted & completely private
* Powerful data processing: write your own JS or use shell scripts
* Liquid templating

I wanted to go beyond strict automation and introduce some organization - I wanted all notifications to be cataloged & delivered in a centralized way. A personal Slack workspace seemed like the perfect solution for this - I can have a `#flights` channel for flight deals, or a `#trending` channel for the, er, pending San Francisco emergencies.

Another requirement I had was that I wanted all of this to be free. Huginn has pretty lax runtime resource requirements (even able to run on a [Raspberry Pi](https://github.com/huginn/huginn/wiki/Running-Huginn-on-minimal-systems-with-low-RAM-&-CPU-e.g.-Raspberry-Pi), with some tweaking), so a free GCP micro tier instance was perfect for this.

## Automation Goals

Let's formalize what I specifically wanted to accomplish with Huginn. Note that this is a small subset of the things possible with Huginn - check out the project's [Github](https://github.com/huginn/huginn#here-are-some-of-the-things-that-you-can-do-with-huginn "Huginn Github") for more inspiration.

* **Twitter notifications**: whenever keywords of interest are tweeted (such as my projects or blog), I want to get notified immediately. Whenever a spike occurs for other keywords ("San Francisco Emergency"), notify me.
* **Hacker news notifications**: whenever an article hits the frontpage discussing something I'm interested in, notify me.
* **Flight deals**: if a flight deal is posted online to one of the many websites I follow (Secret Flying, ThePointsGuy, FlyerTalk to name a few), and the flight originates from a nearby airport, notify me.
* **Product deals**: if a product I'm interested in is posted on Slickdeals, notify me.

I want all notifications to be sent to me via a personal Slack workspace, on different channels.

## Deploying Huginn

### Prerequisites

The easiest way to [install Huginn](https://github.com/huginn/huginn/blob/master/doc/docker/install.md "Huginn installation") is via Docker. Luckily, Google Compute Engine supports deploying Docker containers natively on a lean [container-optimized OS](https://cloud.google.com/container-optimized-os/docs "Container optimized GCP OS").

There are a few key things we need to do in order to have a successful Huginn deploy on the F1-micro (free tier) instances.

**Enable and create a swap file**.

f1-micro instances have 614MB of memory. This is not enough to run Huginn out of the box - doing so will cause Docker to encounter `Error Code 137` [(out of memory)](https://success.docker.com/article/what-causes-a-container-to-exit-with-code-137) errors. To solve this, we need to create a swap file in the VM. Note that a swapfile will decrease the performance of Huginn - if you're interested in performance for a price, consider deploying on a better VM.

Disk-based swap is [disabled](https://stackoverflow.com/questions/58210222/how-to-enable-swap-swapfile-on-google-container-optimized-os-on-gce) by default in container-optimized OS. To enable and set the swap file every time the VM is booted, we can use a custom startup script (shown below).

**Mount the Huginn MySQL database to a directory on the host**.

By default, Huginn creates a MySQL database inside the container. This is problematic, as the container now relies on _state_, and your database will [get deleted every Huginn upgrade](https://github.com/huginn/huginn/blob/master/docker/multi-process/README.md). We can use a volume mount to mount the database in the container to a directory in the host. Alternatively, you can mount a persistent disk and write the database to it. 

### Deployment on GCP using Docker

Head over to GCP, create a new project, and create a new instance.

On the instance creation page, use the following settings:

* `f1-micro` machine type
* Check 'Deploy a container image to this VM instance'
* Container image URL is `docker.io/huginn/huginn`
* Add a Directory volume mount. The mount and host paths should be `/var/lib/mysql`

We also need to add in a statup script. This script lets us 1) enable and turn on a swap file, and 2) change permissions of the volume mount on the host. The latter is required or MySQL won't be able to start.

    #! /bin/bash
    sysctl vm.disk_based_swap=1
    fallocate -l 2G /var/swapfile
    chmod 600 /var/swapfile
    mkswap /var/swapfile
    swapon /var/swapfile
    chmod 777 /var/lib/mysql

After the VM is created, head to your VM's external URL (port 3000) and you should be greeted with the default Huginn login page!

![](/uploads/huginn_default_login.png)

I suggest [reserving a static external IP](https://cloud.google.com/compute/docs/ip-addresses/reserve-static-external-ip-address "Static IP on GCP") for this VM, so the IP doesn't change. You can even take it a step further and associate this to a domain name - like automation.colinarms.com - for ease of access.

Now, let's dive into Huginn.

## How Huginn Works: Agents & Events

At a high level, Huginn relies on two key things: agents and events. Agents are things that monitor for you and create events (possibly if some criteria is met). An example agent is an `RssAgent`, which monitors an RSS feed for new articles. Events created by the `RssAgent` can be passed to a `TriggerAgent`, which uses some regex filter to only listen to keywords of interest; and finally it emits a formatted message, perhaps to a `SlackAgent`, that finally sends a message to a Slack channel.

You can imagine how this works in practice. For the flight deals usecase, for example: we can create an `RssAgent` for the [Secret Flying RSS feed](https://www.secretflying.com/feed/). The `TriggerAgent` can listen to these events, filter for "San Francisco Airport", and the `SlackAgent` can message my `#flights` channel when this happens

Multiple agents for a single usecase can be grouped into a `Scenario` - in the above example, a `Flight Deal Scenario` would make sense.

Let's walk through this example. If you want to get started immediately, you can [download my agents](https://gist.github.com/seeARMS/103b3399f3f925fb6c366600f0bad3c6) and import them into Huginn directly.

## Creating your First Agent

### 1) Monitoring the RSS feed

On Huginn, create a new RSS Agent. Configure the following params:

* **Name** your agent something descriptive. I used "Secret Flying RSS Agent".
* **Schedule** your agent for however frequently you'd like it to check for updates. I used 30 mins.
* **Keep events** for some period of time. I used 7 days.

Use the following JSON in the options:

    {
      "expected_update_period_in_days": "2",
      "clean": "true",
      "url": "https://www.secretflying.com/feed/"
    }

Expected update period is the period at which Huginn should expect the agent to be updated - if it doesn't happen, the agent is considered not working.

Save your agent, and give it a manual run - you should see events populate from the underlying feed.

### 2) Filtering for nearby airports

Now, create a `TriggerAgent`. We want to filter newly posted articles for only nearby airports - in my case, San Francisco or San Jose airport.

Fill it out similar to the first agent. But, this time select your RSS Agent as this agent's `source`. Events from the RSS Agent will be fed into this.

Use the following JSON in the options:

    {
      "expected_receive_period_in_days": "2",
      "keep_event": "false",
      "rules": [
        {
          "type": "regex",
          "value": "(sfo|san francisco|sjc|san jose)",
          "path": "title"
        }
      ],
      "message": "[Secret Flying] <a href=\"{{url}}\">{{title}}<\/a> {{description}}"
    }

We're filtering the RSS feed's title to contain my nearby airports, and emitting a `message` with the URL, title and a description.

### 3) Notifying on Slack

Lastly, create a `SlackAgent`. After [registering a new Slack workspace](https://slack.com/get-started#/) and [creating a new Slack webhook](https://my.slack.com/services/new/incoming-webhook), set the `source` to the Trigger Agent you just created.

Put the following JSON in the options:

    {
      "webhook_url": "https://hooks.slack.com/services/your-webhook-url",
      "channel": "#flights",
      "username": "Huginn",
      "message": "{{message}}",
      "icon": ""
    }

Save your agent, and eventually you should begin receiving flight deals!

{{< figure
width="400px"
src="/uploads/slack_secret_flying.png" alt="Secret Flying" >}}

## Wrapping it up

This example describes a single usecase for what's possible with Huginn, but we're just scratching the surface. If you're interested in the other usecases I described above, feel free to [download and import them](https://gist.github.com/seeARMS/103b3399f3f925fb6c366600f0bad3c6) into your own Huginn installation.