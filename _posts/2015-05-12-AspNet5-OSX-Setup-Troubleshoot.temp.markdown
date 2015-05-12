---
layout:     post
title:      "ASP.NET 5 OSX Part 1"
subtitle:   "Setup and Troubleshoot"
date:       2015-05-12 17:52:00
author:     "Henrique Graca"
header-img: "img/post-bg-06.jpg"
description: "ASP.NET 5 OSX Part 1: Setup and Troubleshoot"
---
In this blog post I'll share my experience with implementing a continuous integration/deployment system using Git, GitHub, TeamCity, ASP.Net MVC 5 and Azure Web Apps.

## What we are going to do ##

http://andreizubov.blogspot.ru/2015/04/getting-started-with-net-on-os-x-and.html

http://stackoverflow.com/questions/29971826/running-first-asp-net-5-application-using-vscode-dnx-and-kestrel-results-in-ioe

run yo as sudo

restoring
sudo dnu restore

#if dnu not found
	source dnvm.sh

Bower problems
Error: EACCES, permission denied '/Users/henrique/.config/configstore/bower-github.yml'
You don't have access to this file.

sudo chown -R henrique ~/.config
sudo chown -R henrique ~/.cache