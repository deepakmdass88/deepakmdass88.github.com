---
layout: post
title: "Vagrant: Make Virtualization Easier"
description: ""
category: virtualization 
tags: []
---
{% include JB/setup %}

***Vagrant*** is a light weight virtualization tool, build over Oracle's `VirtualBox`. It's completely written in ruby and it's very easy to install and configure. The only dependency is `VirtualBox`. Once VirtualBox is installed, we can either use `RubyGems` to install `Vagrant` or we can get the installer from [Vagrant](http://downloads.vagrantup.com/). 

I prefer ruby gems, So

	$ gem install vagrant

That's it, it will install vagrant. Now we need to get the base box. Vagrant uses the base boxes to build the VM's. We can download the base boxes from [Vagrant Boxes](http://www.vagrantbox.es/). we can directly use the url to create the boxes, but it's alwasy good if we have a downloaded copy of the base boxes.

Basically we need to follow just 3 steps. *add*,*initialize*,*up*. So once we have the base box just add the base box.

	$ vagrant box add new_box_name our_downloaded_basebox_file

	example,
	$ vagrant box add mybox precise32.box

Now just do a **vagrant init** to create the `Vagrantfile`. This is the main configuration file. If we go through the `Vagrantfile`, we can see a bunch of options like port forward, provisioning, setting network and so on. If only one vm is required, then we just have to add the *new_box* name which we created at the `config.vm.box=` option int the `Vagrantfile`. And **vagrant up** will start the VM.

But one of the most important feature of ***Vagrant*** is it suports multiple VM's over one single box. But we have to define those VM's in the `Vagrantfile`. Below i've defined two VM's in my `Vagrantfile`, also comment out **config.vm.box=base**

	config.vm.define :ubuntu do |ubuntu_config|
    	 ubuntu_config.vm.box = "precise32"
	end
	config.vm.define :puppet do |puppet_config|
    	 puppet_config.vm.box = "precise32"
	end
Where *ubuntu* and *puppet* are the name of the VM's. And *precise32* is the name of the the box which i've created. Now, **vagrant up** will start all the VM's. But we can mention the name to start a specific VM. Like **vagrant up ubuntu**. It will start only the **ubuntu** VM.

Provisioning is another important feature of Vagrant. We can use ***puppet***,***chef*** and ***shell*** scripts to bootstrap the vm's. I saw [@mitchellh's](https://twitter.com/mitchellh) talk at [PuppetConf 2012](http://www.youtube.com/watch?v=UTQQggVx4sI&feature=BFa&list=PLV86BgbREluVFB73Wwqp_tCbw5Z9TMLX1), in which he mentioned about how to create a **Fully Automated Puppet Master** using the **shell** provisioner. I've tried this out using a shell script that will install puppet master and genrate the ssl certificates. It's working fine, soon i will post it here.
