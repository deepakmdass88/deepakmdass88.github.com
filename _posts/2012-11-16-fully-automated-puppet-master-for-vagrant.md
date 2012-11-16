---
layout: post
title: "Fully Automated Puppet Master For Vagrant"
description: ""
category: virtualization 
tags: []
---
{% include JB/setup %}

In my previous blog, i expalined about how set up ***Vagrant*** with Single as well as Multiple VM's. I saw [@mitchellh's](https://twitter.com/mitchellh) talk at [PuppetConf 2012] (http://www.youtube.com/watch?v=UTQQggVx4sI), where he was explaining about `Automated Puppet Master` using *shell* provisioning. We can use the *shell* provisioner to execute *bash* scripts. My script will add the ***puppetlabs*** *apt* repository and it will install the *puppet* packages. Since i'm not running puppet master in daemon mode, i will be installing `puppet` and `puppet-common`, and i will be using ***puppet master*** option for starting the **puppet** master.And it will also clear all host certificates and it will generate new one's.

So firt we need to add the the provisioning option in the `Vagrantfile`.

	config.vm.define :ubuntu do |ubuntu_config|
    	  ubuntu_config.vm.box = "precise32"
	config.vm.provision :shell, :path => "bootstrap.sh"
  	end

Where `bootstrap.sh` is the bashscript. Below is the content of the script.

	#! /bin/bash

	hname=`hostname`
	domain="put_ur_domain"
	fqdn=`echo $hname.$domain`
	domainname $domain
	repo="deb http://apt.puppetlabs.com precise main"
	stat=`grep puppetlabs /etc/apt/sources.list > /dev/null ; echo $?`
	if [ $stat -eq 0 ]
	then
	echo "Repo is already added"
	else
	echo "not added --- adding"
	echo $repo >> /etc/apt/sources.list
	apt-key adv --keyserver keyserver.ubuntu.com --recv 4BD6EC30
	apt-get update
	echo Y | apt-get install puppet puppet-common
	fi
	puppet cert clean --all
	puppet cert generate $fqdn

If we don't want to regenarate the certificates everytime when the VM starts, then we can put a simple **if**, os that if will check whether the certificate already exists, if yes it will skip certificate generation else it will generate a new certificate for the master.

Since i dont't run puppet in daemon mode during testing, i will be using the below command for starting the master

	$ puppet master --verbose --debug --no-daemonize

or else we can install puppet master package itself and we can enable the puppet master to start in daemon mode itself. 
 
