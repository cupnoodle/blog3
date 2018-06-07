---
layout: post
title:  "How to Deploy Rails using Passenger - Part 2"
description: "How to setup and deploy rails using passenger and nginx"
date:   2016-06-05 21:10:10
categories: post
permalink: "2016-06-05-deploy-rails-passenger-rubyconf-part2"
---

## Recap

This post is a continuation from [here]({% post_url 2016-06-04-deploy-rails-passenger-rubyconf %}). Previously we have installed Ruby, Rails, Nginx, Passenger and PostgreSQL. On this post we will install git, configure nginx config file to deploy rails app and git push the rails app from your development machine to the VPS. The step number will continue from the previous one, which is eleven.  

## Step Eleven - Install git and creating a repository

Install git on the VPS using this command :  
<code>sudo apt-get install git</code><br>

After installing Git, we will now create a bare git repository named <strong>awesomeapp.git</strong> and a folder to store the source code <strong>awesomeapp</strong>, let's put the repository on your user folder for this tutorial ( you can of course change to other location ).  
<code>cd ~ </code>  
<code>mkdir awesomeapp</code>  
<code>mkdir repo && cd repo</code>  
<code>mkdir awesomeapp.git && cd awesomeapp.git</code>  
<code>git init --bare</code>  

<strong>--bare</strong> indicates that the repository will have no working files / source code, just the version control.  

