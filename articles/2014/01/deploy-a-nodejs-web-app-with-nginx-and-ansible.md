<!--
title: Deploy a Node.js Web App with Nginx and Ansible
publish: 2014-01-17
tags: Node.js, Nginx, Linux, Ansible
-->

With the proliferation of PaaS (platform as a service) such as [Heroku][1] and [Nodejitsu][2], it's difficult not to choose them as a hosting provider for your Node.js app. The main reason that may not want to is for cost or control. Use [Digital Ocean][do] and [Ansible][3] makes for a potent combo to deploy your Node.js apps.

This tutorial assumes that you're deploying to Ubuntu 12.04 LTS.

If you don't know what [Ansible][4] is, don't worry. You can complete this tutorial without Ansible, but you'll save a lot of time if you use Ansible.

Let's assume that you have this super simple following Node.js script:

**server.js**:

```js
var http = require('http');

http.createServer(function(req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  if (req.url === '/') {
    res.end('hello world');
  } else {
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

First things first, setup a brand new spanking VPS. I prefer [Digital Ocean][do], but any VPS will do.






[1]:https://devcenter.heroku.com/articles/getting-started-with-nodejs
[2]:https://www.nodejitsu.com/
[3]:http://procbits.com/2013/09/08/getting-started-with-ansible-digital-ocean
[4]:http://www.ansibleworks.com/

[do]:https://www.digitalocean.com/?refcode=a65fd89c7fd0