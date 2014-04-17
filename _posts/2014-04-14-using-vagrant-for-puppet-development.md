---
layout: post
title: "Using Vagrant for Puppet Development"
modified: 2014-04-14 10:35:41 -0700
tags: [vagrant, puppet]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---
Recently at work, my group has been tasked with creating Puppet manifests for the projects we're working on. This has led to a lot of issues with how people develop their manifests. I've found that using Vagrant to initialize and run Puppet has been the easiest way to accomplish this. 

## Install VirtualBox
Virtualbox is an opensource virutalization product. To install head to: [Virtualbox.org](http://virtualbox.org)

## Install Vagrant
Vagrant allows you to build portable work environments on top of Virtualbox. It removes the manual steps of creating new virtual machines, each time you want to test a run of puppet. 
You can install Vagrant from: [vagrantup.com](http://www.vagrantup.com)

## Install Puppet
You'll need Puppet to develop puppet. You can find a copy of puppet at: [puppetlabs.com](http://puppetlabs.com)

## Add a Base box
A base box is a base Virtual Machine image that Vagrant uses to initialize each working environment. You can find a list of base boxes available at [vagrantcloud.com](http://vagrantcloud.com). I've found a base CentOS box provided by "Chef" and will initialize it with the following:

~~~
vagrant box add --name puppetdev http://puppet-vagrant-boxes.puppetlabs.com/centos-65-x64-virtualbox-puppet.box
~~~

## Create new Puppet Module
We'll use the built in Puppet module generator to create a base project.

~~~
puppet module generate <name of module>
~~~

## Initialize Vagrant
Vagrant needs a few configuration files to know how to run. These configuration files can be created by running ``vagrant init`` in your project folder.

~~~
vagrant init http://puppet-vagrant-boxes.puppetlabs.com/centos-65-x64-virtualbox-puppet.box
~~~

This will place a file name ``Vagrantfile`` in whatever folder you run the ``init`` command on. Configuring Vagrant is outside of the bounds of this post, I would recommend spending some time and getting familiar with the workings of the ``Vagrantfile``. I've configured my Vagrant to add a box that's provided by Puppetlabs. You can get a list of Puppetlab created boxes at [http://puppet-vagrant-boxes.puppetlabs.com/](http://puppet-vagrant-boxes.puppetlabs.com)

## Start Vagrant
You can start your Vagrant vm by running ``vagrant up`` in your project folder. It will start a VM and make it available to SSH via the command ``vagrant ssh``. 

## Develop!
One of the neat things about Vagrant is that it uses Virtualboxes "Shared Folders" technology to automatically make your project folder available in the VM. After using "vagrant ssh" to log into your VM, you can go to "/vagrant" to view your project files. This means you can edit your project files in whatever local editor suits your fancy and run your puppet manifests inside your Vagrant VM.

When you think you've gotten your manifests to a nice place.. You can run ``vagrant destroy`` to completely wipe your Vagrant VM, then ``vagrant up`` to create a clean VM to try running your puppet scripts against. 
