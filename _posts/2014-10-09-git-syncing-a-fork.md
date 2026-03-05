---
layout: post
title: "Git : Syncing a fork"
date: 2014-10-09 23:32:00 +0000
tags: ["Git", "GitHub", "sync"]
blogger_orig_link: https://lexxai.blogspot.com/2014/10/git-syncing-fork.html
---

Я долучився до певного проекту на GitHub,  створив свій клон (fork).  
Для синхронізації з головним проектом потрібно виконати це:  
  
[Sync a fork of a repository to keep it up-to-date with the upstream repository.](https://help.github.com/articles/syncing-a-fork/)  
  
git merge upstream/master  
git submodule update
