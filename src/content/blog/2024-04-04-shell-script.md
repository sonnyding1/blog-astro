---
title: Shell script
pubDatetime: 2024-04-04
postSlug: shell-script
featured: false
draft: true
tags:
  - notes
description: Some notes on writing shell scripts
---
This post serves as a memo for myself in case I need to re-learn writing shell scripts in the future. The note content strictly follows \[this tutorial\]([https://www.shellscript.sh/](https://www.shellscript.sh/)) by Steve Parker.  
  
## First script

```sh
#!/bin/sh
echo Hello World
```

First line indicates that no matter if you are in zsh or csh or anything else, you should run this script using Bourns shell.

Second line calls `echo` with 2 arguments, therefore spacing between `Hello` and `World` in this case won't make any difference to the output of the script.