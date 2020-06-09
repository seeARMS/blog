+++
categories = []
date = ""
description = ""
draft = true
tags = ["vim", "tech"]
title = "Lightning-fast fuzzy searching with fzf and ag"

+++
`fzf` and `ag` are two commands I use every single day.

My code workflow commonly looks like this: 

* Multiple tmux panes, all in a single directory of the project I'm currently working on
* 

## Setting up ag

Using .gitignore (don't search node_modules)

## Setting up fzf

vim ** <tab>

Control T

:Files in vim

## One command to fuzzy-search in files and open in vim

`agv() { vim $(ag -l "$1") }`

![](/uploads/screen-shot-2020-06-08-at-7-29-55-pm.png)