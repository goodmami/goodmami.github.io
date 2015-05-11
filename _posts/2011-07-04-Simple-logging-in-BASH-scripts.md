---
layout: post
title: Simple logging in BASH scripts
redirect_from:
 - /2011/07/simple-logging-in-bash-scripts/
---

A set of basic functions for writing to logs in BASH.

{% gist 6556701 %}

Commenter Jul added:

> Very useful, thanks!
> You also could add timestamp at the beginning of each logged line :
>
>     dte=`date +’%Y%m%d %H:%M:%S’`
>     echo -e “$dte $2″| …
