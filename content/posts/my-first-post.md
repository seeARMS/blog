---
title: 'Next-level automation with Huginn: Self-hosted & free'
date: 2020-02-21T03:39:52.000+00:00
categories:
- tech
- automation
tags:
- automation
- tech

---
![](/uploads/ok.jpg)

I've always enjoyed automating as much as I can. I use Home Assistant to automate my smarthome devices.  

I recently came across a [thread on Hacker News](https://news.ycombinator.com/item?id=21772610 "Huginn") discussing a supercharged version of IFTTT: Huginn. This open-source software performs automated tasks by watching for 'events', and triggering 'actions' based on these events. Huginn has the following benefits;

* Open-source & self-hosted: no concerns

## New Beginning

This is a new beginning on my blog on hugo and this seems pretty cool so im adding random text here because I dont know **what** to add here. So im adding a lot more random text here.

This is another test.

## Code

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
var="ruan"
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