---
layout:     post
title:      "Continuous integration and deployment with GitHub, Azure Web Apps and TeamCity"
subtitle:   ""
date:       2015-04-10 11:52:00
author:     "Henrique Graca"
header-img: "img/post-bg-06.jpg"
---
In this blog post I'll share my experience with implementing a continuous integration/deployment system using Git, GitHub, TeamCity and Azure Websites.

## What we are going to do ##

1. Create a new Windows Azure Web App
2. Enable Git publishing on the Web App 
3. Create a repository on GitHub to store your code
4. Create a brand new ASP.NET MVC 5 Web Site with a Unit Test project
5. Push my web site to the GitHub repository
6. Configure TeamCity to pull any changes from my GitHub repository, build the code, run the Unit Tests and push the Web Site on to Azure App local Git repository

I will **NOT** set up GitHub integration for the Web Site. TeamCity will monitor changes on GitHub repository and pull that code.

I assume you have a working TeamCity version either locally or remotely. If not there are many tutorials that could help you accomplish that. Here is [confluence official documentation](https://confluence.jetbrains.com/display/TCD9/Installing+and+Configuring+the+TeamCity+Server "Installing and Configuring the TeamCity Server") 

## Step1: Create a new Azure Web App ##

Nothing could be more easy than creating your very own website in Azure. Navigate to your [Azure Portal](http://portal.azure.com "Azure Portal") account Simply click `New -> Web + Mobile -> WebApp`. A new blade will appear on the right, enter your unique website Url, choose pricing and location, click Create and wait for your new website to go live, after it's finished you can navigate to your Web App Url, that easy.

![Add Web App](https://www.dropbox.com/s/1in2tpdrsq97ejg/Screenshot%202015-04-10%2011.40.46.png?dl=0) 
