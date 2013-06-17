<!--
title: Client-Side JavaScript Management, Browserify vs Component
publish: 2013-06-17
slug: 2013/06/17/client-side-javascript-management-browserify-vs-component
tags: Node.js, JavaScript, AngularJS
-->

I've been writing mostly JavaScript for the last 1.5 years; primarily Node.js using server side only. I started working on a big project that required a single-page web app. For my recent client-side work, I've been using [AngularJS](http://angularjs.org). 

Most of my apps haven't been large enough to require any client-side JavaScript dependency management; I've also been fortunate enough, up to this point, to avoid the [AMD][amd] vs [CommonJS][commonjs] on the client-side.

On the client-side, there are two primary problems with JavaScript packages: 

1. Dependency management.
2. Module composition. With the most frequent styles being: CommonJS and AMD.

Because of m ybias towards CommonJS because of Node.js and the fact that I agree with TJ Holowaychuk that [AMD is ugly][component_intro], automatically rules out any solution that prefers AMD, namely: [RequireJS][requirejs], [Volo][volo], or [Jam][jam]. [Bower][bower] is intriguing because it does a great job of client-side dependency management and it was created by Twitter, so there is a big name backing it. However, Bower doesn't care if you use AMD or CommonJS. I cared more about using CommonJS in the browser than I did package management. So, it's preferable to find a solution that does dependency management and CommonJS.

This left me with a choice of [Browserify](http://browserify.org/) and [Component](https://github.com/component/component). They are both created by well known members of the Node.js community: [Substack](https://github.com/substack) and [TJ Holowaychuk](https://github.com/visionmedia) respectively.


Browserify
----------

### Pros
1. Can reuse Node.js modules on the client with relative ease. Obviously, there are limits such as modules that use any of the networking or file system API.
2. You use NPM for installation and dependency management. Isaac (the Node.js benevolent dictator) has [said that storing pure client-side modules in NPM is OK](http://esa-matti.suuronen.org/blog/2013/03/22/journey-from-requirejs-to-browserify/#comment-838581508). 
3. Source maps.
4. Simple to use. If you're already using Node.js and NPM, there isn't much you need to do with Browserify, other than require your code client-side. There is no dependency listing or anything like that, Browserify will traverse your `require()` statements and include the necessary code.

### Cons
1. There is no dependency management. This could be a pro as well if you're satisfied with NPM for dependency management.
2. It may feel a bit magical. You can use `EventEmitter` and other Node.js core modules on the client.
3. Build step. (A watcher can help this)
4. Client-side libraries in NPM feels weird and a bit wrong.
5. NPM dependency graph could get bloated if you include the wrong module client-side. i.e. The same module with different versions.
6. Documentation is really bad.
7. Node.js naming conflicts. Getting harder to come up with good names. Fortunately you can just append a `.js` to give yourself more choices but will probably introduce ambiguity.


Component
---------

### Pros
1. Encouragement of simplicity. This could become a bit unwieldy because some Component modules are just a few lines. Your app might need 100's of components. But again, this could be a big plus.
2. Github is the de facto repository for storage and retrieval of your components. This makes publishing easy.
3. Github style naming. i.e. `jprichardson/mymodule` vs `tjholowaychuk/mymodule`.
4. Momentum seems to be increasing.
5. Flat dependency tree. Only one version of a component allowed, this reduces the amount of client-side JavaScript.

### Cons
1. Feels like there is a bit too much ceremony to get up and going if you're already using NPM. This makes sense given that it supports dependency management though.
2. Build step.
3. `Component` is hard to Google. Anytime you Google anything with the search term `Component`, a lot of unrelated articles come up. Picking such a generic name was a bad idea IMHO. Overtime, this may get better.
4. HTTP only remotes. You can have private Component repos, but it must support HTTP. I know that this https://github.com/godmodelabs/contre can help with that. I really bothers me. I'd like to `component install jp@myprivateserver.com/var/lib/git/mymodule.git`.
5. Extra `component.json` file. I'm being a bit nitpicky here, but managing the version number in two places (assuming you have a package.json) sucks. I understand TJ's [reason for doing this though](https://github.com/component/component/wiki/F.A.Q).


Conclusion
----------

I ultimately chose Browserify. At this point it seems that my main problem to solve is getting some of my JavaScript modules (specific algorithms) on the client. I love NPM and so Browserify feels natural and makes sense. To be fair, it seems that Component's main goal is to encourage you to write reusable web (or JavaScript) components. This isn't a problem that I need to solve, especially given that I'm pretty invested in the AngularJS ecosystem and Angular way of doing things. But Component is a pure client-side solution. It may feel like a bit much if you're already using NPM.

I'm also hoping that [ES Harmony modules](http://wiki.ecmascript.org/doku.php?id=harmony:modules) and [Web Components standard](http://www.w3.org/TR/2013/WD-components-intro-20130606/) help to solve some of these problems.

Honorable mention: [Ender][ender]. I have no idea why Ender hasn't seen a lot of adoption.

More reading:

1. http://www.reddit.com/r/javascript/comments/vc9d9/npm_vs_jam_requirejs_vs_browserify_vs_ender/
2. http://stackoverflow.com/questions/15603095/jam-vs-bower-whats-the-difference
3. http://yeoman.io/packagemanager.html
4. https://github.com/webpack/webpack
5. http://kpuputti.github.io/perkele.js/examples/javascript-package-managers/index.html
6. http://wibblycode.wordpress.com/2013/01/01/the-state-of-javascript-package-management/
7. https://github.com/medikoo/modules-webmake
8. http://dailyjs.com/2013/01/28/components/
9. http://www.forbeslindesay.co.uk/post/44144487088/browserify-vs-component
10. https://github.com/stagas/browserify-vs-component/wiki/Browserify-vs-Component
11. http://anthonyshort.me/2012/12/building-projects-with-component
12. http://esa-matti.suuronen.org/blog/2013/03/22/journey-from-requirejs-to-browserify/

[component_intro]: http://tjholowaychuk.com/post/27984551477/components 
[amd]: http://requirejs.org/docs/whyamd.html
[commonjs]: http://dailyjs.com/2010/10/18/modules/
[bower]: http://bowerjs.org
[requirejs]:
[volo]: http://volojs.org/
[ender]: http://ender.jit.su/
[jam]: http://jamjs.org/
[requirejs]: http://requirejs.org/

