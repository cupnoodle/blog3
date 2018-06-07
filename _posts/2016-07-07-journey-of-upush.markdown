---
layout: post
title:  "Journey of My Side Project - uPush"
date:   2016-07-07 11:10:10
categories: post
permalink: "journey-of-my-side-project-upush"
---


<img class='centered' src ="https://littlefoximage.s3.amazonaws.com/post23/uPushLogo.png" alt="uPush Logo" /> 


## How It Started
During my final year in university (2015), an external digital agency collaborated with my university and organized a mobile app competition. I participated the competition with two of my friend and started building the iOS app and backend server together. The inspiration of the app came from the frustration and annoyance I faced with my university online learning portal, which was a customized [Moodle](https://moodle.org/) system.  

## Problems
There are few problems with the customized Moodle system, first being not mobile responsive, it's a minor problem but student usually don't bring laptop to university as most of us walk/cycle to campus and laptop is heavy. Downloading lecture note from the portal using mobile is a pain which I have to zoom in every time. The second problem is that some lecturer often post class cancellation notice on the portal at last minute, which we will only know after we check the portal usually after arriving campus. I got really irritated with this problem and wished that I can receive a notification whenever a new notice is posted by the lecturer.

## Implementation

Feel free to skip this part if you are not into technical stuff 😅.  We named the app **uPush** as a short form for **u**niversity **Push**, as the app will notify you for the latest notice/lecture note that lecturer note has posted to the university portal.

### Mobile app
I was interested with iOS programming that time and took this opportunity to learn Objective-C and basics of iOS programming (delegate, NSURLConnection, Push Notification, Grand Central Dispatch etc). I wanted to build an app that can download lecture note and view announcement/notice from the portal, with notification feature whenever a lecturer post a new announcement/notice. Here are some screenshot of the completed app : <br/><br/>
<img class="screenshot" src="https://littlefoximage.s3.amazonaws.com/post23/uPush1.jpg" alt="uPush screenshot" /> 
<img class="screenshot" src="https://littlefoximage.s3.amazonaws.com/post23/uPush2.jpg" alt="uPush screenshot" />
<img class="screenshot" src="https://littlefoximage.s3.amazonaws.com/post23/uPush3.jpg" alt="uPush screenshot" />
<img class="screenshot" src="https://littlefoximage.s3.amazonaws.com/post23/uPush4.jpg" alt="uPush screenshot" />

### Backend
To retrieve the data from the web portal, I coded a web crawler to log into the portal and get data from it. I used ruby [Mechanize](https://github.com/sparklemotion/mechanize) for the web crawling. The portal didn't have any anti web crawler measure at that time so any POST request to its endpoint will be treated as a normal login request. I also coded an API using [Rails Metal Controller](http://api.rubyonrails.org/classes/ActionController/Metal.html) to let the mobile app retrieve data from the server, a metal controller is used to reduce the resource loaded by rails. 

## The Competition
We were given around 3 months to code the mobile app. After submission, we need to present the app to the judges which comprises two staff from the digital agency and one lecturer from our university. We were pretty confident that we are gonna win the first prize as we coded both the backend and mobile app and we have quite some beta user whereas other teams only developed mockup/prototype of the mobile app with simulated data. We presented the app functionality, show the judge the number of beta users and positive reviews we received from the users. But in the end we didn't win and got a consolation prize instead, I were pretty shocked and saddened that our app lost to some crowdsource story telling app and retail price comparison app with fake data. The judges valued idea more than implementation and we admit that perhaps our idea wasn't that good enough but our app was already production ready that time.  

The lost blows a hard hit to my confidence towards the app, I stopped the development and shut down the backend server shortly after the competition. But two weeks after the competition, I received an email from the digital agency which they expressed interested on the app and would like to  meet up to discuss further on financing and licensing. Honestly I didn't expect this as we didn't even get second runner up for the competition, although we did come up with some business model during the competition. They offered around RM 5000 ([~ 1250 USD](https://www.google.com/search?q=5000+myr+to+usd) ) to fund the development of the app and they wanted 60% of the profit generated from the app itself, they boasted that it is a very generous offer as other agency might even take 70-80% profit cut. We rejected the offer almost immediately as we felt the agency has either underestimate the time needed or the cost needed for the development. 💸  
I regained some confidence after the meeting with the agency, it validated that there exist people who are willing to throw money 💰 at this app. I spinned up the backend server again and gave out beta access of the iOS app around my peers, I didn't further develop the app at this point.

## Reignition
After few months of the competition,  I have graduated from my university. Then a junior (Joshua) shown me an Android app mockup he did for uPush. He was really interested regarding to the app as it provided convenience to other students but at that time there wasn't an Android version and he was using an Android phone. He is really good in coding and he learn really fast, we agreed to let him do the Android app for uPush. During the development of the Android app, we also added timetable feature which student can check the timetable and schedule a reminder before the class, of course the timetable data is retrieved via scraping the university portal. I am really hyped during this period and I never thought that there were people interested enough to help the development of this app.

After few weeks of passing the Android .apk file around our friends for beta testing, we felt that it was time to release it on the Google Play Store to let all of the student from my university know about it. We promoted the app via word of mouth through facebook and we got 600+ sign up  the day after the release : 
![uPush release second day](https://littlefoximage.s3.amazonaws.com/post23/serveronfire.png)
I didn't expect such overwhelming response and the backend server was logging 90% RAM usage which I can't even SSH into it due to insufficient RAM. I used [DigitalOcean](https://m.do.co/c/f7f1b47b1fff) 1GB RAM droplet for the backend server at that time. The server probably looks like this at that time :   
<figure style="text-align:center;">
<img class="centered" src="https://littlefoximage.s3.amazonaws.com/post23/serveronfire2.jpg" alt="Server on fire" />
<figcaption>Screencap from Silicon Valley</figcaption>
</figure>  
To accomodate the load, I had to temporarily turn off the server at midnight and scaled it to 2GB RAM, praise the flexibility of cloud computing ☁️ . After the initial fever, we were getting 50-100 daily new user and 400-700 daily active users. We were really excited about the app and response it received.

## Rival
Not long after the release of uPush, we started seeing the appearance of [WBLE Edition](https://www.facebook.com/wbleedition/), an app which did the exact functions like us, appeared in facebook feed. The app is also developed by a student in the same university and he started promoting in facebook. This further validated our idea as we saw competitor started to appear, instead of worrying about our competitor, we feel more hyped.  

## Request for collaboration which didn't turn well
After the well received response, I emailed the university about uPush app and asked for collaboration as the app is scraping the university data and around **10% of the university student population are using uPush at that time**. I thought of asking them to develop API for retrieving the information inside the portal which can lessen the burden of both my scraping server and their portal server. 

Of all the features, they only focused on the part which uPush app asked for student login credential, they were very frantic fearing that I might save these credential for future misuse/abuse. They expressed interest to buy the full rights of the app from us and wanted us to stop the development but we declined as we wanted to continue developing/maintaining it. I assured them that the backend server does not store any credential and all of the communication between server and mobile app is encrypted with HTTPS. I even thought of sharing source code with them but they already started to implement anti-scraping measure on the portal almost instantly after the initial email.

## Circumventing the gate

This part is kinda fun to me, the sense of accomplishment you get when breaking defense imposed by university felt really nice. I guess this is the reason why most hacker started hacking, the feel you get when you break through imposed locks can't be triumphed by any monetary gain.

### User agent checking
At first they implemented user-agent checking as I didn't spoof the user-agent header for the web scraper. User started to report that the app isn't working, any request that was sent to the portal without a legit looking user-agent was denied with HTTP status code 403. This was easily circumvented with two lines of code :  
<script src="https://gist.github.com/cupnoodle/25326dd3d35c6a2d002e01ad84b9a7de.js"></script>

### Constant cookie value setting and validation
They probably noticed the web scraper was still scraping using legitimate user agent now as I didn't change the server IP address. In the login web page they added some javascript code like this :  
<script src="https://gist.github.com/cupnoodle/33d766e88bc86be09cccaa4eb5f6cf17.js"></script>
Then the login server will check if the cookie value matched. Circumventing this is fairly easy as the correct value is already shown in the source of the login web page lulz. I edited the web scraping script to include the cookie value and it works again:  
<script src="https://gist.github.com/cupnoodle/170a0372c33415ff59ae8aa1de7e6aba.js"></script>

### Dynamic cookie value setting and validation
It took a while for them to realize that the uPush web scraper is still accessing the university portal, I still haven't change the scraping server IP address at this point. This time they got smarter and used timestamp plus some dynamically generated characters based on the timestamp, like this :  
<script src="https://gist.github.com/cupnoodle/7a064bc172545050591bd493ce3facb7.js"></script>  
Circumventing this is kinda tricky as I need some javascript parser to parse the javascript in the login web page source code. I used [therubyracer](https://github.com/cowboyd/therubyracer) gem to parse the javascript. Fairly tricky and increased CPU consumption but it works.  

### IP Address ban
Not long after the game of chase of javascript cookie validation (they changed the formula/structure of the javascript cookie intermittently), the long awaited ban hammer was finally delivered. They blocked the IP Address of my scraping server and all request from the server was greeted with HTTP status 403 again. I feel glad that I live in a cloud era where server can be destroyed or created almost instantly thanks to virtualization technology. Shortly after the IP ban, I made a backup of the existing scraper server image and spin up another droplet (server) in [DigitalOcean](https://m.do.co/c/f7f1b47b1fff) with different IP address loaded with the backup image. I removed the old server and pointed the domain name to the new server IP address, uPush is back working again within 5 minutes lulz.

## Game over

Good days don't last, we knew captcha will be implemented eventually and circumventing it is beyond our technical capacity and time resources. The university has used the trump card, they added Google Recaptcha at the login page, it looks like this now:    
<img class="centered" src="https://littlefoximage.s3.amazonaws.com/post23/recaptcha.png" alt="Recaptcha Login!" />  
Now looking back, I wonder why they didn't implement captcha first compared to other measure. Maybe they don't want to degrade the user experience and captcha was their last resort?  

We had no choice but to stop the operation of uPush and posted a [statement](https://www.facebook.com/upushie/posts/1584823265181540) in the facebook page. We didn't take down and left the app in Play store/App store and started getting negative review on why the app doesn't work anymore. 😭  

We started receiving emails from users inquiring about why the app doesn't work, and a [Github Issue](https://github.com/sweatshops/uPush_APK/issues/2) was filed too.

## Epilogue
This side project is hella' roller coaster ride, we experienced many euphoria and depressing moments. We got offered twice but we declined it without hesitation, we didn't regret it but some of our friends said we should have taken the offer, welp 😓. The side project can be considered a failure but during the development of this app I have learnt how to build an API for mobile consumption, some SysAdmin know-how, some basic Objective-C , met few supporting peers, and also mustered up the courage to contact people/university.  **But more importantly, reading play store review of users thanking us for making their university life more convenient is really priceless**, we felt really happy that we solved a little problem for them.

Special thanks to my teammates [Joshua](https://github.com/joscmw95), [Munyee](https://github.com/Munyee), [Guang Xing](https://github.com/GXLye) for contributing to uPush.

In case you are wondering, here is the [Android version of uPush](https://play.google.com/store/apps/details?id=me.sweatshop.upush) and [iOS version of uPush](https://itunes.apple.com/us/app/upush/id981950322).  

TL;DR : I created an app which forced my university to implement captcha on their portal login page, and in this process I got offered twice to sell the app.  
{% comment %}
{% endcomment %}