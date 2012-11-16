---
layout: post
title: "FPM - A must have Packaging Tool"
description: ""
category: "ubuntu/debian" 
tags: []
---
{% include JB/setup %}


***fpm*** is a wonderful tool for building our own packages. It's very simple to use. I'm a qmail user, since our [deepOfix Mail Server](http://www.deeproot.in/deepofix) is built on that. So in many situations i've to compile the qmail from the source along with custom patches. And one main thing about qmail is that it need a bunch of files in the *control* folder for its working. 



Since we are automation freaks, we even wrote a *puppet* module for qmail. But then i came to know about the ***fpm***. Since it supports pre and post scripts, i can automate so many things not only creating the control files, but also adding necessary run scripts to run as a *runit* service.

fpm is very simple to install:

      $ gem install fpm

`fpm --help` will give us a good description about the options.

So in my case, i've already compiled my patched Qmail into `/var/qmail` folder. I've a few dependencies **(libssl-dev,openssl,zlib1g-dev,libldap2-dev)** and also **runit**, because i will be using the post script option to start runit services automatically. 

My prescript will just add the necessary Qmail users.

	 addgroup --system nofiles
	 addgroup --system qmail

	 adduser --system --home /var/qmail/alias/ --shell /bin/false --no-create-home --ingroup nofiles alias
	 adduser --system --home /var/qmail/ --shell /bin/false --no-create-home --ingroup nofiles qmaild
	 adduser --system --home /var/qmail/ --shell /bin/false --no-create-home --ingroup nofiles qmaill
	 adduser --system --home /var/qmail/ --shell /bin/false --no-create-home --ingroup nofiles qmailp
	 adduser --system --home /var/qmail/ --shell /bin/false --no-create-home --ingroup qmail qmailq
	 adduser --system --home /var/qmail/ --shell /bin/false --no-create-home --ingroup qmail qmailr
	 adduser --system --home /var/qmail/ --shell /bin/false --no-create-home --ingroup qmail qmails


My post-script will just populate qmail control files like `defaultdomain`,`locals`,`rcpthost` and also create a runit service for ***qmail-send*** and ***qmail-smtpd***

Now we just need to build the debian package.

	$ fpm -s dir -t deb --before-install preinst.sh --after-install postinst.sh -d "runit (>= 2.0)","openssl (>= 1.0)","libssl-dev (>= 1.0)","libldap2-dev (>= 2.4)","zlib1g-dev (>= 1.2)" -n qmail /var/qmail/

where `-s` is for *source*, `-t` for *package type*,`-d` for dependencies and `-n` for name of the package. There are som many other options also which includes desription etc. But for the time being i'm using very basic options for testing. The above command will build a debian package for me. That's it pretty simple.
