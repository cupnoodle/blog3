---
layout: post
title:  "Deploying node.js application on Heroku using GitHub"
date:   2015-06-01 20:10:10
categories: post
permalink: "deploy-nodejs-heroku-github"
---

Some of my friends have been using Heroku and recommend me to use it to deploy apps. I am comfortable with setting up the full stack from OS to application as I like server configuration, nevertheless I gave Heroku a try since it has a free tier on it.  

![alt text](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post15/1.png "Let's try heroku!")

![alt text](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post15/2.png "Derp app in the making")

![alt text](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post15/3.png "Deploying from GitHub")

![alt text](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post15/4.png "Seems pretty easy")  
<br><br>
Deploying node.js app on Heroku is like a piece of cake until I go to view my app and this popped out :   

![alt text](https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post15/5.png "error FFFFUUUUUUUUU")
<br><br>  

## What went wrong?

Deploying directly using GitHub is kinda tricky because there is no system log to reference which you can view using Heroku Toolbelt.  I have spent many hours on trying to find a solution around stack overflow and google, I should have read [Heroku Documentation](https://devcenter.heroku.com/articles/getting-started-with-nodejs) earlier Orz. Turns out I missed Procfile and a port configuration.

## Procfile
Procfile is used to tell Heroku what command should be executed to start your app, this text file should be stored in the root of your application using the filename `Procfile` (no filename extension).  
<script src="https://gist.github.com/cupnoodle/74c1032cb110e280116f.js"></script>  
The `web` keyword here tells Heroku that this app process will be attached to the HTTP routing stack of Heroku and will receive web traffic when deployed. 
The `node index.js` will execute the index.js file as the main node application.  

## Port configuration
For local testing I used port 3000 for the node.js app but Heroku doesn't give you a fixed port for app and it will dynamically assign you one through process environment variable. Heroku recommends you to use their process environment variable(`process.env.PORT`) as shown in below :  
<script src="https://gist.github.com/cupnoodle/3d4aa5ffbc9bd7f2527b.js"></script>  
I changed the port configuration from fixed port 3000 to the format as shown above and it worked finally TwT/.  

## The node.js app
The app I have deployed on Heroku is located [here](http://multisonic.herokuapp.com). I am currently using Heroku free plan for this app so there will be 6 hours of sleep time a day =(. Try opening the app on two tabs/browsers and you can see both of your characters. I developed this app while learning node.js using express.js framework and socket.io, it allows multiple players to see each other movement. The source code is available on [GitHub](https://github.com/cupnoodle/multi). Thanks for trying &gt;w&lt;/!