<!--
title: Connecting to a SockJS server from native HTML5 WebSocket
publish: 2013-10-19
tags: JavaScript, Node.js
-->

Overview of the Problem
----------------------

I wasted over four hours today on a problem with an extremely trivial solution. I was trying to connect native HTML5 instances of the 
`WebSocket` class to my Node.js [SockJS](https://github.com/sockjs/sockjs-node) server. Why, was I trying to do this? I was trying to mimize dependences for my library [JSock](https://github.com/jprichardson/jsock)

I kept getting the following error in the browser Chrome console:

    failed: Unexpected response code: 200 

I Googled this error, and usually Google returns the cause of the problem within the first page. What followed, was me spending hours trying various different things.

So, this solution applies to you if:

1. You are using SockJS on the server, in particular Node.js. (It may apply to other SockJS server implementations too)
2. In the browser, you are using the native `WebSocket` class and not SockJS.

Your code may look something like this:

**server.js**:
```js
var PORT = 8080

var server = http.createServer()

var sockjs = require('sockjs')
var wss = sockjs.createServer()
wss.on('connection', function(ws) {
  ws.on('data', function(data) {
    ws.write('from server: ' + data)
  })
  ws.on('close', function() {
    console.log('close')
  })
})

wss.installHandlers(server, {prefix: '/data'})
server.listen(PORT)
```

**browser.js**:
```js
var wsUrl = 'ws://' + window.location.host 
var ws = new WebSocket(wsUrl + '/data')

ws.onmessage = function(e) {
  console.log(e.data)
}

ws.onopen = function() {
  console.log('opening...')
  ws.send('hello server')
}

ws.onerror = function(error) {
  console.log('WEbSocket error ' + error)
  console.dir(error)
}
```


Solution
--------

I tried removing Express, different ports, different prefixes, spent an hour combing the SockJS source code, and finally stumbled upon the solution and felt like a complete idiot.

To get your WebSockets working and remove this error:

    failed: Unexpected response code: 200 

Change the following line:

```js
var ws = new WebSocket(wsUrl + '/data')
```

to:

```js
var ws = new WebSocket(wsUrl + '/data/websocket')
```

... as stated in the documentation that I thought I read thourougly enough. I wanted to bang my head against the wall. Oh well, if this saves someone else time, then I'll be glad that I least documented it here. Happy coding!


