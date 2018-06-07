---
layout: post
title:  "Rails starter pack cookbook (Passenger, Nginx, PostgreSQL, RVM) for Chef and how to use it"
date:   2016-09-11 21:10:10
categories: post
permalink: "automate-server-provisioning-chef-html"
---



After typing the same installation command over and over again from [DigitalOcean](https://m.do.co/c/f7f1b47b1fff)'s tutorial to provision rails server. I got tired and thought that there must be a better way to do this. I stumbled upon [Chef](https://www.chef.io/chef) and [Chef-Solo](https://github.com/matschaffer/knife-solo) while Googling on how to automate server provisioning. It got me interested especially it uses Ruby language to define system configuration and you can also insert bash/shell command inside the ruby code.

Before continuing, here are some keywords used in Chef :  

## Chef Terminology  

**Recipe** - A ruby file that contains instruction to install component, usually just one component per recipe.

**Cookbook** - A group of recipes, for example the cookbook used in this post will contain recipe for RVM, Passenger, Nginx and PostgreSQL.

**Node** - A remote server that will be used for provisioning.  

**Attribute** - Parameter in key value pair in either node or role file used to customize the variable of a recipe. e.g : `node['server']['domain_name']` attribute is used to set the domain name for the server to be provisioned.

## Why I wrote this cookbook
I followed tutorial online and also referenced [Reliably deploying Rails application book](https://leanpub.com/deploying_rails_applications) to learn the basics of Chef. While the book suggest to use battle tested cookbooks from the official [Chef supermarket](https://supermarket.chef.io/), I have a hard time finding a working RVM recipe as the official one from the supermarket is fairly complex and the documentation doesn't specify clearly on how to use it. I also wanted to learn to write a cookbook/recipe from scratch to understand better how Chef works. The rails-server-starter-pack cookbook public repository is [located here](https://github.com/cupnoodle/rails-server-starter-pack.git).

## Don't know how to use Chef? No worries!

This post is written for developers who have no prior experience on using Chef/Knife-solo but want to automate the provision/installation of Rails server.

## Post disclaimer
This post will just explain enough Chef command to run and provision your server, I suggest reading the [Chef series written by Vladi Gleba](http://vladigleba.com/blog/topics/chef-series/) to understand more on how Chef works. I am not a DevOps expert and this cookbook might not follow some well known pattern, this cookbook worked for my small projects but it may not be suitable for large scale deployment.



## Step Zero - Install Chef, Knife-Solo  gem
We will need to install **chef**, **chef-zero**, **knife-solo** and **berkshelf**  gem.

`gem install chef`  
`gem install chef-zero`  
`gem install knife-solo`  
`gem install berkshelf`   

Chef, chef-zero and knife-solo (chef-solo) is used to automate the provisioning/setup of server while berkshelf is used to manage external cookbook list, similar like a Gemfile/Bundler for a ruby project.

After installing, we will initialize a chef project by typing :  
`knife solo init rails_chef`  

This will create a new directory with some barebone folder for the chef project. Lets move to the newly created directory.  
`cd rails_chef`  

We will then  download the [rails-server-starter-pack](https://github.com/cupnoodle/rails-server-starter-pack) cookbook into the **site_cookbooks** folder, this folder is used to contain custom cookbooks that is not listed on the official [Chef supermarket](https://supermarket.chef.io/). 

`cd site_cookbooks`

`git clone https://github.com/cupnoodle/rails-server-starter-pack.git`

Now we have the cookbook ready, let's create a new ubuntu server on your favorite cloud VPS provider. My favorite is [DigitalOcean](https://m.do.co/c/f7f1b47b1fff).   



## Step One - Create a Ubuntu server

We will create a Ubuntu 14.04 server with 512 MB RAM and attach the SSH key of our local machine to the server.

![Server OS](https://littlefoximage.s3.amazonaws.com/post26/server_os.png)

![Server memory](https://littlefoximage.s3.amazonaws.com/post26/server_size.png)

![Server ssh keys](https://littlefoximage.s3.amazonaws.com/post26/server_keys.png)



If you didn't select the ssh key before server creation, you can upload the public key of your local machine to your server using this command :  

 `ssh-copy-id root@YOUR_SERVER_IP`  



## Step Two - Prepare the server for Chef

Change your current directory to the **rails_chef** folder we created earlier.  
`cd rails_chef`

Run the following command to install chef client in the server.   
`knife solo prepare root@YOUR_SERVER_IP`

![chef prepare screenshot](https://littlefoximage.s3.amazonaws.com/post26/chef_prepare.png)

After installing the chef client in your remote server, notice that there is a new file named **YOUR_SERVER_IP.json** inside the **nodes** folder in the **rails_chef** directory.

Open the file **YOUR_SERVER_IP.json** using your favorite text editor, it will be similar to this : 

![empty node json file](https://littlefoximage.s3.amazonaws.com/post26/blank_node_json.png)



The **run\_list** array means the recipe (task) that will be executed on the server. We need to insert recipes from the rails-server-starter-pack cookbook into the run_list so it will look like this :

_YOUR\_SERVER\_IP.json_

```json

{
  "run_list": [
    "recipe[rails-server-starter-pack::default]",
    "recipe[rails-server-starter-pack::users]",
    "recipe[rails-server-starter-pack::ssh]",
    "recipe[rails-server-starter-pack::rvm]",
    "recipe[rails-server-starter-pack::passenger]",
    "recipe[rails-server-starter-pack::postgres]",
    "recipe[rails-server-starter-pack::railsapp]"
  ],
  "automatic": {
    "ipaddress": "YOUR_SERVER_IP_HERE"
  }
}
```

The default, users, ssh, rvm, pasenger, postgres and railsapp recipe will be executed.



Before executing these recipe, we need to replace some default attributes first. The default attributes are located in **rails_chef/site-cookbooks/rails-server-starter-pack/attributes/default.rb** , the file contents look like this :   

![default attributes](https://littlefoximage.s3.amazonaws.com/post26/default_attributes.png)



The default values are stored in a key-value hash format in the ruby file. To override it, we have to write some json following the key-value hash format in the **nodes/YOUR\_SERVER\_IP.json** file. 

Let's say we want to change the system user's and database's user username to **cupnoodle** , password to **your\_password\_here** and ssh port to **2020**, we will add these attribute in the **YOUR\_SERVER\_IP.json** file.

_YOUR\_SERVER\_IP.json_

```json
{
  "user":{
    "name": "cupnoodle",
    "password": "$1$7jLcg29f$h1YrnnlifGi44BLpoN35x0"
  },

  "ssh":{
    "port": "2020"
  },

  "db":{
    "root_password": "your_password_here",
    "user":{
      "name": "cupnoodle",
      "password": "your_password_here"
    }
  },

  "railsapp":{
    "name": "tasty_app",
    "server_name": "tasty.vul.io",
    "secret_key_base": "c6f216b5a7af1860d1babbb0700aadd69dda7a4c96012e5bc1aad43f829bfadf82518f3c35c506054172a4794ac4d1509ebcd6c9e50943fb388dc2f8ff61c848"
  },

  "run_list": [
    "recipe[rails-server-starter-pack::default]",
    "recipe[rails-server-starter-pack::users]",
    "recipe[rails-server-starter-pack::ssh]",
    "recipe[rails-server-starter-pack::rvm]",
    "recipe[rails-server-starter-pack::passenger]",
    "recipe[rails-server-starter-pack::postgres]",
    "recipe[rails-server-starter-pack::railsapp]"
  ],
  "automatic": {
    "ipaddress": "YOUR_SERVER_IP_HERE"
  }
}
```

Notice that the user ssh password is a shadow hash of the plain text, you can generate it using this command :  
`openssl passwd -1 "plaintextpasswordhere"`  

The full list of the default attribute can be [viewed here](https://github.com/cupnoodle/rails-server-starter-pack#attributes), override them accordingly especially the user password and database credentials. 



## Step Three - Cook the server using Chef 

No, not using oil and fire. The 'cook' means executing the command/recipe. After overriding the attribute, change your current directory to the **rails_chef** directory again.  

`cd rails_chef`  

Then cook the server using this command :   

`knife solo cook root@YOUR_SERVER_IP_HERE`

This will take around 15-20 minutes, you can grab a coffee or tea at the mean time. ☕️ 🍵



## Step Four - Git push and deploy

After chef has finished cooking, open your favorite web browser and head to your server IP address or domain name. You will see a placeholder rails app like this :  

![yay rails](https://littlefoximage.s3.amazonaws.com/post26/placeholder_rails.png)

Yay! You have successfully setup a Rails stack server! All that left is to push your rails app from your local machine to the remote server.  
Navigate to your rails app directory in your local machine  
`cd my_tasty_rails_app`  


Add a git remote that points to the server in this format :  
<script src="https://gist.github.com/cupnoodle/426a49140333e35d1086e764765205a5.js"></script>

Replace the **username** with the ssh user created just now, **your\_server\_ip** with… your server IP address, **port** with your SSH port, **app\_name** with the rails app name you entered in the json earlier.    



Following the configuration I used for the chef just now, my git remote command looks like this :  

```bash
git remote add live ssh://cupnoodle@tasty.vul.io:2020/home/cupnoodle/repo/tasty_app.git  
```

After adding the remote, git commit and push it to remote :  
`git push live master`



You will see the server running deploy command such as  **bundle install**, **rake db:migrate** and **rake assets:precompile**  etc.


And now open your server IP or domain name using web browser again, your Rails app is live now 🍻! Next time just git push to your server for any new changes. Feels like Heroku now didn't it? Just way cheaper 😝



p/s: Remember to copy the **application.yml** to _/home/[username]/[app\_name]_ before git push if you are using Figaro gem.  

<script src="https://gist.github.com/cupnoodle/470610232117e33a98450b4859c5ec13.js"></script>
