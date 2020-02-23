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

For example, if there's a sudden spike in discussion on Twitter with the terms "San Francisco Earthquake", Huginn can send a text to my phone. Or, if a phenomenal flight deal is posted on one of the many deal-finding websites out there, Huginn can send me an email with the price and a link to Google Flights to buy.

Huginn seemed to be a pretty powerful automation tool, but I wanted to take this a step further and introduce some organization - I wanted all notifications to be cataloged & delivered in a centralized way. A personal Slack workspace seemed like the perfect solution for this - I can have a `#flights` channel for flight deals, or a `#trending` channel for the, er, pending San Francisco emergencies.

Another hard requirement I had was that I wanted all of this to be free. Huginn is self-hosted, and I quickly realized had pretty lax runtime resource requirements, so a GCP micro tier instance (1 instance free per month) was perfect for this.

## Getting Started

This is python code:

    from random import randint
    from faker import Fake
    randint(1, 2)
    
    destFile = "largedataset-" + timestart + ".txt"
    file_object = open(destFile,"a")
    file_object.write("uuid" + "," + "username" + "," + "name" + "," + "country" + "\n")
    
    def create_names(fake):
        for x in range(numberRuns):
            genUname = fake.slug()
            genName =  fake.first_name()
            genCountry = fake.country()
    file_object.write(genUname + "," + genName + "," + genCountry + "\n")
    ..

This is bash code:

\#!/usr/bin/env bash
var=""
echo "Hello, ${var}"

## Tweets

This is one of my tweets, see [configuration](https://gohugo.io/content-management/shortcodes/#highlight) for more shortcodes:

## Tables

This is a table:

| id | name | surname | age | city |
| --- | --- | --- | --- | --- |
| 20-1232091 | ruan | bekker | 32 | cape town |
| 20-2531020 | stefan | bester | 32 | kroonstad |
| 20-4835056 | michael | le roux | 35 | port elizabeth |

## Lists

This is a list:

* one
* two
* [three](https://example.com)

This is another list:

1. one
2. two
3. [three](https://example.com)

## Images

This is an embedded photo:

![](https://images.pexels.com/photos/248797/pexels-photo-248797.jpeg?auto=compress&cs=tinysrgb&dpr=1&w=500)