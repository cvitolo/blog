---
title: "SSH without password"
date: 2012-09-14 08:23:41+00:00
---

Bored of typing a password to access your remote desktop? Use a key!

Let's say you are user1 and are using machineA.

You want to access machineB as user2.

Generate key pairs:

    
    user1@machineA:~>ssh-keygen -t rsa
    
    user1@machineA:~> ssh user2@machineB mkdir -p .ssh         
                      enter user2@machineB's password:
    user1@machineA:~> cat .ssh/id_rsa.pub | ssh user2@machineB 'cat >> .ssh/authorized_keys'


If the ssh port is not the standard 22 and you use ip address rather than hostname

    
    user1@machineA:~> cat .ssh/id_rsa.pub | ssh <strong>-p 0000 user2@ip.address</strong> 'cat >> .ssh/authorized_keys'
                      enter user2@machineB's password:            (one last time, I promise!)
    
    user1@machineA:~> ssh user2@machineB hostname
                      or
                      ssh <strong>-p 0000 user2@ip.address
    
    </strong>


More info [here](http://www.linuxproblem.org/art_9.html)
