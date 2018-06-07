---
layout: post
title:  "How to prevent hotlinking of images hosted on Amazon S3"
date:   2016-10-01 10:20:10
categories: [aws]
permalink: "prevent-hotlink-s3"
---

I host all the images used on blog post on Amazon S3. As Amazon S3 bandwidth cost me money 💰, I am paranoid and afraid someone is hotlinking my image on their server and keep refreshing their page 24/7 to eat up my bandwidth cost 😫. 

No worries, bucket policy to the rescue! Head over to the bucket containing your image, and click "**Edit bucket policy**".
![edit bucket policy](https://littlefoximage.s3.amazonaws.com/post27/bucketpolicyhere.png)

Paste this bucket policy end edit a little :  
<script src="https://gist.github.com/cupnoodle/08aca05a051f17ada9e4a74c2d00cbd9.js"></script>  
  
Edit the "**Resource**" key and replace <strong>arn:aws:s3:::littlefoximage/* </strong> with your s3 bucket name such as <strong> arn:aws:s3:::your_s3_bucket_name/* </strong> . If you want to specify a folder location instead of all folders/files, replace the "<strong>*</strong>" at the end with the path to your folders/files.  

Edit the "**aws:Referer**" key and add your website address(es) here, you can use "<strong>*</strong>" wildcard in front or end of the domain name.  

Click "**Save**" when you are done editing.  

Now when someone hotlink your image on unauthorized domain, they will be greeted with this error message :  
![access denied](https://littlefoximage.s3.amazonaws.com/post27/access_denied.png)  

Now you can sleep soundly at night without worrying someone stealing your S3 bandwidth 😝