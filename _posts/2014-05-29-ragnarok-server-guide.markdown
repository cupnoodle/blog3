---
layout: post
title:  "How to set up a private Ragnarok Online Server using Linux"
date:   2014-05-29 22:51:15 +0800
categories: ragnarok online linux
permalink: "how-to-set-up-a-private-ragnarok-online-server-using-linux"
---

## Introduction

I really like Ragnarok Online and it consist most of my early teenage year.  
After it changed to pay to play model, I turned into private servers and subsequently interested in how to make a private ragnarok online server.  
Most guide I found online is a bit unclear so I decided to make one to help future people that are interested -w-.
Note: This is kinda like gray area of law, hosting ragnarok server is not illegal but modification and distribution of client is, proceed carefully.

<br>

## Prerequisite
In this guide I will assume you have a little bit knowledge on Linux command.

I used <a href="https://www.digitalocean.com/?refcode=41d7946659b4">Digital Ocean</a> VPS for demonstration purpose.  

<img src="https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post5/step0.png">

You need <b> at least 512 MB RAM </b> for the server.  
Recommended 1GB RAM if you want smooth performance.  

For this tutorial I will use Ubuntu 14.04, but no worries, you can use any Linux distro.  Just replace the <code> apt-get</code> command with other repository code.  

<img src="https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post5/step1.png">  

Use default setting and click "Create Droplet"  

<a href="https://www.digitalocean.com/community/articles/initial-server-setup-with-ubuntu-12-04"> Follow this guide to create a new non-root user for the server</a>  
<i> Using root account is dangerous if you dont know what you are doing ^^;</i>  

After setting up the new account, login with your new account to your server.

<br>

## GUI for managing database  
To make it easier to manage database, we need to install nginx for web server,php for processing, MySQL for database and phpMyAdmin to access database using GUI.  
I prefer to use Nginx than Apache as it consumes less RAM. If you have a web server running before then you can skip to next section.  

<a href="https://www.digitalocean.com/community/articles/how-to-install-linux-nginx-mysql-php-lemp-stack-on-ubuntu-14-04"> Follow this guide to install Nginx, PHP, MySQL</a>

<h3>phpMyAdmin installation guide </h3>  
<code>sudo apt-get install phpmyadmin</code>    


During the installation, phpmyadmin will ask you if you want to configure the database with dbconfig. Go ahead and choose yes.  

Input MySQL database password when prompted and click ok.

When phpmyadmin prompts you to choose a server (either apache or lighttpd) hit tab, and <b>select neither one</b>. 

Create a symbolic link (shortcut, so you can access it with http://<i>your_server</i>/phpmyadmin)  
<code>sudo ln -s /usr/share/phpmyadmin/ /usr/share/nginx/html/phpmyadmin</code>  

Restart the nginx server  
<code> sudo service nginx restart</code>  

By accessing http://<i>your_server</i>/phpmyadmin , it should look like this :   
<img src="https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post5/step2.png">   

Log in with your MySQL username and password.   
Create a database named <i>herculesrag</i> (or other name you like, I will use this name for the following section) using phpmyadmin before proceeding to next step.  

<br>

## Installing and configuring Hercules server
We will be using Hercules server as emulator for Ragnarok Online's server.  
First, install the dependency.  
<code>apt-get install git make gcc mysql-server libmysqlclient-dev zlib1g-dev libpcre3-dev screen</code>  

Then navigate to the folder you desire, then clone the Hercules folder from github.  
<code>git clone https://github.com/HerculesWS/Hercules.git ~/Hercules</code>  

Change the current directory to  the Hercules folder we just copied.  
<code> cd Hercules</code>  

Change directory to <b>sql-files</b> folder, we need to import some stuff to database first.  
<code> cd sql-files </code>  

Login to mysql using terminal/command prompt.  
<code> mysql -u <i>mysql username</i> -p </code>  
Then type your mysql user's password when prompted.  

Execute the following commands :    
(replace <i>herculesrag</i> with your database name)  
<code><i>herculesrag</i> < item_db.sql </code>  
<code><i>herculesrag</i> < item_db2.sql </code>  
<code><i>herculesrag</i> < item_db_re.sql </code>  
<code><i>herculesrag</i> < logs.sql </code>  
<code><i>herculesrag</i> < main.sql </code>  
<code><i>herculesrag</i> < mob_db.sql </code>  
<code><i>herculesrag</i> < mob_db2.sql </code>  
<code><i>herculesrag</i> < mob_db_re.sql </code>  
<code><i>herculesrag</i> < mob_skill_db.sql </code>  
<code><i>herculesrag</i> < mob_skill_db2.sql </code>  
<code><i>herculesrag</i> < mob_skill_db_re.sql </code>  

Go back to Hercules folder  
<code>cd ../</code>  


For 64 bit Linux, type the following code to compile Hercules.  
<code>./configure</code>  
<code>make sql</code>  

For 32 bit Linux, type the following code to compile Hercules.  
<code>./configure --disable-64bit</code>  
<code>make sql plugins</code>  

Before you start the server, use Filezilla or any other ftp client to access your server and go to <i>Hercules/conf</i> directory.  
Download,edit and reupload the following file : <i>char-server.conf</i> , <i>inter-server.conf</i>,  <i>login-server.conf</i>, <i>map-server.conf</i>.  

Replace the server ip with your server ip.  
Replace the sql db,user and password with your own database information.  
Replace the following with your desired username and password for the server account.   

<img src="https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post5/step3.png">  

Then go to phpmyadmin, access your <i>hercules_rag</i> table and choose the login table, find the row with <b> account_id '1' </b> .  
<img src="https://s3-ap-southeast-1.amazonaws.com/littlefoximage/post5/step4.png">  

Replace the userid and user_pass with the username and password you typed just now.  

<br>

## Starting the server
Navigate to the Hercules folder,  
Change the file access permission on the start file  
<code>chmod a+x athena-start</code>  

To start the server, type  
<code>./athena-start start</code>  
To stop,  
<code>./athena-start stop</code>  
To restart,  
<code>./athena-start restart</code>  

Ta-dah! Your ragnarok server should be running fine now , cheers!  

<br>

## Extra Information
<a href="http://herc.ws/wiki/Main_Page"> Hercules server official wiki</a>  
<a href="http://herc.ws/board/topic/3768-2013-12-23-full-client-download/"> Client files and tools </a>  
