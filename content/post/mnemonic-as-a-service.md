+++
title = "Mnemonic as a Service"
summary = "Be careful choosing a wordlist for your server names.  Not all are created equal."

# Optional image to display on homepage (relative to `static/img/` folder).
image_preview = "scaled/unicorn.jpg"

date = 2014-09-06
lastmod = 2018-01-13
draft = false

tags = ["programming", "devops"]

[header]
image = "headers/header-unicorn.jpg"
+++


# The mnemonic wordlist is fascinating

Naming servers is fun.  At my very first job, we used the names of
deceased family members.  I've seen simpsons characters, Greek and
Indian gods, and long unpronounceable drug names (BAD IDEA). 

I mainly deploy in the cloud now, so I hadn't really thought much about naming things for several years.  At least until I found this fantastic [guide](http://mnx.io/blog/a-proper-server-naming-scheme/) from the nice folks at mnx.io.  They've really put a lot of thought into this.

One of the bits of insight is to use a high quality wordlist and they suggest:

> the specific word list we recommend comes from Oren Tirosh’s [mnemonic encoding project](http://web.archive.org/web/20090918202746/http://tothink.com/mnemonic/wordlist.html). These 1633 words were chosen very specifically to be short (4-7 letters), phonetically different from one another, easy to understand over the phone, and also recognizable internationally. The mnemonic word list should be much less prone to typos and transposed characters when compared to more structural names. A lot of time and research went into these words, and their properties make them ideal for our purpose.
>
> -- [mnx.io](http://mnx.io/blog/a-proper-server-naming-scheme/)

Genius!

I'd never heard of that wordlist before and immediately wrote a quick and dirty [shell script](https://gist.github.com/alexlovelltroy/119c32a12f6aca28c3f3) to give me a few of them at a time.  That led to a [flask application](https://github.com/alexlovelltroy/mnemonic-as-a-service), and a [django application](https://github.com/alexlovelltroy/django-mnemonic), and finally, [mnemonic-as-a-service.com](http://mnemonic-as-a-service.com).

Use them in good health to name your next release, your next project codename, or even your next offspring.


