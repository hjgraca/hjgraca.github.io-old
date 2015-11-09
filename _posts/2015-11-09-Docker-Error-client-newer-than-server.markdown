---
layout:     post
title:      "Fix Docker Error: client is newer than server"
subtitle:   "After upgrade to version 1.9"
date:       2015-11-09 22:16:00
author:     "Henrique Graca"
header-img: "img/post-bg-02.jpg"
description: "Docker Error: client is newer than server"
---

## Error ##

After updating Docker through docker toolbox to version 1.9, when trying to run I was getting the following error:

``Error response from daemon: client is newer than server (client API version: 1.21, server API version: 1.20)``

## Fix ##

Run the following command to get the list of machines, in my case I have only one with the name "default"

``docker-machine ls``

Then run docker-machine upgrade with the name of your machine(s), in my case

``docker-machine upgrade default``

And that's it, hope it helps
