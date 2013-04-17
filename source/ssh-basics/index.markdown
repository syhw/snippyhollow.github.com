---
layout: page
title: "ssh basics"
date: 2013-04-17 12:08
comments: true
sharing: true
footer: true
---

### Public key authentication

You can easily generate a private/public key pair by typing:

    ssh-keygen -t rsa

If you do not set a passphrase during the generation, you will be able to 
login without having to type a password. How so? You have to copy the content 
of `~/.ssh/id_rsa.pub` (`~` meaning your home folder) on the other machine you 
want to log to in `~/.ssh/authorized_keys` (by default). 
Say you are on machine `A` and log on to `B`, do the 
`ssh-keygen` command above on machine `A` and put the public key (ending in 
`.pub`) on machine `B`. WARNING: you should _never_ let the private key (the 
one not ending in `.pub`) leave machine `A`. You can put the public key where 
ever you want (even on public internet sites). There is a command to directly 
copy the public key and set the right files authorizations for you:

    ssh-copy-id -i ~/.ssh/id_rsa.pub remote-host

With remote-host being how you login to machine `B`. What? You are still 
loging in often to the same machine by typing `ssh username@ipaddress`?

### SSH config

So with public key authentication, you are way safer than using a password 
(which is easier to crack than 8 letters alpha-numeric passwords). Now for the 
easiness of use, the ssh client (on machine `A`) uses a config file, 
whose default path is `.ssh/config`. You can put all the servers you log to 
inside like that (one `Host=` and its following paragraph per server):

    Host=habilis
    Hostname=HABILIS_IP_HERE
    User=MY_USER_ON_HABILIS_HERE
    IdentityFile=~/.ssh/habilis 

The last line is optional and is used because I use a specific ssh 
private/public keypair for habilis. The path to the key that you should put 
here is for the _private_ key (on machine `A`).

