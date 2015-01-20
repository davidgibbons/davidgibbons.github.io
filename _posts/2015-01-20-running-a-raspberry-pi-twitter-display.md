---
layout: post
category : code
tagline: "Building a raspberry pi twitter display"
tags : [ raspberrypi, twitter, python ]
---
{% include JB/setup %}

# Building a Raspberry Pi based Twitter Display

## History
One of the first projects I found when I first started playing around with the raspberry pi was a two way Portal mirror. This used a simple display + camera to show a remote location of another "paired" device. This got me thinking about other small frame-based uses for a micro-computer. I realized that there were a few projects for turning a raspberry pi into a picture frame, and I wanted to do something similiar.


## Research
The first challenge I had was exploring solutions for displaying text cleanly at potentially low resolution on a raspberry pi. I had previously explored running an X session and chrome browser for a dashboard I built for work. This had some flaws, specifically the results often ran out of memory, it took a lot of heavy processes and a window manager for what really didn't require it. I started to explore instead ways to use the framebuffer without a lot of heavy graphics work. From there I found the Pygame library and some tutorials that showed easy usage of the framebuffer for displaying text. Perfect!


## Design
### Graphics
The end result is a gutted Pygame class that instantiates a empty rectagle based on the framebuffer size. We then grab some text to render, word wrap it as appropriate and display on the screen.

### Network
I ended up wanting to make the program show it's ip address on startup so we could address the device if there was any problms. At first I wrote a function that would grab specifically the ip address of eth0. When I expanded the device to use a wifi adapter this solution fell apart. I found another solution that instead tells the code to use the networking stack to connect to a public address and then grabs the local results of that socket connection. This appears to more dynamically grab the RIGHT adapter.

### Twitter
I explored the python-twitter module. This was really straight forward to do searches and parse the results. There are *so many* options for twitter libraries, I can't say this one is the best but it worked well for my needs. I think when this fails is where my code is missing the most exception handling. If I look up a twitter user and it doesn't exist, I'm not catching and handling that situation yet.

## Results
The end result is this small display that sits on my desk, every 30 seconds it displays a new 'tweet', altering the color and source from a yaml list thats pulled in on load. I've attempted to have it often re-read the yaml file but I don't know that it's working. Auto-reloading the configuration file seemed like a easy way to allow dynamic updates. I also considered polling from a twitter account, but that doesn't work for topics only for users to follow. I could possibly change it to search for the results of a tweet as if it was a topic, and the users followed by an account for users to follow, although removing topics would not be straightforward. Dynamic configuration data seems to be the biggest challenge with the display. I also considered setting up a small torando server that you could post an api request to for configuration changes. But I don't think thats actually easier than the current method where I can do a git pull.