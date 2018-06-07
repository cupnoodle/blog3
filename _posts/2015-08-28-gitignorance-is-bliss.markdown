---
layout: post
title:  "Remember to gitignore sensitive information"
date:   2015-08-28 22:10:10
categories: post
permalink: "gitignorance-is-bliss"
---

_Note : the purpose of writing this post is to serve as a reminder to gitignore sensitive data in the repository, not teaching you how to exploit Github search and getting others credentials._

We all know that Git is awesome for version control and tracking of code, and GitHub is the usual go-to place for git repository hosting as it is free for public / open source repository. Since any file can be pushed to Github, if you are not careful with gitignore, the pushed files may include your private key information, database connection credential and even worse your Amazon S3 keys. 

By default, SSH searches for **id_rsa** and id_dsa files when an user attempt to connect via using key, hence there is a large possibility that some people may have pushed their own private key to Github… Lets search _“filename:_ _id_rsa”_ for example : 

![That's lots of key here](https://littlefoximage.s3.amazonaws.com/post16/rsakeys.png)
<br><br>
That's lots of key here, by having their private key means you can impersonate as them and if you have access to their server which happened to use SSH key authentication....bingo! Now imagine someone has your private key and your server uses SSH key authentication, woah scary isn't it?  

I followed [RailsApps guide on how to set environment variable](http://railsapps.github.io/rails-environment-variables.html), one of the way mentioned in the guide is to create a **local_env.yml** file to store environmental variables. The RailsApps website is kinda popular so I figured maybe there will be people who follow the guide and forgot to gitignore the _local_env.yml_ file, let's try _"filename:_ _local_env.yml"_ this time: 

![Lot's of secret here too!](https://littlefoximage.s3.amazonaws.com/post16/localenvyaml.png)
<br><br>
Managed to find a few of Facebook App ID /Secret and Google API Secret from some results shown above. Hmm, lets try something more scary, _"filename:_ _local_env.yml_ _amazon"_

![And you wonder why your Amazon bills have been expensive recently](https://littlefoximage.s3.amazonaws.com/post16/amazons3keys.png)
<br><br>
[And you wonder why](https://securosis.com/blog/my-500-cloud-security-screwup) there's [a spike in your Amazon bills recently](http://vertis.io/2013/12/16/unauthorised-litecoin-mining.html). Well the good news is that Amazon frequently scans public repository on Github and they will notify the owner to remove the file from Github if found.

Be extra careful when your code involves keys and secret, it can cost you thousands or even your integrity at worst. [Github has written an instruction on how to remove sensitive data](https://help.github.com/articles/remove-sensitive-data/) if you have accidentally pushed it to remote. Please remember to immediately revoke access to all the previous keys and generate new keys once you found that the keys have been pushed to remote for safety measure, some bots may have recorded it down.

<br><br>
<em>TL;DR : Gitignore is serious business, remember to ignore all your keys and secrets before commiting.</em>
