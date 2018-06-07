---
layout: post
title:  "How to Deploy Rails using Passenger - Part 1"
date:   2016-06-04 20:10:10
categories: post
permalink: "deploy-rails-passenger-rubyconf"
---

A post originally written for an upcoming workshop. This post assumes you have some knowledge on Ruby on Rails but have never touched a server before and have no experience in SysAdmin/DevOps.

## Introduction

If you are a Ruby on Rails developer who can't afford a PaaS like Heroku  💸, or want to learn how to setup rails on your own server, then this tutorial is suitable for you. 
We will be using [Phusion Passenger](https://www.phusionpassenger.com/) as the rails app server and [Nginx](https://www.nginx.com) as the web server. Passenger is one of the easiest app server to install, configure and have decent performance. For this tutorial, we will install Passenger with Nginx and also PostgreSQL on Ubuntu 14.04 . We will be using [DigitalOcean](https://m.do.co/c/f7f1b47b1fff) for its VPS, you can sign up using [this link](https://m.do.co/c/f7f1b47b1fff) for $10 free credit!  
  
Note : This tutorial demonstrates the basic steps needed to get a Rails app running on your own VPS and might not be the best way to deploy. I am aware of existing deployment/provisioning tools like Capistrano, Chef , Dokku etc can make the workflow better but the aim of this tutorial is to show the basic steps for setting up and deploy. I have also skipped server security measure as it is out of scope for this tutorial, remember to disallow root login and change the default ssh port for better security.  

## Step One - Create your server / droplet
  
Create a new Ubuntu 14.04 server, for starter applications, 512MB memory should suffice. You can upgrade it easily should you need more memory in the future. Choose the 32 bit version of Ubuntu if your server memory is less than 4GB, as 64-bit programs consume 50% more memory compared to 32 bit programs. If there is possibility which your server will need more than 4GB RAM in the future, choose the 64 bit version.
![Choose OS](https://littlefoximage.s3.amazonaws.com/post21/choose_os.png)
![Choose Spec](https://littlefoximage.s3.amazonaws.com/post21/choose_spec.png)
  

## Step Two - Create a Sudo user
  
First you have to login to your server as root, open up terminal and type <br> <code>ssh root@SERVER_IP_ADDRESS</code> <br>Replace SERVER_IP_ADDRESS is your server IP address. You will be prompted to input password that is sent to you by email if you didn't specify ssh key during server creation. If it is your first time connecting to ther server, you will be prompted about host authenticity, choose allow/yes. You might be prompted to change the root password in this case too.  

Now we will create a user with sudo privilege and we will login as the user after this step. It is dangerous running command as root because root has the absolute power to do anything and you might damage the system using root if not careful.

To create user, replace the _demo_ with your desired name : <br>
<code>adduser <span style="color: #F20B2E;">demo</span></code><br><br>
After entering password, you can just press enter to skip the field.  
To grant administrative power (able to execute command with sudo put in front) to the user, type the command below using root account, replace the _demo_ with your username : <br>
<code>gpasswd -a <span style="color: #F20B2E;">demo</span> sudo</code><br>
The command above will add the user to _sudo_ group.  
Now you can logout and login as this user.  
<code>exit</code>  
<code>ssh <span style="color: #F20B2E;">demo</span>@SERVER_IP_ADDRESS</code>  
    

## Optional Step - Setup Domain Name
This step is omitted in this tutorial but you can [refer here](https://www.digitalocean.com/community/tutorials/how-to-set-up-a-host-name-with-digitalocean) if you want to setup a domain name to link to your server.  
  

## Step Four - Install RVM
Before performing any installation step, we should run a quick update to make sure the installation packages are up to date.  
<code>sudo apt-get update</code><br><br>
Next, we will install RVM (Ruby Version Manager). RVM allows us to install multiple version of ruby in the system but for this tutorial we will just install only one version of ruby.  Type the following command to install RVM.  
<code>curl -L get.rvm.io | bash -s stable</code><br><br>
If the GPG signature verification failed, type the command returned by the prompt, usually in this format : <br>
<code>gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3</code> <br>
then retry the RVM installation command again. <br><br>

After finished installing, load RVM.<br>
<code>source ~/.rvm/scripts/rvm</code><br><br>
RVM has some dependencies which are required to work, to install them, type :  
<code>rvm requirements</code><br>
you might be prompted to enter your password for this command. <br><br>

## Step Five - Install Ruby  
Use the rvm command to install ruby, the latest stable version of ruby as of writing this post is 2.3.1.  
<code>rvm install 2.3.1</code><br><br>
Ruby is now installed in your system, but we still need to tell the system to use version 2.3.1 as default.
<code>rvm use 2.3.1 --default</code><br>

## Step Six - Install Rails
Ruby version 2.3.1 already included gem by default, to install rails, type :  
<code>gem install rails --no-ri --no-rdoc</code><br>
This command will install the rails gem without documentation (--no-ri --no-rdoc means without documentation) as you can find the [online documentation here](http://guides.rubyonrails.org/).  
After installing rails, we will install nginx and passenger to support the rails app.  

## Step Seven - Install Passenger
Passenger is available as a gem too, simply install it using  
<code>gem install passenger --no-ri --no-rdoc</code><br>  

## Step Eight - Install Nginx
Before proceeding to install, if your server has less than 1GB of RAM, you will have to add some swap space. You can skip this step if your server has more than 1GB of RAM.<br>
<code>sudo dd if=/dev/zero of=/swap bs=1M count=1024</code>  
<code>sudo mkswap /swap</code>  
<code>sudo swapon /swap</code>
<br>

We will also need to install Curl development headers with SSL support : <br>
<code>sudo apt-get install libcurl4-openssl-dev</code>
<br>

Now we will install Nginx web server with passenger module  
<code>rvmsudo passenger-install-nginx-module</code><br>

Type in your password if it prompts, after that you will be presented to a screen like this :   
![Press enter to continue](https://littlefoximage.s3.amazonaws.com/post21/install_passenger.png)  
Press <kbd>enter</kbd> to continue  

![Press enter to continue](https://littlefoximage.s3.amazonaws.com/post21/install_passenger2.png)  
Press <kbd>space</kbd> to select the language/framework you want, we will be selecting only Ruby for this tutorial.  

![Press enter to continue](https://littlefoximage.s3.amazonaws.com/post21/install_passenger3.png)  
Type 1 and press enter unless you know what you are doing 😅.

![Press enter to continue](https://littlefoximage.s3.amazonaws.com/post21/install_passenger4.png)  
Press <kbd>enter</kbd> when it ask which directory to install, we will be using default. (default installation directory is <strong>/opt/nginx</strong>) Grab a bite or coffee while the installation is running, this might take quite some time.  

![Press enter to continue](https://littlefoximage.s3.amazonaws.com/post21/install_passenger5.png)  
Press enter and continue. Take note of the Nginx configuration file location.
After installing, we will add a system startup script for nginx so that we can easily start/stop/restart it next time. (From [http://askubuntu.com/questions/257108/trying-to-start-nginx-on-vps-i-get-nginx-unrecognized-service](http://askubuntu.com/questions/257108/trying-to-start-nginx-on-vps-i-get-nginx-unrecognized-service) )<br><br> 
Download the Nginx startup script from linode documentation. (Thanks good guy Linode!)  
<code>wget -O init-deb.sh https://www.linode.com/docs/assets/660-init-deb.sh</code>

Then move the script to the init.d directory and make it executable  
<code>sudo mv init-deb.sh /etc/init.d/nginx</code><br>
<code>sudo chmod +x /etc/init.d/nginx</code>

Add Nginx to the system startup  
<code>sudo /usr/sbin/update-rc.d -f nginx defaults</code>

Then we can start the Nginx web server using :  
<code>sudo service nginx start</code>

_Tips : After adding the Nginx start up script, we can use the following command :_   
<code>sudo service nginx start</code><br>
<code>sudo service nginx stop</code><br>
<code>sudo service nginx restart</code><br>   

After starting Nginx, you can now type in the server IP address in browser and you should see a Nginx welcome page.

![Press enter to continue](https://littlefoximage.s3.amazonaws.com/post21/nginx_success.png)

## Step Nine - Install NodeJS (Javascript Runtime)
Before creating a new rails project, we will have to install a javascript runtime first as rails require javascript runtime to work. We will be installing NodeJS for its javascript runtime :   
<code>sudo apt-get install nodejs</code><br>  

## Step Ten - Install PostgreSQL  
We will install PostgreSQL as the database for rails as it is the most pouplar RDBMS for rails.<br>
<code>sudo apt-get install postgresql postgresql-contrib libpq-dev</code>  
By default, Postgresql will create a database user/role named '**postgres**'. We will need to login using this account and create a new user/role.  

Type the command below to create a new superuser for the database. The command below will use the postgres account (in the operating system) to create a superuser named <strong>demo</strong> for the database, change the <strong>demo</strong> to match the username of the sudo user you created in step two.  
<code>sudo -u postgres createuser -s <span style="color: #F20B2E;">demo</span></code>  

After creating the database user, we will need to set a password for the user. First, log into the PostgreSQL console using the postgres system account :  
<code>sudo -u postgres psql</code>  

If you see the <strong>postgres=#</strong> prompt, means you are inside the PostgreSQL console. In the PostgreSQL prompt, enter this command to set password for the newly created user :   
<code>\password <span style="color: #F20B2E;">demo</span></code>  

Your screen should look similar to this :
![PostgreSQL console](https://littlefoximage.s3.amazonaws.com/post21/postgres_prompt.png)  

After setting the password, type <code>\q</code> to quit the console.  

Don't forget to install PostgreSQL gem so that the rails app can communicate with the database :   
<code>gem install pg --no-ri --no-rdoc</code>

## Next Part - Git push and deploying

Howdy! You have made it through the installation part, give yourself a pat on the back and we will continue the deploying process using git in the [next post](2016-06-05-deploy-rails-passenger-rubyconf-part2).  
![Asriel Howdy](https://littlefoximage.s3.amazonaws.com/post21/howdy.png) 
