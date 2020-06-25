+++
categories = ["tech"]
date = ""
description = ""
draft = true
tags = ["gcp", "tech"]
title = "Diagnosing node.js memory leaks with Google Cloud Profiler"

+++
I have a node.js app bundled in a Docker container and deployed on Google Cloud Run. Recently, the container has been crashing with the following error:

`FATAL ERROR: Ineffective mark-compacts near heap limit Allocation failed - JavaScript heap out of memory`

Oh no. Looking at the memory utilization graph confirmed my suspicious of a memory leak:

![](/uploads/memory_usage_increasing.png)

The OOM's always happened at the same time a request comes in.

## Google Cloud Profiler

Luckily, I set up [Google Cloud Profiler](https://cloud.google.com/profiler "Google Cloud Profiler") several weeks back, so I was armed with a pretty powerful tool for diagnosing memory issues in production.

Firing up Cloud Profiler presents you with a pretty colourful (and, overwhelming) UI:

![](/uploads/screen-shot-2020-06-24-at-9-24-32-pm.png)