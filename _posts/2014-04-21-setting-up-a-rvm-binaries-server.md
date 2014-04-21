---
layout: post
title: "Setting Up a RVM Binaries Server"
modified: 2014-04-21 11:09:28 -0700
tags: [RVM, Ruby]
image:
  feature: 
  credit: 
  creditlink: 
comments: true
share: true
---

At work I'm limited on the online resources I can use for installing software. Most of our systems are blocked from getting out to the internet in general. This creates a problem when we need to install software. It seems that we have more Ruby projects on different versions and need a way to install different versions of Ruby easily. We don't have build tools on our productions boxes so we can't build from source. This leaves us with using prepackaged binaries. 
We've decided to use RVM since some of servers have mulitple projects. Since we're unable to reach the internet, we have to create our own binaries server for the versions of Ruby we support. 

## Preparing a binaries server
Just setup a basic webserver with directory indexing turned on. There will be a directory structure built around the OS/Version/Arch.

## Prepare the binaries
You'll need a server that has internet access and is on the same OS/version as your production servers. 
You'll have to install the version of Ruby you want then prepare the binary package. 

{% highlight bash %}
rvm install ruby-2.1.1
rvm prepare ruby-2.1.1
{% endhighlight %}

This will create a file called ruby-2.1.1.tar.bz2. This is your binary that you need to place on your server. You'll notice that after you run your ``prepare`` statement, you'll see a blurb like the following:

{% highlight bash %}
reparing ruby-2.1.1 ...
Prepared ruby-2.1.1.tar.bz2.
--- upload:
ssh "rvm@rvm.io" "mkdir -p ~/shared/public/binaries/centos/6/x86_64/"
scp "ruby-2.1.1.tar.bz2" "rvm@rvm.io:~/shared/public/binaries/centos/6/x86_64/ruby-2.1.1.tar.bz2"
--- rvm/config/remote:
http://jruby.org.s3.amazonaws.com/downloads/ruby-2.1.1.tar.bz2
--- rvm/config/md5:
http://jruby.org.s3.amazonaws.com/downloads/ruby-2.1.1.tar.bz2=8b48cfbb7d3ad3a37c9e48585ffaffcd
--- rvm/config/sha512:
http://jruby.org.s3.amazonaws.com/downloads/ruby-2.1.1.tar.bz2=9d9505dcf96ca4c2ba7300158142a4788c5f0814d81dc830dc339d286cee20b280082e24edc0b5779eaa37a2ab5dcc7843e39a0b5ca288c7e32bf995bb4653e5
{% endhighlight %}

Take note of the ``ssh`` line under ``upload``. That is the directory structure you'll need to use.
## Upload binary
Assuming that your webserver is pointing to ``/var/rvmrubies/`` create a the folder structure ``/var/rvmrubies/binaries/centos/6/x86_64/`` and upload ``ruby-2.1.1.tar.bz2`` there. You should now be able to use a Web browser to navigate to ``http://<server>/binaries/centos/6/x86_64/ruby-2.1.1.tar.bz2``. 

## Configure RVM
RVM stores it's configuration files at ``/usr/local/rvm/config``. The user override folder is located ``/usr/local/rvm/user/``. 
You will need to add your new binary to a ``remote`` file and to the ``db``

{% highlight bash %}
echo "http://<servername>/binaries/centos/6/x86_64/ruby-2.1.1.tar.bz2" >> /usr/local/rvm/user/remote
{% endhighlight %}

You should now be able to run ``rvm list remote`` and see your binary available. 
You will now need to add your RVM binary server to the acceptable list.

{% highlight bash %}
echo "rvm_remote_server_url=http://<servername>/binaries/" >> /usr/local/remote/user/db
{% endhighlight %}

## Add checksums
In the blurb after you prepared the binaries. It automatically generated MD5 and SHA512 checksums. IF you'd like to use them. Add them to the corresponding checksum files in /usr/local/rvm/user/

If you do not want to use the checksums. You can add the configuration:

{% highlight bash %}
echo "rvm_remote_server_verify_downloads=1" >> /usr/local/remote/user/db
{% endhighlight %}
