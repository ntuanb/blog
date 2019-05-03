---
layout: post
title: Continuous Deployment With Codeship
author: tuan
categories: [tutorial, software]
image: assets/images/2016/codeship.jpg
---

> Continuous delivery (CD) is a software softwareing approach in which teams produce 
> software in short cycles, ensuring that the software can be reliably released at any 
> time. It aims at building, testing, and releasing software faster and more frequently. - Wikipedia

Using Codeship as our deployer, create a new project and attach a git 
repository to the chosen project.

After doing so, go into the **General** options, grab the public SSH key and paste it
the server at `~/.ssh/authorized_keys`. Each project requires a seperate key attachment
as they point to different folders on the server.

In Codeship, select a custom script deployment and add the following line
`scp -rp ~/clone/* root@123.456.789.0:/var/www/blog.tuanb.me/`. By doing so, we can now
allow the entire repo to be cloned onto the server everytime we make a push to the 
desired branch (preferably `master`) in the deployment pipeline.

More commands can be added such as `composer install` or `apt-get update` but we must
ssh in with `ssh root@123.123.123.123`, to allow more automation. Another option is to 
create a script file and run that script file instead.

After it is all set up, we can now deploy to the server after every push rather using a file
manager to manually copy over files or ssh into the server to run git pull. Certain hooks
can be added into the pipeline and tests can also be ran for each push to master.

Codeship is one of many continous deployment applications out there.