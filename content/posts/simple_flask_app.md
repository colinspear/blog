---
title: "Flask wordcount app"
date: 2021-03-11
draft: false
tags: ['flask', 'web-dev']
disqus: false
---

I made a simple flask app that takes a URL as input and returns a table and chart of the most used words on the site. 

Built with Flask using a Postgres backend. Uses beautiful soup to scrape and process html from given website. Implements a Redis task queue to handle requests. Uses Angular to poll the back end for completion and to display a word frequency chart using JavaScript and D3. Hosted on Heroku.

The finished product looks like this:

{{< figure src="/images/wordcount_app.png" >}}

You can see the app in action [here]()

Based on [this tutorial](https://realpython.com/flask-by-example-part-1-project-setup/).