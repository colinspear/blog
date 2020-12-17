---
title: 'Local job scheduling'
date: 2020-07-21
tags: ['command line', 'shell']
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

In my case, I want my bot to post often enough that there is regular new content, but without running out of samples too quickly. For this project I'm using a dataset of applications for [California Vanity license plates](https://github.com/veltman/ca-license-plates). I'm only interested in rejected plates, so once I drop the others and do a bit more clean up, I'm left with **NUMBER** remaining samples. At this rate, if I were to post twice a day, it would take **NUMBER** to run out of samples. That seems sufficiently long, so twice a day it is. Here's the line in my crontab that does just that[^1]:

```
30 9,21 * * * /path/to/program /path/to/script
```

The above command says, "run `script` using `program` at each 9:30 am (minute 30 of hour 9) and 9:30 pm (minute 30 of hour 21)." This demonstrates how you can uses commas to specify more than one instance of the period you are referring to. Other special characters are `*/` which will run your command at a specific interval (`/*5 * * * *` will run your command every 5 minutes), `-` which will run your command at each point between the numbers on either side of the `-` (`* * * * 1-5` will run your command once a day, Monday through Friday). Have a look at [this link](https://ostechnix.com/a-beginners-guide-to-cron-jobs/) for more shortcuts when scheduling jobs. Also, be sure to save and close your crontab before you start freaking out about your job not running even though every. detail. is perfect.

Annoyingly, crontab sends default system emails after each cron job is executed. To disable this feature, simply add `2>&1` to the end of the job's line in your crontab. It can also be nice to save a log of anything that goes wrong with your job. It can also help if you need to debug your job because it's not doing what you want. To do this, simply redirect the output of the command to a log file. Making these two changes, the above line in my crontab becomes:

```
30 9,21 * * * /path/to/program /path/to/script > /path/to/logfile.log 2>&1
```

Now I have a program that runs twice a day, prints any error that comes up to a log file and does not spam me with a bunch of email I don't want. Yeeha. Unfortunately, this really isn't great if you ever turn your computer off.


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
  
[^1]: If, like me, you are running a python program and want to use the python version you have in your virtual environment, be sure your `/path/to/program` points to that python version. For me (I run a Macbook) that path takes the shape `/Users/user_name/.virtualenvs/env_name/bin/python`. 
