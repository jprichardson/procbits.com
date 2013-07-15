<!--
title: CSS Resets
publish: 2013-07-12
slug: 2013/07/13/css-resets
tags: CSS
-->

This is the first of [many articles][css] related to my adventures with CSS. I haven't actually really had the need to use CSS for about 6 years. I always had avoided it. When [Twitter Bootstrap][bootstrap] was released I was pumped because there was a way for developers to just focus on code and ignore the style. Recently, I decide that I'm going to write a cross-platform app using [Adobe Phonegap][phonegap]. So, I'll need to get really familiar with CSS3 hardware transitions and styles.

Let's start with the basics. CSS resets.

Huh? What's the point?

Each browser renders elements in its own default way. Consider the following HTML:

```html
<!doctype html>
<body>
  <p>Some paragraph</p>
  <ul>
    <li>item 1</li>
    <li>item 2</li>
  </ul>
</body>
```

#### output:

<iframe src="http://embed.plnkr.co/RRptESb8sbIeWWk4JzXf/preview" ></iframe>

It looks liked you'd expect. 

Now, let's modify it a bit, and add the following style:

```css
* {
  padding: 0px;
}
```

#### output:

<iframe src="http://embed.plnkr.co/rxRPPHnK9ploDznjhAD5/preview"></iframe>

Notice the item list bullets are gone?

Let's modify it a bit more:

```css
* {
  padding: 0px;
  margin: 0px;
}
```

#### output:

<iframe src="http://embed.plnkr.co/VlVoTlypjSKWo4CGvSSu/preview"></iframe>

Now you'll notice that the margins are removed.

There is a case to be made against using the universal selector `*`, and that is that it ruins the margins and padding
of form elements. But it's definitely a good start to understanding on you can make cross browser HTML look consistent.


### CSS Reset Frameworks
* [Normalize.css](http://necolas.github.io/normalize.css/)
* [Eric Meyer's Reset](http://meyerweb.com/eric/thoughts/2011/01/03/reset-revisited/)


### Resources
* http://sixrevisions.com/css/css-tips/css-tip-1-resetting-your-styles-with-css-reset/
* http://snook.ca/archives/html_and_css/no_css_reset/
* https://mondaybynoon.com/why-i-like-and-use-reset-css/
* http://stackoverflow.com/questions/99643/css-reset-default-styles-for-common-elements


[css]: http://procbits.com/tags/css
[bootstrap]: http://twitter.github.io/bootstrap/
[phonegap]: http://phonegap.com/



