---
title: 'Count files by type and directory'
date: 2020-12-17
tags: ['command line', 'grep', 'awk']
author: 'Colin'
summary: 'Using the command line to count stuff'
draft: true
---

I have been working on updating a 15 year old data collection / ETL process in the last weeks. As part of the planning process, I wanted to know how many people are using the data we collect[^1]. Sure, I could _ask_ people, but how does that help me sharpen my skills a Data Wizard[^2].

At my work, our shared server is organized by analyst (i.e. each of the subfolders to the team's main directory is the name of an employee). Since our data is stored in a SQL database and you need to write a query to get the data, I can get a quick and dirty look at who is using this data and how often. I can simply look in everybody's folder for `.sql` files that contain a distinct word or phrase that will identify my database.

To do this efficiently at the command line, I used a couple of built in bash functions (`awk`, `sort` and `uniq`) and the speedy version of another: `ripgrep` (`grep`). They aren't exactly faster versions as they don't mimic the behavior of the built-ins, but they accomplish many of the same tasks. Most importantly for me, it means I can do this task without having to wait around for a day for my code to run.

The basic procedure is the following:

1. Search through each directory for `.sql` files.
2. Check whether those files contain the pattern I am looking for.
3. If so, add the file path to my list.
4. (optional) Save the list to a file.
5. Split the file paths to get just the names of people who have used the data and saved their query.
6. Sort and count the number of files. 

I'm going to do this in two lines which could be chained together using `xargs` (also left to the reader) or made into a function. I may consider the latter if it turns out to be something useful down the line as well. The first command I used makes a list of all the files I am interested in and save it as the text file `MyQueries.txt`:

```
rg 'dbo.CoolTable' -g '!BadDirectory/*' -g '*.sql' -l -i > /Path/to/MyQueries.txt
```

Here's what each part of that command does:

* `rg 'dbo.CoolTable'`: uses ripgrep to search for `dbo.CoolTable`. The pattern you give is a regular expression (unless you speccify otherwise with the `-F` or `--fixed-strings` flag), so you can as fancy as you want with your regex.
* `-g '!BadDirectory/*'`: this is the glob command which allows you to tell ripgrep what directories to look in or avoid in its search. The `!` in front of the directory name tells ripgrep to exclude that directory. Glob flags can be used more than once (which brings us to our next piece):
* `-g '*.sql'`: another glob flag to search only for SQL files. This can also be specified with the `--type` (`-t`) flag and the file type you are interested in (`sql` in my case). I went this route because the latter flag wasn't working for me.
* `-l -i`: `-l` (`--files-with-matches`) returns the file path where at least one match was found and `-i` (`--ignore-case`) makes your pattern search case insensitive. Since SQL is case-insensitive, using this means I'll get files where people use `dbo.cooltable`, `DBO.COOLTABLE` and everything in between.
* `> /Path/to/MyQueries.txt`: finally I redirect the output from ripgrep to the textfile which I will analyze with my next command.

The second command takes the list of files I found, parses it for my colleagues' names and counts how many files there are in each of their directories:

```
awk -F "/" '{print $1}' /Path/to/MyQueries.txt | sort | uniq -c > query-count.txt
```

Awk is actually its [very own programming language](https://en.wikipedia.org/wiki/AWK) (!) that is mostly used for extracting data from text. There is a whole lot to it, so if you are interested, go spend the day [on the documentation](https://www.gnu.org/software/gawk/manual/gawk.html). All I'm going to do for you here is explain my little command :). Again, bit by bit:

* `awk -F "/"`: calls awk and specifies a delimiter to use to parse your data. Since I am dealing with file paths, I use the forward (or is it backward?) slash.
* `'{print $1}'` tells awk to return the first element of my parsed line. Since the file paths I have in my file go something like `NameOfColleague/subdir/*/sql_file.sql` with the name of my coworker always in the first position, this gives me one instance of each person's name for each query they have written to my database.
* `/Path/to/MyQueries.txt`: tells `awk` what file to operate on.
* `| sort | uniq -c`: finally, the resulting list of names is sorted and counted! What I really want only the number of times each name appears in my list, so you would think `sort` wouldn't be necessary. However, `uniq` only detects repeats in *adjacent* lines, so the list has to be sorted with the `sort` command before passing it to `uniq`. `-c` tells `uniq` to return a count of unique occurences it finds.

And there you have it! A long explanation of a short series of commands that will give you the number of files that contain a text snippet of interest, grouped by directory.


[^1]: I also want to know which features folks are using which I may also try to do at the command line, but [that exercise is currently left to the reader](https://www.reddit.com/r/physicsmemes/comments/8wil0z/the_meme_is_left_as_an_exercise_for_the_reader/).
[^2]: I am just kidding here in case you were wondering. Usually talking to people gets us to the answer to a question like this much more quickly and effectively than any silly computer wizardry.