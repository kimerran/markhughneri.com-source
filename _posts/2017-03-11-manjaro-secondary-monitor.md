---
layout: post
title: Manjaro Linux Secondary Monitor Issue (Fixed)
---

## Switching to Manjaro Linux
After some time of playing around with Manjaro Linux on Virtual Machine (and some other distros), I finally switched to using this as my main operating system on my desktop.

## Some issue
Though most of my development tools run perfectly on Manjaro Linux, I have one issue - the secondary monitor's resolution only has `1024x768`. 

## Fixing the issue
While doing my research to fix the issue, I came into this [site](https://bbs.archlinux.org/viewtopic.php?id=73738) and followed the steps but unfortunately it doesn't fixed my issue.

I tried to add `1920x1080 @ 60Hz` but nothing would show on my secondary monitory. I've actually tried installing the XFCE version of Manjaro if it somehow fix the issue but it does not.

Somehow it came to me to try **59Hz** instead of **60Hz** - and well, it worked!

Here's the commands I used to fix my problem:

```
xrandr --newmode "1920x1080_59.00"  169.77  1920 2040 2248 2576  1080 1081 1084 1117  -HSync +Vsync

xrandr --addmode VGA1 1920x1080_59.00

xrandr --output VGA1 --mode 1920x1080_59.00
```

# 1920x1080 at last!
Once you've added the new mode to the correct monitor, it will show up to the resolution options. The third command just sets it up.
![](/public/img/manjaro-second-monitor.jpg)