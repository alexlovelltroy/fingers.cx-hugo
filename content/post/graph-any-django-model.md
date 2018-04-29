+++
title = "Graph Any Django Model"
summary = " Using [_flotcharts_](http://www.flotcharts.org) and a simple gist I can graph all my django models in a DRY way.  You can"

# Optional image to display on homepage (relative to `static/img/` folder).
image_preview = "scaled/mountain.jpg"

date = 2013-06-26
lastmod = 2018-01-13
draft = false

tags = ["programming", "django", "python", "metrics"]

[header]
image = "headers/header-mountain.jpg"
+++

In my django applications, I often find myself wanting to graph events per day.
Take signups using the standard contrib.auth User model for example.  It's
helpful to know how many people signed up each day last month and the month
before.  A graph is a quick way to get an impression of how my application is
doing.

![An example graph image](/static/images/UserGraph.png)

I wrote a combination of python and javascript to make these graphs with [_flotcharts_](http://www.flotcharts.org) for _any_ django model.


Here's the gist:

<script src="https://gist.github.com/alexlovelltroy/5993250.js"></script>