Type <code>pwd</code> to get your present working directory, you will need this later. My output looks like this :  
![pwd](https://littlefoximage.s3.amazonaws.com/post22/pwd.png)  

Next, we will dive into the git hooks to add a work tree directory (where your source code will be stored), more info about [git hooks here](http://githooks.com/). We will use the <strong>post-receive</strong> hook as it is executed after a git push has finished receiving.

Inside <strong>awesomeapp.git</strong> directory, if you type <code>ls</code>, you can see the list of files and folders like this :  
![git inner mysteries](https://littlefoximage.s3.amazonaws.com/post22/git_inner.png)  

Go to the <strong>hooks</strong> folder  
<code>cd hooks</code>  
Create a file named 'post-receive' :  
<code>cat > post-receive </code><br><br>
When you execute the command above, you will see a blank line indicating that everything you type will be saved to this file. Let's proceed to type : <br>
<pre>
#!/bin/sh
git --work-tree=/home/soulchild/awesomeapp --git-dir=/home/soulchild/repo/awesomeapp.git checkout -f
</pre>  
Replace the <strong>work-tree</strong> directory with your desired location, this is the place where the source code will be saved.  
Replace the <strong>git-dir</strong> directory with the bare git repository you created just now.   

After finish typing, press <kbd>Ctrl</kbd> + <kbd>D</kbd> to save. We need to allow permissions for the file to be executable :    
<code>chmod +x post-receive</code>

Now the source code will be saved in the <strong>work-tree</strong> every time a git push is received by the <strong>awesomeapp.git</strong> repository.

{% comment %}
### Optional but recommended step - Set up git hooks

[Git hooks](http://githooks.com/) are trigger points which you can define commands that will be executed whenever a specific event is triggered. ( eg : restart Nginx server after a git push is received )  
Continuing from previous step, we will edit the post-receive hook for the awesomeapp git repository.  
<code>cd .git</code>

We are inside the inner working of git now, if you type <code>ls</code> to list the directories, you will see output similar to this :  
![Inner mysteries of how git works...or not](https://littlefoximage.s3.amazonaws.com/post22/git_inner.png)  

Now lets go to <strong>hooks</strong> folder

{% endcomment %}

## Step Twelve - Push the Rails app from your local machine to VPS

<strong>Now on your local machine ( your laptop/pc )</strong>, locate to your existing Rails application or create one if you don't have one :  
<code>rails new awesomeapp -d postgresql</code><br>

If you are creating a new Rails application, make sure to point the root route to a controller action first.  

Open <strong>/config/database.yml</strong> of the rails app, scroll to the bottom and edit the production database username to look like this :  
<script src="https://gist.github.com/cupnoodle/b2b63c556064a89fca1f7c59968316b8.js"></script>  

We will also generate a secret key for the production secret key base.  
<code>cd awesomeapp</code>  
<code>rake secret</code>

![Keep this a secret! Shhh](https://littlefoximage.s3.amazonaws.com/post22/rake_secret.png)  
Copy down the string generated, we will need it later for setting environment variables.  

Remember the present working directory from the previous step? We will add it to the git remote of the Rails app. If your local rails application has not initialize a git repository yet,  
<code>#inside awesomeapp rails root folder </code>  
<code>#cd awesomeapp</code>  
<code>git init</code><br>

Then add the remote : <br>
<code>git remote add <span style="color: #F20B2E;">live</span> ssh://<span style="color: #F20B2E;">user</span>@<span style="color: #F20B2E;">YOUR_SERVER_IP_OR_DOMAIN_NAME</span>/<span style="color: #F20B2E;">repo_location</span></code>

<strong>live</strong> is the name of the remote, you can change this to whatever name you like.  
<strong>user</strong> is the username you used to login to the ssh, change this to your ssh username.  
<strong>YOUR_SERVER_IP_OR_DOMAIN_NAME</strong> is pretty much self-explanatory.  
<strong>repo_location</strong> is the location of the repository in the VPS, replace this with the bare git repository mentioned in the previous step (eg: 'home/soulchild/repo/awesomeapp.git').
<br>  

And now you are ready to push to the remote VPS, replace <strong>live</strong> with the remote name you have chosen just now.  
<code>git add -A</code>  
<code>git commit -m "Awesome app first commit"</code>  
<code>git push live master</code>  

Sweet! Next, let's edit the Nginx configuration to serve the Rails app and include environment variables as well.  

## Step Thirteen - Edit Nginx Configuration file and deploy

Connect to your VPS using ssh  
<code>ssh <span style="color: #F20B2E;">demo</span>@SERVER_IP_ADDRESS</code>  
Edit the Nginx configuration file using nano  
<code>sudo nano /opt/nginx/conf/nginx.conf</code>  

In the server block inside http block, edit it to look like this : 
<pre>server {
        listen       80;
        server_name  localhost;
        root /path/to/rails_app_root/public; # <-- be sure to point to 'public'
        passenger_app_root /path/to/rails_app_root;
        passenger_enabled on;
        rack_env production;
        passenger_env_var SECRET_KEY_BASE d6532f57f0e1f5150f38ef413fd85c2c9081195431177950d6e44248822ab3d05bb2619871c29a8367d95ff04abaaba84bfc4433f37d089c9437c3e2e1efadc6;
        passenger_env_var AWESOMEAPP_DATABASE_USERNAME demo;
        passenger_env_var AWESOMEAPP_DATABASE_PASSWORD correcthorsebatterystaple;
}
</pre>

1. Replace the <strong>/path/to/rails_app_root/</strong> with the work tree you defined in previous step.  
2. <strong>rack_env</strong> is the environment of the rails app, we set it to production.  
3. <strong>passenger_env_var</strong> is used to define environment variables, you can define multiple environment variables in the config file. Replace the value of environment variables above with yours.  

Press <kbd>Ctrl</kbd> + <kbd>X</kbd> to finish editing, type 'Y' and press <kbd>Enter</kbd> when prompted to save.
<br>
Your nginx.conf will look similar to this :  

<script src="https://gist.github.com/cupnoodle/6a48e77e6243fc2c4c05f9e34b2d58a4.js"></script>

Now we will move to the Rails application root directory (the work tree),  
<code>cd ~/awesomeapp</code>  

Run bundle install  
<code>bundle install</code>  

Precompile assets, remember to set the RAILS_ENV to production  
<code>rake assets:precompile RAILS_ENV=production</code>  

Restart the Nginx server first so that the environment variables will be loaded :  
<code>sudo service nginx restart</code>  

Run rake:db create to create database  
<code>rake db:create RAILS_ENV=production</code>  

Run rake:db migrate to migrate database  
<code>rake db:migrate RAILS_ENV=production</code>  

And finally restart the Passenger app to update the changes  
<code>passenger-config restart-app <b>/path/to/rails_app_root</b></code>  

## Step Fourteen - Cheers!

Now navigate to your VPS IP /domain name in the browser, you should see your Rails app running. 🍻  
![Bye Heroku](https://littlefoximage.s3.amazonaws.com/post22/deployed.png "Bye Heroku!")   

Next time whenever you git push a new update to remote, remember to login to the VPS and run these commands :  

1. bundle install  
2. rake assets:precompile RAILS_ENV=production  
3. rake db:migrate RAILS_ENV=production  
4. passenger-config restart-app /path/to/rails_app_root  

Passenger app server must be restarted to reflect the changes after every git push.  

Wait.... why do we need to repetitively execute these commands after git push, there must be a better way to automate this right? Yes there is! we can add these command to run in the post-receive hook, remember?  

## Step Fifteen - Adding deployment task to post-receive hooks

Lets go to the repository of the rails app again and edit the post-receive hook :  
<code>cd ~/repo/awesomeapp.git</code>  
<code>cd hooks</code>  
<code>nano post-receive</code>   

We will edit it to look like this :  
<script src="https://gist.github.com/cupnoodle/50db5e50c31b32a44766e927a57d28ad.js"></script>
Replace the work tree and git dir accordingly, press <kbd>Ctrl</kbd> + <kbd>X</kbd> to end editing. 


<b>Edited 19 July 2016</b> : I found that restarting the Passenger app is sufficient without restarting Nginx, no sudo permission is required for restarting passenger app.<br>
<s>
Notice that there is a sudo command which is the <strong>sudo service nginx restart</strong>, executing a sudo command requires a password input hence the restart command will not run in this post-receive hook. We have to manually set that no password is required for this command (nginx) for the user (the sudo user you created).<br><br>
Let's edit the user file in sudoers.d directory, replace <strong>demo</strong> with your username :<br>
<code>sudo visudo -f /etc/sudoers.d/90-<span style="color: #F20B2E;">demo</span></code>  <br><br>
Add this line to the end of the file , replace <strong>demo</strong> with your username :<br>
<code><span style="color: #F20B2E;">demo</span> ALL=(ALL) NOPASSWD:/etc/init.d/nginx</code>  
Press <kbd>Ctrl</kbd> + <kbd>X</kbd> to finish editing, type 'Y' and press <kbd>Enter</kbd> to save.  
</s>

Now you should be good to go. In your local machine development repository, make a commit and push it the remote, you will see an output like this :  
![Git push looks more magical now](https://littlefoximage.s3.amazonaws.com/post22/git_deploy_success.png "Git push looks more magical now")    

And after receiving push, the server will run those deployment commands automatically and the web pages has changed :  
![Look! No manual restart!](https://littlefoximage.s3.amazonaws.com/post22/deployed_with_auto.png "Look! No more manual deploy and restart!")   

Now every time you push master branch to remote, the remote will deploy automatically. Sounds like Heroku? 😜

Congratulation for making this far! 🎉 You have learnt how to deploy Rails app on a bare VPS using git flow.  

{% comment %}
https://www.digitalocean.com/community/tutorials/how-to-deploy-a-rails-app-with-git-hooks-on-ubuntu-14-04
{% endcomment %}