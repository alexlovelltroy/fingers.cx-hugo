+++
title = "Building My Homelab with apu4d4 machines"
summary = "I like to tinker with lots of physical nics.  The apu4d4 boards are cheap and excellent for tinkering"

date = 2020-03-15
lastmod = 2020-03-15
draft = false

tags = ["homelab", "hardware"]

[header]
image = "headers/header-martenitsa.jpg"
caption = "[**Bulgarian Martenitsas**](https://en.wikipedia.org/wiki/Martenitsa/)"
+++

My humble home lab is a testbed for my software projects that can't easily fit in the cloud.  That means messy hardware interaction and low level networking.  I also dabble with network management software projects.  Rather than a rack full of loud, secondhand Dells and HPE machines, I tend to go for more modest SoC options.  While the raspberry pi has a place in my lab, the workhorse is the [apu4d4 board from PC Engines](https://pcengines.ch/apu4d4.htm).  With four physical network devices that can all operate at gigabit speeds, and support for mSata/miniPCI devices, all on a SoC system that supports a real DB9 Serial console, the hardware itself is well suited to experimentation.  The coreboot bios makes it even more friendly for embedded developers with support for "watchdog" which reboots the machine in case of OS failure. 

I've got four of these machines at the moment

These little boards have lots of features to make them excellent as network appliances, but are cheap enough to use for lots of other things as well.  
The combination of four gigabit ethernet devices, a real DB9 Serial port, and coreboot with watchdog support makes them  
They are powerful enough to run a set of small VMs or docker containers for network services.  
With four physical ethernet devices, they can handle a slew of network tasks and have en

Since I first built a firewall out of a [Soekris Box](http://soekris.eu/shop/net5501_en/) to separate DMZ and home networks, I've loved working with physical ethernet ports in my homelab.  Unfortunately for me, Soekris USA shut down in 2017 and the European site hasn't been updated since 2005.  I shifted to the [PC Engines APU4d4 boards](https://pcengines.ch/apu4d4.htm) in 2018 and have found them to be really exciting pieces of hardware. 
