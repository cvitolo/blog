---
title: "Remote access to AWS instance"
date: 2012-07-05 20:11:28+00:00
---

If you need help to set up an AWS instance, read [my previous post](http://wp.me/p2yns8-7).

From the navigation menu of your Amazon EC2 console, click on INSTANCES.

[caption id="attachment_21" align="alignnone" width="300"][![](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-7.png?w=300)](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-7.png) AWS Console[/caption]

Tick the box to select the new instance and take a note of the web server's address, e.g. `ec2-00-000-00-000.eu-west-1.compute.amazonaws.com`.

[caption id="attachment_22" align="alignnone" width="300"][![](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-8.png?w=300)](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-8.png) Choose the instance[/caption]

Now go to terminal and type (I assume you are familiar with Secure SHell):

```
ssh -i ~/.ssh/webserverkey.pem ubuntu@ec2-00-000-00-000.eu-west-1.compute.amazonaws.com
```

(you should avoid to access an instance as root, for this reason the user "ubuntu" is automatically created).

If your terminal looks like the one below... well done! You successfully accessed your instance!

[caption id="attachment_26" align="alignnone" width="300"][![](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-91.png?w=300)](http://aquaresearcher.files.wordpress.com/2012/07/screenshot-91.png) Access through terminal[/caption]

You can also send files from your computer to the instance using Secury CoPy (scp), which syntax is exactly the same as ssh.

Imagine you want to transfer a text file called "test.txt" from your computer (user home folder, ~) to the amazon instance (ubuntu home folder, /home/ubuntu).

Then go to terminal and type:

```
scp -i ~/.ssh/webserverkey.pem ~/test.txt ubuntu@ec2-00-000-00-000.eu-west-1.compute.amazonaws.com:/home/ubuntu/
```

That's all!

More info [here](http://blog.taggesell.de/index.php?/archives/73-Managing-Amazon-EC2-SSH-login-and-protecting-your-instances.html)
