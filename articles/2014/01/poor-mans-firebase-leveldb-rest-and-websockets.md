<!--
title: Poor Man's Firebase: LevelDB, REST, and WebSockets
publish: 2014-01-06
slug: 2014/01/06/poor-mans-firebase-leveldb-rest-and-websockets
tags: WebSockets, JavaScript, Node.js, LevelDB, REST, Browserify
-->

Firebase
--------

I wanted to build a web app that would allow data to easily be transmitted to other connected web clients. I had heard of [Firebase](http://www.firebase.com) before. So I started reading the Firebase documentation and playing around with the examples.There is a nice library that they created called [AngularFire](http://angularfire.com) which provides some slick integration between Firebase and [AngularJS](http://angularjs.com). But for some reason, the provided chat example would sporadically not work. (As an aside, it seems that it would work most of the time in most of environments that I'd try, but for some reason, it rarely worked in one). So I needed to find a new solution.



LevelDB
------- 

According to the project page: "[LevelDB](https://code.google.com/p/leveldb/) is a fast key-value storage library written at Google that provides an ordered mapping from string keys to string values." What's great about LevelDB is that it's fast and it doesn't have any external dependencies that users need to install before they install your app.

[LevelUP](https://github.com/rvagg/node-levelup) is the Node.js bindings built on [LevelDOWN](https://github.com/rvagg/node-leveldown) which is the low-level Node.js bindings for LevelDB. 

example:

```js
var levelup = require('levelup')

var db = levelup('./mydb.db', {valueEncoding: 'json'}) //default value encoding is 'utf8'
db.put('somekey', {name: 'some data'}, function(err) {
  db.get('somekey', function(err, data) {
    console.dir(data) //{name: 'some data'}
  })
})
```

As you can see, it has pretty intuitive API.

That's not the exciting part though. What's exciting is the ecosystem of [modules and plugins](https://github.com/rvagg/node-levelup/wiki/Modules) built on LevelDB and LevelUP.



REST
----

So let's assume that you want your database to be accessible by others. You can create a simple REST API over your LevelDB database by using [multilevel-http](https://github.com/juliangruber/multilevel-http). `multilevel-http` just wraps [Express](http://expressjs.com/) and adds REST routes.

    npm install --save multilevel-http


example **(server.js)**:

```js
var levelup = require('levelup')
  , multilevelHttp = require('multilevel-http')
  , http = require('http')

var db = levelup('./mydb.db', {valueEncoding: 'json'})
var app = multilevelHttp.server(db)

var server = http.createServer(app)

server.listen(3000, function(){
  console.log('listening on port %d...', 3000)
})
```

you can now run:

    node server.js

and access the REST interface via your browser or `curl`:

    curl -X POST -d '{"name":"data from curl"}' -H "Content-Type:application/json" http://localhost:3000/data/somekey

retrieve the key `somekey`:

    curl -x GET http://localhost:3000/data/somekey



### serving HTML

create the following file **(index.html)**:

```html
<!-- 
  watch this: http://www.youtube.com/watch?v=WxmcDoAxdoY 
-->
<!doctype html>
<meta charset="utf-8">
<title>LevelDB Rules the World</title>

<h1>hi</h1>
```

As a quick aside, you don't need the `html`, `body`, and `head` tags in HTML5. Watch this [talk by Paul Irish](http://www.youtube.com/watch?v=WxmcDoAxdoY) explaining why.


let's modify **(server.js)**:

```js
/* ... */

var app = multilevelHttp.server(db)

app.get('/', function(req, res) {
  res.sendfile('./index.html')
})

var server = http.createServer(app)

/* ... */
```

now, rerun:

    node server.js

notice now that you're redirected to `/meta`? This is because `multilevel-http` has setup this redirect. Here's how you can fix it:

```js
/* ... */

function removeRoute(app, method, routeMatcher) {
  var routes = app.routes[method]

  for (var i = 0; i < routes.length; ++i) {
    var route = routes[i]
    if (route.path === routeMatcher)
      break;
  }

  routes.splice(i, 1)
}

var app = multilevelHttp.server(db)

removeRoute(app, 'get', '/')
app.get('/', function(req, res) {
  res.sendfile('./index.html')
})

var server = http.createServer(app)

/* ... */
```

now run:

    node server.js

notice now your `index.html` page is being served up correctly.



WebSockets
----------

A REST API is nice, but there is still more that you'd need to do to get it working with your client-side JavaScript. Yes, you can easily interface with a REST API via AJAX calls, but let's make things even easier and use RPC over WebSockets.

install `multilevel` and `shoe`:

    npm install --save multilevel shoe

`shoe` requires `browserify` for it to run client-side:

    npm install -g browserify

[`browserify`](http://browserify.org/) is an awesome solution for client-side package management. Probably [the best](http://procbits.com/2013/06/17/client-side-javascript-management-browserify-vs-component) at the moment. 

`multilevel` isn't the same as the package above `multilevel-http`. This is its sexier sister. `shoe` is a wrapper for [sockjs](http://github.com/sockjs). It makes dealing with WebSockets more like Node.js streams.

**server.js**:

```js
/*** 
  other requires
***/
var multilevel = require('multilevel')
var shoe = require('shoe')

/*** other code ***/

var wsdb = shoe(function(stream) {
  stream.pipe(multilevel.db(db)).pipe(stream)
})
wsdb.install(server, '/wsdb')
```

**client.js**:

```js
var multilevel = require('multilevel')
var shoe = require('shoe')

var db = multilevel.client()
var stream = shoe('/wsdb')

stream.pipe(db.createRpcStream()).pipe(stream)

/****

later in the script, you use the leveldb api

e.g.: db.get, db.put, etc

*****/
```

(reference app.js in index.html)

browserify:

    browserify client.js > app.js

run it:

    node server.js

That's it. Now client-side/browser scripts can use the `levelup` API.



### Live Changes

Part of the utility of Firebase is that changes propagate to other connected clients. Fortunately, you can do the same with LevelDB. We'll use another WebSocket to broadcast the changes.

install deps:

    install --save event-stream level-live-stream

**server.js**:

```js
/*****
  other requires
******/
var leveLiveStream = require('level-live-stream')
var es = require('event-stream')

/* ... */

var liveDBStream = levelLiveStream(db)
var changesSocket = shoe(function(stream) {
  es.pipeline(
    liveDbStream,
    es.map(function(data,next) { next(null, JSON.stringify(data)) }),
    stream
  )
})
changesSocket.install(server, '/wschanges')

/* ... */
```

**client.js**:

```js
var changesSocket = shoe('/wschanges')
changesSocket.on('data', function(data) {
  console.dir(JSON.parse(data))
})
```


Chat Example
------------

Let's put together what we learned to create a chat example. Similar to the one found on http://angularfire.com. 

Install deps:

    npm init
    npm install --save levelup leveldown multilevel event-stream shoe level-live-stream browserify

create **server.js**:

```js
var levelup = require('levelup')
  , multilevel = require('multilevel')
  , levelLiveStream = require('level-live-stream')
  , http = require('http')
  , shoe = require('shoe')
  , fs = require('fs')
  , browserify = require('browserify')
  , es = require('event-stream')

var db = levelup('./chat.db', {valueEncoding: 'json'})
var liveDbStream = levelLiveStream(db)

var messages = {}

//load initial messages
db.get('messages', function(err, data) {
  if (err) return
  messages = data
})

liveDbStream.on('data', function(data) {
  if (data.type === 'del' && data.key === 'messages') { 
    //'clear' pressed, doesn't actually remove all of the keys, although you easily could
    messages = {}
  }

  if (data.key.indexOf('message:') >= 0) {
    var idx = data.key.split(':')[1]
    messages[idx] = '' //not sophisticated enough to handle messages generated at exact same time
    db.put('messages', messages)
  }
})

var server = http.createServer(function(req, res) {
  switch (req.url) {
    case '/': 
      fs.createReadStream('./index.html').pipe(res)
      break;
    case '/client.js':
      res.writeHead(200, {'Content-Type': 'application/javascript'})
      browserify('./client.js').bundle({debug:true}).pipe(res)
      break;
    default: 
      res.writeHead(200, {'Content-Type': 'text/plain'})
      res.end(res.url + ' not found')
  }
})

var dbSocket = shoe(function(stream) {
  stream.pipe(multilevel.server(db)).pipe(stream)
})
dbSocket.install(server, '/wsdb')

var changesSocket = shoe(function(stream) {
  es.pipeline(
    liveDbStream,
    es.map(function(data, next) { next(null, JSON.stringify(data)) }),
    stream
  )
})
changesSocket.install(server, '/wschanges')

server.listen(8000, function() {
  console.log('listening...')
})
```

create **index.html**:

```html
<!DOCTYPE html>
<meta charset=utf-8>
<title>chat example</title>
<script src="client.js"></script>
<form>
  <input type="text" id="name" value="guest" style="width: 75px;">
  <input type="text" id="message" placeholder="type message here..." style="width: 300px;">
  <input type="submit" onclick="send(); return false;" value="send">
  <button onclick="clearMessages(); return false;">clear</button>
</form>
<hr>
<div id="messages"></div>
```

create **client.js**:

```js
var multilevel = require('multilevel')
  , shoe = require('shoe')

var db = multilevel.client()
var dbSocket = shoe('/wsdb')
var changesSocket = shoe('/wschanges')

dbSocket.pipe(db.createRpcStream()).pipe(dbSocket)

changesSocket.on('data', function(updateData) {
  var updateData = JSON.parse(updateData)

  if (updateData.type === 'del' && updateData.key === 'messages') {
    document.getElementById('messages').innerHTML = ''
    return
  }

  if (updateData.key.indexOf('message:') >= 0) {
    appendMessage(updateData.value)
  }
})

function appendMessage(msg) {
  var p = document.createElement('p')
  var text = document.createTextNode(msg.name + ': ' + msg.message)
  p.appendChild(text)
  document.getElementById('messages').appendChild(p)
}

window.send = function() {
  var nameEl = document.getElementById('name')
  var msgEl = document.getElementById('message')
  var obj = {name: nameEl.value, message: msgEl.value}
  msgEl.value = ''
  db.put('message:' + Date.now(), obj)
}

window.onload = function() {
  var nameEl = document.getElementById('name')
  var id = Math.random().toString().substr(2,3)
  nameEl.value += id

  //get initial chat state
  db.get('messages', function(err, messages) {
    if (messages == null) return
    
    var ids = Object.keys(messages).slice(-15) //take last 15
    ids.forEach(function(id) {
      db.get('message:' + id, function(err, data) {
        appendMessage(data)
      })
    })
  })
}

window.clearMessages = function() {
  db.del('messages', function(err) {
    if (err) alert(err.message)
  })
}
```

now run:

    node server.js

Boom! Now you have a hacky chat server ready to rock!


