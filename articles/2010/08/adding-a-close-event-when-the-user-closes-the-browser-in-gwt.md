<!--
author: JP Richardson
publish: Fri Aug 06 2010 23:23:28 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2010/08/06/adding-a-close-event-when-the-user-closes-the-browser-in-gwt/
tags: GWT
slug: 2010/08/06/adding-a-close-event-when-the-user-closes-the-browser-in-gwt
-->

Adding a Close Event When the User Closes the Browser in GWT
============================================================

Often times your GWT app will queue up client side RPC events. What if
the user clicks close on the browser window or tries to navigate to a
new page? The solution is very simple.

```java
Window.addWindowClosingHandler(new ClosingHandler(){
    @Override public void onWindowClosing(ClosingEvent event) {
        event.setMessage("If you leave, you may lose data. Continue?");
    }
});
```

Simple as pie. If event.setMessage is called, a prompt will be displayed
for you.

Are you a [Git](http://gitpilot.com) user? Let me help you make project
management with Git simple. Checkout [Gitpilot](http://gitpilot.com).

Follow me on Twitter: [@jprichardson](http://twitter.com/jprichardson)

-JP
