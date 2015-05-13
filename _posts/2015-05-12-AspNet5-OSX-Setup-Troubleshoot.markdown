---
layout:     post
title:      "ASP.NET 5 OSX Part 1"
subtitle:   "Setup and Troubleshoot"
date:       2015-05-12 17:52:00
author:     "Henrique Graca"
header-img: "img/post-bg-04.jpg"
description: "ASP.NET 5 on OSX - Part 1: Install and Troubleshoot"
---
In this blog post I'll share my experience with installing, running and troubleshooting Asp.Net 5 on OSX. 

## Pre-Requisites and versions ##

* Node and Npm installed (if not installed just follow this [Link](http://blog.teamtreehouse.com/install-node-js-npm-mac "Install npm"))

* Visual Studio Code ([download](https://code.visualstudio.com "Download Visual Studio Code"))

* .NET Version Manager - Version 1.0.0-beta5-10376

* dnx/dnu - Version 1.0.0-beta4-11566

## Installing ASP.NET 5 and DNX ##

Follow the [tutorial](https://github.com/aspnet/home#os-x "Setup Asp.Net OSX") on Asp.Net Github

Make sure you have Homebrew installed.

Then run the following commands:

``brew tap aspnet/dnx``
``brew update``
``brew install dnvm``

## Install yeoman ##

Install yeoman to scaffold a new Asp.Net 5 application

`npm install -g yo grunt-cli generator-aspnet bower`

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://code.visualstudio.com/Content/images/YoASPNET.png" alt="yeoman" class="image" style="width: 100%;"> 
</div>
</div>

Follow the instructions provided by the generator, and go to the folder of your new app.

Run `sudo dnu restore` to download and install the necessary nuget packages.

### If dnu not found ###

Run the following command:

``source dnvm.sh``

### Problems with Bower! ###

Error: EACCES, permission denied '/Users/{your name}/.config/configstore/bower-github.yml'
You don't have access to this file.

You need to add permissions to the following folders:

``sudo chown -R {your name} ~/.config``
``sudo chown -R {your name} ~/.cache``


### Problems with Mono! ###

If you have Mono link problems, run the folowing commands:

Error: Could not symlink include/mono-2.0
/usr/local/include is not writable.

``sudo chown -R {your name} /usr/local``
``brew link --override mono``

## dnvm version ##

Run ``dnvm list`` and check if you have an asterisk on one of the options that appear

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="http://docs.asp.net/en/latest/_images/dnvm-list-beta4.png" alt="dnvm" class="image" style="width: 100%;"> 
</div>
</div>

if no asterisk appears, run the command: ``dnvm use default``

## Running your first Asp.Net 5 app in Osx ##

Now when you have all the packages restored, the application server can be started. Linux and OS X use Kestrel server to host and run Asp.net applications. Start it with kestrel command:

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://code.visualstudio.com/Content/images/DNXCommandPalette.png" alt="kestrel" class="image" style="width: 100%;"> 
</div>
</div>

if the command does not start the server from Visual Studio Code, you can execute the command from your app folder to run manually

``dnx . kestrel``

Open you favorite browser and browse to ``http://localhost:5001`` to browse the site we created.

## IOException: kqueue() FileSystemWatcher ##

if you get this error when loading the site, it is a bug in mono. Asp.net uses mono behind the scenes while .net core is not fully ported to OSX.

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropboxusercontent.com/s/lgepkpjkwxutphh/Screen%20Shot%202015-04-30%20at%2022.53.57.png?dl=0" alt="IOException" class="image" style="width: 100%;"> 
</div>
</div>

To fix it you need to set the environment variable:

``export MONO_MANAGED_WATCHER=false``

## It works! ##

If someone told me 2 years ago that I would be running Asp.net/c# code in OSX I would not believe it.

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropboxusercontent.com/s/6l2xkule80m5hvn/Screen%20Shot%202015-04-30%20at%2023.52.44.png?dl=0" alt="It works" class="image" style="width: 100%;"> 
</div>
</div>

This is an amazing time to be a .Net developer, the team behind Asp.net is doing a great work porting all the .net core framework to Linux and OSX.

In a couple of months I believe we could run full .net core apps in unix devices without the help of mono.

And Visual Studio Code is just amazing! There is also an amazing [Asp.Net 5 tutorial](https://code.visualstudio.com/Docs/ASPnet5 "Asp.net 5 Tutorial") on the Visual Studio Code page.

