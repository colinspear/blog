---
title: 'Local job scheduling'
date: 2020-07-21
tags: ['scheduling', 'shell']
author: 'Colin'
summary: "In which Colin learns to schedule a task"
draft: true
---

Learning to schedule a task has been on my list of to-learns for some time. Some time ago, I started collecting data on my Spotify Discover Weekly playlist through [Spotify's great API](https://developer.spotify.com/documentation/web-api/). If you're not familiar, Discover Weekly is a personalize playlist that Spotify refreshes every week. As far as I know there is no way of seeing previous week's playlists, so I decided I would keep a record for myself. I collect most of the song features for the songs in my playlist, of which there are many. For a few months I have just be manually running the script I wrote to collect this data, but as a human, I sometimes forget and as a programmer I want to solve my human fallibility through code wherever possible. Hence, automating the data collection.

## Cron

Chances are you have heard of a cron job. Cron is a small utility that has been around for nearly fifty years and can be used on any Linux or Unix-like (say MacOS) operating system. It can be used to run many different processes, is very simple to set up a job once you get the hang of it and is great for scheduling repetitive tasks... if you always leave your computer on. We'll get back to that last point.

So, let's set up a job:

```
crontab -e
```

This will open up your crontab file which will be where all your jobs live. If you haven't scheduled a job in the past, this will likely be empty at this point. Basic job formatting is as follows:

```
 ┌───────────── minute (0 - 59)
 │ ┌───────────── hour (0 - 23)
 │ │ ┌───────────── day of the month (1 - 31)
 │ │ │ ┌───────────── month (1 - 12)
 │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday;
 │ │ │ │ │                                   7 is also Sunday on some systems)
 │ │ │ │ │
 │ │ │ │ │
 * * * * * <command to execute>
```

- explain above
- Quirks
    - Save an quit or else might not work
- Make log file
- turn off emails
- run at save / close

- Okay, that was great. Unfortunately, crontab doesn\'t have a lot of flexibility. 
- List common use cases where it breaks down

- There are a number of other tools available to fill in the gaps: list
- Turns out since $YEAR cron uses launchd to run processes anyway 

Launchd
- Good [overview post](https://beepb00p.xyz/scheduler.html) on scheduling options (say something about cloud options)
- Really a pretty annoying tool
- Create plist (xml) file
- use launchctl from command line to execute
- Some GUI solutions, [web app](launched.zerowidth.com) - doesn\'t seem to be a useful command line tool that will generate a plist file for you. I used the zerowidth web app
- [launchd.info](launchd.info) has everything you need to write your plist files and schedule your jobs
- Definitely a bit of a pain, but not complicated once you get the hang
  
