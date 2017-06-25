---
title: "Set up an Amazon Web Service (AWS) Free Usage Tier"
date: 2012-07-04 20:02:46+00:00
---

Amazon allows you to use a computer in the cloud for free (for 1 year).

To have a go, visit http://aws.amazon.com/free/ sign up then log in.

This is your AWS Management Console:

[caption id="attachment_8" align="alignnone" width="300"][![](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-1.png?w=300)](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-1.png) AWS Management Console[/caption]

Click on EC2 and follow the instructions to launch a virtual server, also called Amazon Elastic Compute Cloud (Amazon EC2) instance.

[caption id="attachment_9" align="alignnone" width="300"][![](http://aquaresearcher.files.wordpress.com/2012/07/screenshot.png?w=300)](http://aquaresearcher.files.wordpress.com/2012/07/screenshot.png) Create a new instance[/caption]

Just to make things easier, choose the "Quick Launch Wizard", choose a name for your instance and create a new key pair.

Download the key (e.g. webserverkey.pem) and save it in a folder, e.g. `~/.ssh`.

To ensure the user can read the key, go to terminal and type: `sudo chmod 400 ~/.ssh/webserverkey.pem`.

Then choose a launch configuration, e.g. Ubuntu Server 12.04 LTS - 64 bit and click CONTINUE.

[caption id="attachment_11" align="alignnone" width="300"][![](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-2.png?w=300)](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-2.png) Choose the instance[/caption]

Now you can either edit the details of the newly created instance or launch it.

[caption id="attachment_13" align="alignnone" width="300"][![](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-3.png?w=300)](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-3.png) Details of the instance[/caption]

When ready, click on LAUNCH then CLOSE.

[caption id="attachment_14" align="alignnone" width="300"][![](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-4.png?w=300)](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-4.png) The instance is ready[/caption]

You will be automatically redirected to you AWS Management Console which now shows the new instance in the section "My Resources".

If it doesn't, just click on REFRESH.

[caption id="attachment_15" align="alignnone" width="300"][![](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-6.png?w=300)](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-6.png) Console[/caption]

Your instance is ready to use!

To stop or terminate a running instance see read this:

http://support.rightscale.com/06-FAQs/FAQ_0149_-_What%27s_the_difference_between_Terminating_and_Stopping_an_EC2_Instance%3F
