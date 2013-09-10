<!--
title: Getting Started with Ansible on Digital Ocean
publish: 2013-09-08
slug: 2013/09/08/getting-started-with-ansible-digital-ocean
tags: Linux, Server Management, Ansible
-->

Full disclosure: any link to Digital Ocean has a referral code. If you signup to Digital Ocean and purchase anything, I get a little credit for you doing so.

If you have to manage more than one server with common software packages and configuration, you're going to want a server configuration management tool. Originally, I wrote shell scripts to handle this. However, shell scripts can have problems with error handling. Another benefit of most configuration management tools is that their operations are [idempotent](http://en.wikipedia.org/wiki/Idempotence) i.e. you can run any operation more than once and it won't have any effect. 

For the last few months, I've read tutorials on [Puppet][puppet], [Chef][chef], and [Salt][salt]. I took a liking to Chef, especially the Chef tool [Knife Solo](https://github.com/matschaffer/knife-solo). I finally discovered [Ansible][ansible] in the comments of a [Hacker News](http://news.ycombinator.com) post about Salt. If you're having trouble deciding whether you should choose Puppet, Chef, Salt, or Ansible then the eBook [Taste Test: Configuration Management Tools Get Tasted!][ebook] by [Matt Jaynes](http://mattjaynes.com/) will help you decide.

Ansible's primary benefit is that of simplicity and not requiring any software to be installed on the target machine. All you need is SSH enabled. I have two [Digital Ocean][do] VPS that I need to install software and configure.



Assumptions
-----------

You're running Mac OS X (should work with Linux) and you have Git and Python installed. These instructions are specific to [Digital Ocean][do] but should work with any remote servers offered by any vendor.



Installing Ansible
------------------

checkout latest development branch of Ansible:

    cd /tmp
    git clone git://github.com/ansible/ansible.git
    cd ./ansible
  

if you want a specific version, you can check that out too:

    git tag

outputs:

    ...

    0.8
    v0.9
    v1.0
    v1.1
    v1.2
    v1.2.1
    v1.2.2
    v1.2.3

to checkout a specific version:

    git checkout v1.2.3

or, as stated, if you want the latest, don't call `git checkout`

    sudo make install

according to [this](https://raymii.org/s/tutorials/Ansible_Deployment_Framework.html), you need to install some python Ansible dependencies:

    sudo easy_install jinja2 
    sudo easy_install pyyaml
    sudo easy_install paramiko



Configuring Ansible to Communicate with Servers
-----------------------------------------------

Let's assume that you have two servers named with IPs `1.2.3.4` and `4.3.2.1`. With Digital Ocean, unfortunately, you're insecurely emailed the root password to each server. Let's add our SSH key to each server.


### SSH Keys

if you don't have an SSH key, you can generate one really easily:

    ssh-keygen -t rsa -C "your_email@somedomain.com"

if you're running Linux, you can use `ssh-copy-id` to copy the key the remote servers:

    ssh-copy-id -i ~/.ssh/id_rsa.pub root@1.2.3.4
    ssh-copy-id -i ~/.ssh/id_rsa.pub root@4.3.2.1

if you're running on OS X, you won't have `ssh-copy-id`, here is an alternative:

    cat ~/.ssh/id_rsa.pub | ssh root@1.2.3.4 "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"
    cat ~/.ssh/id_rsa.pub | ssh root@4.3.2.1 "mkdir ~/.ssh; cat >> ~/.ssh/authorized_keys"

if you get an error about the `.ssh` directory existing, modify the previous to this:

    cat ~/.ssh/id_rsa.pub | ssh root@1.2.3.4 "cat >> ~/.ssh/authorized_keys"
    cat ~/.ssh/id_rsa.pub | ssh root@4.3.2.1 "cat >> ~/.ssh/authorized_keys" 


### Ansible Hosts File

You must create a file that tells Ansible which servers to connect to:

    touch ~/ansible_hosts

edit `~/ansible_hosts`:

    [databases]
    1.2.3.4
    4.3.2.1

you can optionally add the port if you connect to your servers using another port:

    [databases]
    1.2.3.4:5555
    4.3.2.1:22

add the following to the end of your shell resource file `~/.bashrc`, `~/.bash_profile`, or `~/.zshrc` (if you use zsh):

    export ANSIBLE_HOSTS=~/ansible_hosts

load the changes in your current shell:
  
    source ~/.bashrc


Test Ansible
------------

    ansible all -m ping

if you get the following errors:

    1.2.3.4 | FAILED => could not create temporary directory: SSH exited with return code 255
    4.3.2.1 | FAILED => could not create temporary directory: SSH exited with return code 255

or:
  
    1.2.3.4 | FAILED => Authentication or permission failure.  In some cases, you may have been able to authenticate and did not have permissions on the remote directory. Consider changing the remote temp path in ansible.cfg to a path rooted in "/tmp". Failed command was: mkdir -p $HOME/.ansible/tmp/ansible-1378609938.37-242553567200892 && chmod a+rx $HOME/.ansible/tmp/ansible-1378609938.37-242553567200892 && echo $HOME/.ansible/tmp/ansible-1378609938.37-242553567200892, exited with result 255
    4.3.2.1 | FAILED => Authentication or permission failure.  In some cases, you may have been able to authenticate and did not have permissions on the remote directory. Consider changing the remote temp path in ansible.cfg to a path rooted in "/tmp". Failed command was: mkdir -p $HOME/.ansible/tmp/ansible-1378609938.37-89444589247915 && chmod a+rx $HOME/.ansible/tmp/ansible-1378609938.37-89444589247915 && echo $HOME/.ansible/tmp/ansible-1378609938.37-89444589247915, exited with result 255

it's because we didn't specify that we want to connect with the `root` user. Generally, it's considered a bad idea to connect to SSH with the `root` user, but that's how [Digital Ocean][do] servers are configured out of the box.

you can modify your `ANSIBLE_HOSTS` file:

    [databases]
    1.2.3.4 ansible_connection=ssh  ansible_ssh_user=root
    4.3.2.1 ansible_connection=ssh  ansible_ssh_user=root

or you can also do:

    ansible all -m ping -u root

expected output:

    1.2.3.4 | success >> {
        "changed": false, 
        "ping": "pong"
    }

    4.3.2.1 | success >> {
        "changed": false, 
        "ping": "pong"
    }

which indicates it was a success.

Further reading: http://www.ansibleworks.com/docs/ In a future article, I'll discuss [playbooks](http://www.ansibleworks.com/docs/playbooks.html).

[do]: https://www.digitalocean.com/?refcode=a65fd89c7fd0
[puppet]: http://en.wikipedia.org/wiki/Puppet_(software)
[salt]: http://en.wikipedia.org/wiki/Salt_(software)
[chef]: http://en.wikipedia.org/wiki/Chef_(software)
[ansible]: http://en.wikipedia.org/wiki/Ansible_(software)
[ebook]: http://devopsu.com/books/taste-test-puppet-chef-salt-stack-ansible.html


