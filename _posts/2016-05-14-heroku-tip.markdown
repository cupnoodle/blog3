---
layout: post
title:  "Heroku command reminder"
date:   2016-05-14 21:10:10
categories: post
permalink: "heroku-tip"
---

A short post to serve as a reminder in case I forgot it again.

## Heroku tip
Since I got used to use Github to deploy apps to Heroku, I didn't type <code>heroku create</code> command in terminal and it gets a bit tricky to retrieve log or perform <b> rake </b> task. Found the solution on StackOverflow, just add a parameter <code>-a <i>appname</i></code> after the command!  

Let say you have a heroku app named <b>fluffytails</b> and you want to run <b>rake db:migrate</b>, then just enter <code>heroku run rake db:migrate -a fluffytails</code> on your local machine, thats it! Be sure to <b>heroku login</b> on your local machine first.

Rails on Heroku is really easy and convenient, too bad the price escalates quickly when the RAM usage increase 😲 .