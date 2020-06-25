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

I wanted to look at a given timespan, and compare memory usage on average with the top 1 percentile of memory usage. With Cloud Profiler, this can be done by setting a timespan, end time, and comparison criteria.

![](/uploads/profiling.png)

The comparison criteria I chose was 'all weight' (the full memory usage range - from lowest to highest usage - over the timespan), compared to just the top 1 percentile of memory usage.

This brought up a much less colourful but equally overwhelming graph:

![](/uploads/memory_usage_comparison.png)

Hovering over the various method calls shows you the compared memory usage - comparing the full range of weights to only the top 1 percentile. Neat!

You can also view the difference in memory consumption per-method-call. This lets you sort by, for example, all functions that were consuming drastically more memory in your comparison weight.

![](/uploads/method_call.png)

Something looked off - there were a small handful of functions, getting called very frequently (>100 times during the timespan I was looking at), using 16MB memory (drastically more than all other functions). Let's dig a bit deeper:

 