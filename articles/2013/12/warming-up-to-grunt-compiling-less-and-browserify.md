<!--
title: Warming Up To Grunt: Compiling Less and Browserify
publish: 2013-12-03
slug: 2013/12/05/warming-up-to-grunt-compiling-less-and-browserify
tags: HTML, JavaScript, Node.js, CSS, Productivity, CoffeeScript
-->

I use to hate [Grunt][grunt]. No, I actually use to loath Grunt. I couldn't understand why anyone would want to use it over something so simple as a Makefile. I'd rather gouge my eyes out with a spoon than look at a Gruntfile. I hate CoffeeScript too, (forgive all the hate, please don't think of me like a Sith Lord of programming) but CoffeeScript at least makes Grunt a little easier on the eyes.

When I started using [Browserify][browserify] and [Less][less] I needed a way to recompile the changes. So I like a good little *nix developer, I made Make tasks:

**Makefile**:
```make
client-js:
    browserify js/app.js -o build/bundle.js

client-css:
    lessc css/app.less css/app.css 

.PHONY: client-js client-css
```

It got really annoying typing `make client-js` or `make client-css` after everytime I changed my JavaScript of less source. I needed something to watch my files and recompile the changes.

Enter Grunt.

Here is a [sample Gruntfile](http://gruntjs.com/getting-started):

```js
module.exports = function(grunt) {

  // Project configuration.
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    uglify: {
      options: {
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'
      },
      build: {
        src: 'src/<%= pkg.name %>.js',
        dest: 'build/<%= pkg.name %>.min.js'
      }
    }
  });

  // Load the plugin that provides the "uglify" task.
  grunt.loadNpmTasks('grunt-contrib-uglify');

  // Default task(s).
  grunt.registerTask('default', ['uglify']);

};
```

Text vomit to me, ick.


```coffeescript
module.exports = (grunt) ->
  
  # Project configuration.
  grunt.initConfig
    pkg: grunt.file.readJSON("package.json")
    uglify:
      options:
        banner: '/*! <%= pkg.name %> <%= grunt.template.today("yyyy-mm-dd") %> */\n'

      build:
        src: "src/<%= pkg.name %>.js"
        dest: "build/<%= pkg.name %>.min.js"

  
  # Load the plugin that provides the "uglify" task.
  grunt.loadNpmTasks "grunt-contrib-uglify"
  
  # Default task(s).
  grunt.registerTask "default", ["uglify"]
```

As stated, I pretty much hate CoffeeScript despite the fact that I wrote [this](http://procbits.com/2012/05/18/why-do-all-the-great-node-js-developers-hate-coffeescript). (I might just go invent a time-machine so that I can go back in time and give a round house kick to the face of JP past.) But at least it looks a little better.



Grunt with Browserify
---------------------

First, you should install the Grunt task runner:

    [sudo] npm install -g grunt-cli

install the grunt browserify package:

    npm install --save-dev grunt-browserify

that's it.


Let's create a simple JS file that will act as our browser side JavaScript:

    mkdir www
    touch www/app.js

Now let's create our gruntfile `Gruntfile.js`:

```js
module.exports = function(grunt) {
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    browserify: {
      js: {
        src: './www/app.js',
        dest: './www/bundle.js'
      }
    }
  })

  grunt.loadNpmTasks('grunt-browserify')
  grunt.registerTask('default', ['browserify'])
}
```

now run the task:

    grunt browserify

and you'll notice that `./www/bundle.js` has been created. But that's not much better than creating a Makefile. Arguably worse.



Grunt with Watch
----------------

What would really be useful is to save a file and then have Grunt call browserify to recompile it. 

Install the following npm package:

    npm install --save-dev grunt-contrib-watch

modify your grunt file to look like:

```js
module.exports = function(grunt) {
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    watch: {
      js: {
        files: ['www/**/*.js'],
        tasks: ['browserify']
      }
    },
    browserify: {
      js: {
        src: './www/app.js',
        dest: './www/bundle.js'
      }
    }
  })

  grunt.loadNpmTasks('grunt-contrib-watch')
  grunt.loadNpmTasks('grunt-browserify')
  grunt.registerTask('default', ['watch', 'browserify'])
}
```

now run the task:

    grunt watch

and if you modify `./www/app.js` and save it, you'll notice browserify will automatically run!



Grunt with Less
---------------

Now let's say that you want the same behavior with Less. Install:

    npm install --save-dev grunt-contrib-less

create your less file:

    touch www/app.less

modify your gruntfile:

```js
module.exports = function(grunt) {
  grunt.initConfig({
    pkg: grunt.file.readJSON('package.json'),
    watch: {
      js: {
        files: ['www/**/*.js'],
        tasks: ['browserify']
      },
      css: {
        files: ['www/**/*.less'],
        tasks: ['less']
      }
    },
    browserify: {
      js: {
        src: './www/app.js',
        dest: './www/bundle.js'
      }
    },
    less: {
      development: {
        files: {'./www/app.css': './www/app.less'}
      }
    }
  })

  grunt.loadNpmTasks('grunt-contrib-watch')
  grunt.loadNpmTasks('grunt-browserify')
  grunt.loadNpmTasks('grunt-contrib-less')
  grunt.registerTask('default', ['watch', 'browserify', 'less'])
}
```

Now modify `www/app.less` and save it. You'll see `www/app.css` update accordingly.



Conclusion
----------

I don't love Grunt yet, but I'm definitely warming up to the idea of having it as another tool in my developer arsenal.





[browserify]: https://github.com/substack/node-browserify
[less]: http://lesscss.org/
[grunt]: http://gruntjs.com/


