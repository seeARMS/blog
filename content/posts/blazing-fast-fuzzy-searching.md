+++
categories = []
date = ""
description = ""
draft = true
tags = ["vim", "tech"]
title = "Lightning-fast fuzzy searching with fzf and ag"

+++
`fzf` and `ag` are two commands I use every single day for lighting-fast searching through files.

My code workflow commonly looks like this: 

* Multiple tmux panes, all in a single directory of the project I'm currently working on
* 

## Setting up ag

Using .gitignore (don't search node_modules)

## Setting up fzf

vim ** <tab>

Control T

:Files in vim

Combining with ag

\[ -f \~/.fzf.zsh \] && source \~/.fzf.zsh

\# See: [https://github.com/junegunn/fzf/issues/1625](https://github.com/junegunn/fzf/issues/1625 "https://github.com/junegunn/fzf/issues/1625")

export FZF_DEFAULT_COMMAND='ag -p \~/.gitignore -g ""'

export FZF_CTRL_T_COMMAND="$FZF_DEFAULT_COMMAND"

_fzf_compgen_path() {

  ag -p \~/.gitignore -g . "$1"

## One command to fuzzy-search in files and open in vim

`agv() { vim $(ag -l "$1") }`

![](/uploads/screen-shot-2020-06-08-at-7-29-55-pm.png)