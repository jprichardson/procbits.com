<!--
author: JP Richardson
publish: Wed Apr 11 2012 22:24:09 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2012/04/11/quick-and-dirty-screen-scraping-with-node-js-using-request-and-cheerio/
tags: JavaScript, Node.js
slug: 2012/04/11/quick-and-dirty-screen-scraping-with-node-js-using-request-and-cheerio
title: Quick and Dirty Screen Scraping with Node.js using Request and Cheerio
-->



I wrote my own screen scraping module built on
[PhantomJS](http://www.phantomjs.org/), but unfortunately it's too slow
for most screen scraping tasks that don't require browser-side
JavaScript. One easy way to scrape pages with Node.js is to use
[Request](https://github.com/mikeal/request) and
[Cheerio](https://github.com/MatthewMueller/cheerio).

Here is an example of scraping Bing to get all of the search results:

```javascript
var request = require('request');
var cheerio = require('cheerio');

var searchTerm = 'screen+scraping';
var url = 'http://www.bing.com/search?q=' + searchTerm;

request(url, function(err, resp, body){
  $ = cheerio.load(body);
  links = $('.sb_tlst h3 a'); //use your CSS selector here
  $(links).each(function(i, link){
    console.log($(link).text() + ':\n  ' + $(link).attr('href'));
  });
});
```

Cheerio acts a jQuery replacement for a lot of jQuery tasks. It doesn't
replicate jQuery in every way, and most importantly it's not meant for
the browser but for the server. But it beats the pants off of the
[jsdom](https://github.com/tmpvar/jsdom)/jQuery combo for screen
scraping.


