---
layout: post
title: Building your new CentOS box
author: Gary Rafferty
meta_keywords: CentOS VPS, Gary Rafferty
meta_description: Build and configure a new CentOS server
---
# {{ page.title }}
 

I tend to use Dediserve for my VPS solutions, and anytime I create a new box with them,  
I generally build it the same way. Most of the steps I've picked up along the way,  
or referenced from various sources likes blogs and forums.  
What follows is my usual workflow on a fresh CentOS 5.5 image. Your mileage may vary, but this  
should be handy as a guide.  

### First steps

Using the Dediserve client area, I usually choose a blank CentOS 5.5 image for provisioning.  
When the box is ready, the client area will provide you with the _initial_ root login and IP address.  
First thing to do is login, change the root password and set yourself up with a regular user account.   
 
```bash
  $: ssh root@your.i.p.address
```

Run the following command to change the root password to something more secure  

```bash
  $: passwd
```

Once you've changed the root password, we'll go and set the 'wheel' group to handle all sudo users  

```bash
  $: /usr/bin/visudo
```

and uncomment out the lines that looks likes this  

```bash
  ## Allows people in group wheel to run all commands
  # %wheel  ALL=(ALL)       ALL
```

Now go ahead and set yourself up a regular user account and add them to the 'wheel' usergroup.  

```bash
  $: /usr/bin/adduser gary
  ...
  ...
  ...
  ...

  $: /usr/bin/usermod -a -G wheel gary
```

### Public key and SSH permissions

Your public key resides in your _~/.ssh_ directory (assuming your on Linux that is; I'm sure Mac's are probably similar)  
so we need to push that up the server. I like using SCP as it's nice and simple so just issue the following command  

```bash
  $: scp ~/.ssh/id_rsa.pub gary@my.i.p.address:
```

As you can see, it's simply _scp FILE-TO-COPY USER@SERVER:_

On the server, then you can simply move that file to the correct location. To do so, you may need to create a .ssh directory

```bash
  $: mkdir ~gary/.ssh && mv ~gary/id_rsa.pub ~gary/.ssh/authorized_keys
```

That will create the .ssh directory and move the public key you uploaded to the correct location.

Finally, configure up SSH as you please

```bash
  $: vim /etc/ssh/shd_config
```

I usually, change the port and disallow root login but you can additionally set the authorized users and opt for a   
passwordless login. 

Next up, basic firewall.  

### Basic firewall and protection

I'm no expert on iptables, but came across the following on somewhere like Stackoverflow and stuck with it.

First, flush the existing setup

```bash
  $: iptables -F
```

Next, create a file for the rules

```bash
  $: touch /etc/iptables.up.rules
```

Copy the following and save it to the above file

```bash
  *filter
  -A INPUT -i lo -j ACCEPT
  -A INPUT ! -i lo -d 127.0.0.0/8 -j REJECT
  -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
  -A OUTPUT -j ACCEPT
  -A INPUT -p tcp --dport 80 -j ACCEPT
  -A INPUT -p tcp --dport 443 -j ACCEPT
  -A INPUT -p tcp -m state --state NEW --dport 30000 -j ACCEPT
  -A INPUT -p icmp -m icmp --icmp-type 8 -j ACCEPT
  -A INPUT -j REJECT
  -A FORWARD -j REJECT
  COMMIT
```

Make the changes permanent

```bash
  $: /sbin/iptables-restore < /etc/iptables.up.rules
  $: /sbin/service iptables save
```

h3. Finally, reload ssh, and verify you can login as your new user

```bash
   $: /etc/init.d/sshd reload
```

### All done,

Depending on what the box is for, my steps sometimes vary, but assuming you just want a server  
to do some development on or the likes of that, the above steps should have you on your way.
