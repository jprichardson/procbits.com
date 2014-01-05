<!--
title: Understanding Custom AngularJS Promises, Services, and Punching Clowns with a Phonegap Example
publish: 2014-01-05
slug: 2014/01/05/understanding-custom-angularjs-promises-services-clown-punch-phonegap-example
tags: JavaScript, AngularJS, Phonegap, Cordova
-->

If you look at the documentation for [$q](http://docs.angularjs.org/api/ng.$q), the AngularJS promise library inspired by [Q](https://github.com/kriskowal/q), you'll notice that it functions a bit different than the promise returned by [$http](http://docs.angularjs.org/api/ng.$http).

For example:

```js
// => myPromise standard $q promise returned or created above somewhere
myPromise.then(function() {
  // => success code here
}, function() {
  // => failure code here
})
```

vs `$http` promise:

```js
$http.get(/* ... */)
.success(function() {
  // => success
}).failure(function() {
  // => failure
})
```

The documentation does not make it clear why they're different. But this [StackOverflow post](http://stackoverflow.com/questions/16385278/angular-httppromise-difference-between-success-error-methods-and-thens-a)  has some details. Arguably, it can be said that it may be a bit cleaner. Let's take a look on you can leverage this idea to make a clean modal confirmation service for a Phonegap/Cordova app.



Phonegap/Cordova
----------------

As you may know, Adobe Phonegap provides [notifications](http://docs.phonegap.com/en/3.3.0/cordova_notification_notification.md.html#Notification) so that your app can have native alert dialogs instead of the dialogs created by JavaScript.

so, instead of:

```js
// => returns `true` if 'OK' was clicked and `false` if 'Cancel' was clicked
window.confirm('Are you sure that you want to punch the clown?') 
```

you can do this:

```js
var onConfirm = function(idx) {
  if (idx === 1) {
    // => 'OK' was pressed (clown punched)
  } else {
    // => 'Cancel' was pressed (you're a weeny)
  }
}

navigator.notification.confirm('Are you sure that you want to punch the clown?', onConfirm)
```

even better, you can add a title and change the text of the buttons:

```js
navigator.notification.confirm('Are you sure that you want to punch the clown?', onConfirm, 'Punch Clown?', ['Punch Clown', 'Cancel'])
```


AngularJS Services
------------------

Let's wrap this bad boy up in a service so that we don't have to worry if we're testing our desktop browser or native mobile app.

**alerts.js**:

```js
var alertsModule = angular.module('alerts', [])

alertsModule.service('alerts', function($q, $window) {
  this.confirm = function(message, title, buttonLabels, successCallback, cancelCallback) {
    if (navigator.notification && navigator.notification.confirm) { //native app
      var onConfirm = function(idx) {
        idx === 1 ? successCallback() : cancelCallback()
      }

      navigator.notification.confirm(message, onConfirm, title, buttonLabels)
    } else {
      $window.confirm(message) ? successCallback() : cancelCallback()
    }
  }
})
```

now somewhere in your app, you can do:

```js
function onSuccess(){
  // => bozo punched
}

function onCancel() {
  // => ya, Bozo scares me too, not punched.
}

alerts.confirm('Do you want to punch bozo?', 'Punch him?', ['Punch Bozo', "No, I'm a Weeny"], onSuccess, onCancel)
```

but that's ugly. 



Promises
--------

I won't talk about the virtues of promises, as it's been done [ad nausaem](http://blog.parse.com/2013/01/29/whats-so-great-about-javascript-promises/). I rarely use them, but when I do... probably a Dos Equis meme here. 

Let's modify our service to have a promise:

```js
mod.service('alerts', function($q, $window) {
  this.confirm = function(message, title, buttonLabels) {
    var defer = $q.defer();

    if (navigator.notification && navigator.notification.confirm) {
      var onConfirm = function(idx) {
        idx === 1 ? defer.resolve() : defer.reject()
      }

      navigator.notification.confirm(message, onConfirm, title, buttonLabels)
    } else {
      $window.confirm(message) ? defer.resolve() : defer.reject()
    }

    return defer.promise
  }
})
```

now you can do this:

```js
alerts.confirm('Do you want to punch bozo?', 'Punch him?', ['Punch Bozo', "No, I'm a Weeny"])
.then(function(){
  // => bozo punched
}, function() {
  // => ya, Bozo scares me too, not punched.
})
```

### Custom Promise

You can make a custom promise to add clarity, let's modify our `alerts` service again:

```js
mod.service('alerts', function($q, $window) {
  this.confirm = function(/* ... */) {
    /* ... */
    return alertPromise(defer.promise)
  }
})

function alertPromise(promise) {
  promise.ok = function(fn) {
    promise.then(function() {
      fn.apply(this, arguments)
    })
    return promise
  }

  promise.cancel = function(fn) {
    promise.then(null, function() {
      fn.apply(this, arguments)
    })
    return promise
  }

  return promise
}
```

now you can do this:

```js
alerts.confirm('Do you want to punch bozo?', 'Punch him?', ['Punch Bozo', "No, I'm a Weeny"])
.ok(function(){
  // => bozo punched
})
.cancel(function() {
  // => ya, Bozo scares me too, not punched.
})
```

is this better? Not really. But at least your clown punching code has a little more clarity.










