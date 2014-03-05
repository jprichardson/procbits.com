<!--
title: Deploy a Node.js Web App with Nginx and Ansible
publish:
slug: 
tags: Node.js, Nginx, Linux, Ansible
-->

With the proliferation of PaaS (platform as a service) such as [Heroku][1] and [Nodejitsu][2], it's difficult not to choose them as a hosting provider for your Node.js app. The main reason that may not want to is for cost or control. Use [Digital Ocean][do] and [Ansible][3] makes for a potent combo to deploy your Node.js apps.

This tutorial assumes that you're deploying to Ubuntu 12.04 LTS.

If you don't know what [Ansible][4] is, don't worry. You can complete this tutorial without Ansible, but you'll save a lot of time if you use Ansible.

Let's assume that you have this super simple following Node.js script:

**./coolapp/index.html**:

```html
<!doctype html>
<meta charset="utf-8">
<title>Cool App</title>
<link href="/css/style.css" rel="stylesheet"/>

<h1>this is the cool app</h1>
```

**./coolapp/css/style.css**:
```css
h1 {
  font-size: 54px;
  color: red;
  text-decoration: underline;
}
```

**./coolapp/server.js**:

```js
var http = require('http');
var fs = require('fs');

http.createServer(function(req, res) {
  if (req.url === '/') {
    res.writeHead(200, {'Content-Type': 'text/html'});
    fs.createReadStream('./index.html').pipe(res);
  } else if (req.url === '/css/style.css') {
    res.writeHead(200, {'Content-Type': 'text/css'});
    fs.createReadStream('./css/style.css').pipe(res);
  } else {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end(req.url);
  }
}).listen(process.env.PORT);
```

Why?
----

You really wouldn't need an elaborate setup like what we're going to recreate. But why isn't just running the following for your web app goood enough:

    PORT=80 node server.js

Because you typically get an error like this:

```
Error: listen EACCES
```

because all ports below (inclusive) **1024** are privileged.

Ok, so why not:

    sudo PORT=80 node server.js

If there is a vulnerability on your web app, your system could be compromised because it's running with `root` privileges. 

why not:

    PORT=8080 node server.js

Unlesss you specify that people visit your url `http://mywebapp.com:8080`, this won't work well either. Puting the port in the URL is non-standard it it's almost assured that it'll get removed when people tell other people to visit `mywebapp.com`.

Other problems of these methods:

- What if your server needs to reboot? Don't you want your webapp to start up with the system?
- What if there is a bug in your app and it crashes? Don't you want it to restart?



Getting Started
---------------

First things first, setup a brand new spanking VPS. I prefer [Digital Ocean][do], but any VPS will do. My instructions should work for Digital Ocean or Amazon EC2.



Installing Node and a Node Version Manager
------------------------------------------

On my client workstation, I prefer [nvm][5]. On my servers I prefer [n][6]. Really, they are just a matter of taste and you could use either one. Now login to your workstation if you're not going to use Ansible. 

Note that the syntax `[sudo]` means that you should run `sudo` if you're not logged in as `root`.

manually:

    [sudo] apt-get install make git-core
    git clone https://github.com/visionmedia/n /tmp/n
    make -C /tmp/n
    [sudo] n stable

(TODO: ansible)

Now the latest stable Node version is installed.


Setup Git Repo on Server
------------------------

    [sudo] mkdir -p /var/lib/git/coolapp.git
    cd /var/lib/git/coolapp.git
    [sudo] git init --bare
    [sudo] touch hooks/pre-receive
    [sudo] touch hooks/post-receive
    [sudo] chmod +x hooks/pre-receive
    [sudo] chmod +x hooks/post-receive


(error on push)

    (pre-receive hook declined)




Creating User / Group / Upstart Service
---------------------------------------

    [sudo] addgroup --system nodejs
    [sudo] adduser --system --shell /bin/false  --no-create-home --ingroup nodejs nodejs



[1]:https://devcenter.heroku.com/articles/getting-started-with-nodejs
[2]:https://www.nodejitsu.com/
[3]:http://procbits.com/2013/09/08/getting-started-with-ansible-digital-ocean
[4]:http://www.ansibleworks.com/
[5]:https://github.com/creationix/nvm
[6]:

[do]:https://www.digitalocean.com/?refcode=a65fd89c7fd0