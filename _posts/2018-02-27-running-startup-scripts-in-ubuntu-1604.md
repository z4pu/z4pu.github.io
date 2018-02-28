---
layout: post
title: Running Startup Scripts in Ubuntu 16.04
date: 2018-02-27 16:00:00
description: Running Startup Scripts Ubuntu 16.04
categories: posts
hidden: false
comments: false
published: true
---

### Otherwise known as 'how I saved my eyes'

I spend most of my life in Ubuntu running on an old MacBook Pro. It runs pretty well for the most part, but it had the annoying habit of starting up at maximum screen brightness.

After a bit of Googling, I came up with the following script:

{% highlight ruby %}
#!/bin/bash
setpci -v -H1 -s 00:01.00 BRIDGE_CONTROL=0
echo 250 > /sys/class/backlight/gmux_backlight/brightness
{% endhighlight %}


250 works well for my purposes. Set it to something that doesn't cause you temporary blindness.

Running it myself each time became annoying, so I looked up how to get it to run automatically at startup.

It turns out that systemd has been the default init system for Debian since Debian Jessie. You should write a unit file for each of the script you want to run at startup, and pop that unit file in /etc/systemd/system. I saved my bash script as /usr/bin/brightness. And because you really shouldn't allow anyone to mess with your scripts, set the permissions as follows:

{% highlight ruby %}
sudo chmod 755 /usr/bin/brightness
sudo chown root /usr/bin/brightness
{% endhighlight %}

{% highlight ruby %}
sudo touch /etc/systemd/system/brightness.service
sudo nano /etc/systemd/system/brightness.service
{% endhighlight %}


This is what my brightness.service file looks like.
{% highlight ruby %}
[Unit]
Description=Control screen brightness
Before=runlevel2.target runlevel3.target runlevel4.target runlevel5.target shutdown.target
After=local-fs.target remote-fs.target systemd-journald-dev-log.socket
Conflicts=shutdown.target

[Service]
Type=forking
Restart=yes
TimeoutSec=5min
IgnoreSIGPIPE=no
KillMode=process
KillSignal=SIGTERM
GuessMainPID=no
RemainAfterExit=yes
ExecStart=/usr/bin/brightness

[Install]
WantedBy=multi-user.target
{% endhighlight %}


To reload the systemd and scan for new or changed units:

{% highlight ruby %}
systemctl daemon-reload
{% endhighlight %}


To get the service to start at boot, use the enable command

{% highlight ruby %}
sudo systemctl enable brightness.service
{% endhighlight %}


To stop the service from starting automatically:

{% highlight ruby %}
sudo systemctl disable brightness.service
{% endhighlight %}
