---
layout:     post
title:      "ASP.Net MVC 5 Continuous integration and deployment with GitHub, Azure Web Apps and TeamCity"
subtitle:   ""
date:       2015-04-10 11:52:00
author:     "Henrique Graca"
header-img: "img/post-bg-06.jpg"
description: "ASP.Net MVC 5 Continuous integration and deployment with GitHub, Azure Web Apps and TeamCity"
---
In this blog post I'll share my experience with implementing a continuous integration/deployment system using Git, GitHub, TeamCity, ASP.Net MVC 5 and Azure Web Apps.

## What we are going to do ##

1. Create a new Windows Azure Web App
2. Enable Git publishing on created Web App 
3. Create a repository on GitHub to store your code
4. Create a brand new ASP.NET MVC 5 Web Site with a Unit Test project in your git cloned folder 
5. Push my web site to the GitHub repository
6. Configure TeamCity to pull any changes from my GitHub repository, build the code, run the Unit Tests and push the Web Site on to Azure App local Git repository
7. TeamCity push artifacts to Azure App local Git repository
8. Testing the result
9. Failing tests
10. Rollback deployed version

I will **NOT** set up GitHub integration for the Web Site. TeamCity will monitor changes on GitHub repository and pull that code.

I assume you have a working TeamCity version either locally or remotely. If not there are many tutorials that could help you accomplish that. Here is [confluence official documentation](https://confluence.jetbrains.com/display/TCD9/Installing+and+Configuring+the+TeamCity+Server "Installing and Configuring the TeamCity Server") 

## Step1: Create a new Azure Web App ##

Nothing could be more easy than creating your very own website in Azure. Navigate to your [Azure Portal](http://portal.azure.com "Azure Portal") account Simply click `New -> Web + Mobile -> WebApp`. A new blade will appear on the right, enter your unique website Url, choose pricing and location, click Create and wait for your new website to go live, after it's finished you can navigate to your Web App Url, that easy.

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropbox.com/s/1in2tpdrsq97ejg/Screenshot%202015-04-10%2011.40.46.png" alt="Add Web App" class="image" style="width: 100%;"> 
</div>
</div>

## Step2: Enable Git publishing on created Web App ##

After the Web App is created open the respective tile scroll down to Deployment area and click `Set up continuous deployment` choose `Local Git Repository` as source and save your changes. Make sure if you haven't done already to add your credentials, you can do that on the tile next to `Set up continuous deployment` choose `Local Git Repository` which has your Web App name.

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropbox.com/s/wv0w1v6pscgljep/Screenshot%202015-04-10%2014.32.24.png" alt="Enable Git publishing on the Web App" class="image" style="width: 100%;"> 
</div>
</div>

What we've done so far is create a Web App and enable git for deployments. We are not going to push directly to this Git Repository, we will be using TeamCity to build our code and push changes to Web App Local Git Repository.

## Step3: Create a repository on GitHub to store your code and clone it locally ##

I'm not going into details here there are many tutorials that can help you [create a public/private GitHub repository](https://help.github.com/articles/create-a-repo/ "create a public/private GitHub repository"). Make sure you initialize it with a readme file, include .gitignore for Visual Studio and add a license file. 

Once you have a repository on GitHub the next step is to clone it to your machine you can use any number of free tools, even Visual Studio for that task or [follow this tutorial for a simple command line clone](https://help.github.com/articles/duplicating-a-repository/ "Git Clone")

## Step4: Create a brand new ASP.NET MVC 5 Web Site with a Unit Test project in your git cloned folder ##

Again I'm not going into details of how to getting started with ASP.NET MVC, but I'll point you to [this amazing tutorials](http://www.asp.net/mvc/overview/getting-started/introduction/getting-started "Getting Started with ASP.NET MVC 5"). Two things to keep in mind, the project folder needs to be inside of the Git Cloned folder and don't forget to add a Unit Test project even if it has a dummy test.

## Step5: Push my web site to the GitHub repository ##

Now it's time to push your new shiny web site to GitHub. 

To do that you can either run your Git GUI of choice or run the following Git commands at shell prompt on your cloned folder:

- git add . (Adds all of our files to the Git Repository.)


- git commit –am “Initial Commit.” (Confirms or Commits that my changes –all of the adds – are correct.)


- git push (Pushes the local commit to the remote repository – the GitHub source.)

If you now navigate to your GitHub repository you can see after a few moments all your files there.

## Step6: Configure TeamCity to pull any changes from my GitHub repository, build the code, run the Unit Tests ##

Wow that is a lot of things in one step... We need TeamCity to tie all of this together, this is our next and final big step. Here are the things we want TeamCity to do:

1. Monitor when changes are pushed to our Github repository
2. Integrate all changes
3. Download Nuget packages
4. Build the solution
5. Run Unit Tests
6. If everything succeeds push the new changes to Azure Wep App Local Git Repository

Open a new Browser window and navigate to your TeamCity `administration` page and `Create a new Project`, give it a name and click create.


<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropbox.com/s/no5wp8122tk8b4d/Screenshot%202015-04-10%2015.48.31.png" alt="Create TeamCity project" class="image" style="width: 100%;"> 
</div>
</div>

On the next screen select `Create build configuration` and add a name

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropbox.com/s/0xqgmbq0gs8f1ws/Screenshot%202015-04-10%2015.53.07.png" alt="Create build configuration" class="image" style="width: 100%;"> 
</div>
</div>

On the following screen (VCS Root) add a unique name and add the `Fetch URL` this URL can be found on your GitHub repository page on the right side where it says `HTTPS clone URL` this URL is used so TeamCity can fetch changes when you push to GitHub.

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="
https://dl.dropbox.com/s/qjz1837mtnz7rkd/Screenshot%202015-04-10%2015.55.36.png" alt="Setup VCS" class="image" style="width: 100%;"> 
</div>
</div>

> Important: After this step we need to run the Build so that TeamCity can get the source code for the first time. This will help us setting up other screens.

Now we need to change some Settings, using the navigation menu on the left, select `General Settings` and change the `Artifact paths` value. In this case we are going to select the folder of the project only (Not the tests or packages folder), you can use the tree selector next to the input. 

Next we need to change `Version Control Settings`, using the navigation menu on the left. Go to `Checkout Options` and set `VCS checkout mode` to `Automatically on agent (if supported by VCS roots)`, this is very important so that Git Version Controls the folder we run the build.

Next select `Triggers`, using the navigation menu on the left. Click `Add new trigger` select `VCS Trigger` and check `Trigger a build on each check-in` and `Include several check-ins in a build if they are from the same committer`. This will guaranty that when you check in code changes to GitHub TeamCity will start the build process. 

Next select `Build Steps` on the left and `Add build step`. 
The first step will be `Nuget Installer` select it from the drop down and leave it with it's default values and save.

Click `Add build step` again and select `Visual Studio (sln)` select the `Solution file path` using the tree picker, set `Visual Studio` version to `2013`. `Targets` = `Rebuild` and `Configuration`=`Release`

Add another Build step this time select `NUnit` and change the value of `Run tests from` to `**/*.Tests.dll` this will look for all dlls that finish with `Tests`

This should be your final Build Steps result

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropbox.com/s/f219e93tkmh15me/Screenshot%202015-04-10%2016.27.37.png" alt="Build Steps" class="image" style="width: 100%;"> 
</div>
</div>


Now run your build again and you should see your build steps running and artifacts being generated

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropbox.com/s/idyx973zxwnndpt/Screenshot%202015-04-10%2016.25.27.png" alt="TeamCity Build" class="image" style="width: 100%;"> 
</div>
</div>


## Step7: TeamCity push artifacts to Azure App local Git repository ##

This is the final step now we are going to push TeamCity built artifacts to Azure App Local Git repository

     For this to work TeamCitys Machine/Vm needs to have Git installed

Before we add another build step we need to get the the Azure App Local Git Repository Deployment Trigger URL, at the time of this writing that key is only available in the "old Azure Portal". Once you are in the old portal go to your Web App `Configure` tab and scroll down and find the `Deployment Trigger URL` copy it.

The format should be similar to this:

     https://$henriquetest:ZRkqvSMG2jYyuXvi0R1WlNqxpmqAH7T0JSLE7jKwjPPZ39Ckh0tRfbBKGy24@henriquetest.scm.azurewebsites.net/deploy
This URL includes your Web App name and security keys, don't share with anyone.

Next we need to replace some text, where it says deploy change to your Web App name and add .git to the end, it should look like this:

    https://$henriquetest:ZRkqvSMG2jYyuXvi0R1WlNqxpmqAH7T0JSLE7jKwjPPZ39Ckh0tRfbBKGy24@henriquetest.scm.azurewebsites.net/henriquetest.git 

Now we can add the final build step.

Got to your Build settings and add another Build Step this time select `Command Line`, set `Run` to `Executable with parameters` and set `Command executable` to `C:\Program Files (x86)\Git\bin\git.exe` and set `Command parameters` to `push <Deployment trigger URL> master`: 
    
    push https://$henriquetest:ZRkqvSMG2jYyuXvi0R1WlNqxpmqAH7T0JSLE7jKwjPPZ39Ckh0tRfbBKGy24@henriquetest.scm.azurewebsites.net/henriquetest.git master
Notice that I've added "push" to the beginning and "master" to the end. This is basically a git command to push to a specific branch on a specific remote.

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropbox.com/s/psi694ujbopddvd/Screenshot%202015-04-10%2016.47.21.png" alt="Push to Azure" class="image" style="width: 100%;"> 
</div>
</div>

## Testing the result ##

Go to your project on TeamCity and click `Run`. When all the build steps are complete and the result is green fingers crossed, some magic will happen!

The last thing TeamCity does is push to the Git repository in your Web App. To check if it worked go to your Azure Portal go to your Web App tile and check the `Deployment` area, you should see `Success` and your last build time and comment if you click it you get a new blade will appear with all the build history.

<div class="row">
<div class="col-lg-12 col-md-12">
<img src="https://dl.dropbox.com/s/fu4hmg5bosvgv65/Screenshot%202015-04-10%2016.54.15.png" alt="Push to Azure" class="image" style="width: 100%;"> 
</div>
</div>

## Failing tests ##

Now we can check if we really accomplished a true continuous integration environment.

On Visual Studio make your Unit Tests fail, push your changes to GitHub, wait for TeamCity to pick your changes and you will see that the build failed and that no changes where pushed to your Web App. 

This is vital in a continuous integration when someone checks something that breaks the tests, we don't want those changes to go to our customers.

## Rollback deployed version ##

Even with all the Unit tests and integration tests running against our code, sometimes bugs make their way to our customers. If we accidentally add a bug to our Web App Azure has a great feature that allows us to revert those changes by restoring and older build.

Go to Azure portal and to your Web App, scroll down to `Deployment`, open the deployments blade to see the deployment history. In that screen the current version deployed to your Web App has a green tick if that version had a bug we could easily pick a previous version and redeploy it.

Select an older build and click redeploy on the blade. When you browse to your Web App you should see that that version is deployed, now you can start working on fixing that bug :)

We are running this Web Site in one single free instance. Even if you scale this Web Site out to multiple instances the steps back and forth in history are just as fast. All of your Web Apps will redeploy any version you pick. Amazing

## Summary ##

From my experience this is a "Real world" continuous integration example, in this case I'm using TeamCity but it also applies to Jenkins, TFS or any other Build System. This setup gives developers more confidence because they integrate their features, run tests and only if everything is successful the code is deployed to the live web site.

Another great feature is the possibility to go back in time and revert to a better deployment if we accidentally added a breaking bug on our code

Hope this tutorial helps you configure your continuous integration and deployment environment.