---
title: basic-es6 - 005 webpack features
collection: posts
layout: pages/basic-es6/02/005-webpack-features.nunj
excerpt:
---

Webpack features

Plugins: Babel Core
====

need to run your js through babel? no sweat. there's a webpack plugin called [babel-loader](https://babeljs.io/en/setup#installation) that allows you to select some js to pass through the babel transpiler. handy for browsers (backwards compatible es5, ...).


<pre><code class="language-bash">

$ npm install --save-dev babel-loader  @babel/plugin-syntax-dynamic-import

</code></pre>

then configure the loader:

<pre><code class="language-js">
// webpack.config.03.js
const path = require('path')

module.exports = {
  entry: './src/ch0203/index.js',

  // resulting buntdle: ./dist/js/ch0202/bundle.js
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, '../../dist/js/ch0203')
  },

  // new section. let's add a loader, babel-loader, so we can run our js through it
  // see .babelrc in this folder for additional configuration
  module: {
    rules: [
      {
        test: /\.js/, // test condition (it's a js file)
        exclude: /(node_modules|bower_components)/,  // skip these directories when encounering them
        use: {
          loader: 'babel-loader', // run the .js file you found through the babel transpiler
          options: {
            babelrc: true
          }
        }
      }
    ]
  }
}

</code></pre>
___


And configure .babelrc file as well. in this case we're using a dynamic import syntax babel plugin, indicate that:

<pre><code class="language-json">

{
  "presets": [
      ["@babel/env", { "modules": false } ]
    ],
    "plugins": ["@babel/plugin-syntax-dynamic-import"]
}

</code></pre>

<pre><code class="language-bash">

$ webpack --config ./config/ch02/webpack.config.03.js --mode development --watch
Version: webpack 4.17.2
Time: 338ms
Built at: 09/12/2018 1:01:59 AM
    Asset      Size  Chunks             Chunk Names
bundle.js  3.94 KiB    main  [emitted]  main
Entrypoint main = bundle.js
[./src/ch0203/index.js] 133 bytes {main} [built]
$ ls -lh dist/js/ch0203/
total 4.0K
-rw-r--r-- 1 yuvilio yuvilio 4.0K Sep 12 01:00 bundle.js
$

</code></pre>

and now we have a js bundled _and_ transformed via babel that we can include and use in our html

{% filter escape %}
<pre><code class="language-html">

    <script src="/dist/js/ch0203/bundle.js"></script>

</code></pre>
{% endfilter %}



Code splitting
====

So one thing that bundlers help with is in optimizing for code that gets brought in  only when you need it. so if some page on the site needs some code but other pages dont, the latter don't use it.

typically at the start, we generate one bundle of js. but for some pages it seems that they only need some of the libraries but not others. This is where [code splitting](https://webpack.js.org/guides/code-splitting/) can help us. We can generate multiple bundles fro multiple entry and output points,  and load then on demand as needed

<pre><code class="language-js">
// webpack.config.04.js
const path = require('path')

module.exports = {
    // code splitting example .
  entry: {
    app: './src/ch0204/index.js'
  },
  output: {
    // we'll output
    filename: '[name].bundle.js',
    path: path.resolve(__dirname, '../../dist/js/ch0204')
  },


  // run some js through babel
  module: {
    rules: [
      {
        test: /\.js/, // test condition (it's a js file)
        exclude: /(node_modules|bower_components)/,  // skip these directories when encounering them
        use: {
          loader: 'babel-loader', // run the .js file you found through the babel transpiler
          options: {
            babelrc: true
          }
        }
      }
    ]
  }
}


</code></pre>
___


notice the output is now dynamic. it's jut 'bundle.js' but rather '[name].bundle.js'. let's take a look at the actual code to see what that's about . we have a few files we're working with using [dynamic imports](https://medium.com/front-end-hacking/webpack-and-dynamic-imports-doing-it-right-72549ff49234):

<pre><code class="language-js">

// src/ch0204/index.js

const button = document.createElement("button")
button.textContent = 'Open chat'
document.body.appendChild(button)

// we don't need this chat.bundle.js just yet , it'll be auto "imported" (brought into the html) once the button is clicked
button.onclick = () => {
  import(/* webpackChunkName: "chat" */ "./chat").then(chat => {
    chat.init()
  })
}


</code></pre>
/*


<pre><code class="language-js">

// src/ch0204/chat.js

import people from "./people" // just some array of data objects we're using

export function init() {
  const root = document.createElement("div")
  root.innerHTML = `<p>There are ${people.length} people in the room.</p>`
  document.body.appendChild(root)
}


</code></pre>


So here's where the code splitting comes in handy. notice that the import of the chat.js file (in a gnarly new webpack notation format ) only happens when the button is _clicked_

but what if the button is never clicked on most pages? and more, what if chat.js ends up being some substantial about of js code? do we need it brought in every time?  Nope, that's why we have it split. when we run webpack, it notices we want multiple outputs. so it runs through the js files it sees (index.js, chat.js) and generates a bundle for each of them. but only the index.js's bundle (bundle.js) is wht needs to be included:

<pre><code class="language-bash">
$ webpack --config ./config/ch02/webpack.config.04.js --mode development --watch
...
[./src/ch0204/chat.js] 244 bytes {chat} [built]
[./src/ch0204/index.js] 398 bytes {app} [built]
[./src/ch0204/people.js] 152 bytes {chat} [built]
^C
$
$ ls -lh dist/js/ch0204/
total 16K
-rw-r--r-- 1 yuvilio yuvilio 8.7K Sep 12 01:40 app.bundle.js
-rw-r--r-- 1 yuvilio yuvilio 1.6K Sep 12 01:40 chat.bundle.js
$

</code></pre>

notice two bundles were generated, that latter one is from chat.js. if the button is clicked, then webpack added code to http fetch that and insert it into the DOM as a script tag. so it's not initially in the included js on the page

{% filter escape %}
<pre><code class="language-html">
<script src="/dist/js/ch0204/app.bundle.js"></script>

</code></pre>
{% endfilter %}

when you click on the button that index.js creates and appends and adds an onclick event to , it'll bring in the chat butndle.js . until then , that's nont needed. pretty nice. figuring the http paths, might take a little digging
