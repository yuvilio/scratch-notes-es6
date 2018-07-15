---
title: basic-es6 - 004 webpack intro
collection: posts
layout: pages/basic-es6/02/004-webpack-intro.nunj
excerpt:
---

Webpack intro

What's webpack? WHere does it fit in?

Webpack is some code whose job it is to manage brining in code or removing code. It's there to make use js modules  in an efficient way.  It started with just js related modules and found ways to bring in other js modules that help manage css styles (e.g.: postcss, styled components, ..) as well as we templating (react, .. ).

It offers ways to generate multiple bundles, or have bundles that have modules added and removed from there. It has plenty of competitors (rollup, parcel, stealjs, system.js, ...) but still manages to hold it's own thanks to improved features and popular adoption.

So how to get started?

Let's try installing it with a [basic setup](https://webpack.js.org/guides/getting-started/#basic-setup) and seeing how it adds code. We can learn [some webpack concepts](https://webpack.js.org/concepts/) along the way

<pre><code class="language-bash">
$ npm install --save-dev webpack
# ...
+ webpack@3.10.0
added 251 packages in 10.416s
$
#despite this adding but one package,...
$ cat package.json  | grep -i devdep -A 3
  "devDependencies": {
    "webpack": "^3.10.0"
  }
}
# it ads a bunch of infrustructure for its needed tasks

$ ls node_modules/
acorn                 concat-map                  fill-range               json-schema-traverse       path-type                  signal-exit
acorn-dynamic-import  console-browserify          find-up                  kind-of                    pbkdf2                     source-list-map
ajv                   constants-browserify        for-in                   lazy-cache                 p-finally                  source-map
ajv-keywords          core-util-is                for-own                  lcid                       pify                       spdx-correct
align-text            create-ecdh                 get-caller-file          loader-runner              p-limit                    spdx-expression-parse
# ...
$ ls -1 node_modules/ | wc -l
234
$
# ... fortunately, the result in usage doesn't include them in the footprint

$

</code></pre>

Now let's try a simple js bundle and use it in a simple page . We'll start with a barebones html page that references the javasript file we'll generate with webpack `./dist/index.html`:

{% filter escape %}
<pre><code class="language-html">
<!DOCTYPE html>
<html>
  <head>
    <title>Getting Started</title>
  </head>
  <body>
		<h1>hey there</h1>


    <!-- js code that is partially bundled dependencies and partially code we'll write -->
    <script src="./js/bundle.js"></script>
  </body>
</html>
</code></pre>
{% endfilter %}

Next up, let's set up a simple js file where the app code we want to focus on will run. We'll consider it the `entry` point .

Next up, let's set up a barebones webpack config for weback

<pre><code class="language-js">
// ./src/index.js
// this code will use some modules that webpack will bring  in
document.addEventListener('DOMContentLoaded', function (event) {
  console.log('here is ')
})

</code></pre>


This isn't exciting yet (we haven't imported any code).

Alright. we installed webpack, so let's get it involved. We'll generate that bundle using the code we have (`src/index.js`) along with modules it will bring in. For now let's see what a barebones bundle (with just the simple js we have in `src/index.js`) will look like. We'll run webpack, giving it a barebones a config to use.

<pre><code class="language-js">
// ./webpack.config.js
const path = require('path');

module.exports = {
  // this file kicks things off . webpack will analyse this file and import what it needs (some configuration might be needed to pull that off)
  entry: './src/index.js',

  // the output bundled file will be in ./dist/js/bundle.js
  output: {
    filename: 'bundle.js',
    path: path.resolve( __dirname, 'dist', 'js')
  },

};
</code></pre>
__.__

Alright the stage is set Let's generate the bundle using our config. We can use webpack from node_modules and pass it the config we added.

<pre><code class="language-bash">

$ node_modules/webpack/bin/webpack.js  --config webpack.config.js
Hash: 3d7af60c0902dacae83e
Version: webpack 3.10.0
Time: 38ms
    Asset     Size  Chunks             Chunk Names
bundle.js  2.63 kB       0  [emitted]  main
   [0] ./src/index.js 157 bytes {0} [built]
$

</code></pre>

<pre><code class="language-js">

/******/ (function(modules) { // webpackBootstrap
/******/ 	// The module cache
/******/ 	var installedModules = {};
/******/
/******/ 	// The require function
/******/ 	function __webpack_require__(moduleId) {
  // ...
/******/ 	}
/******/
// ...

/***/ (function(module, exports) {

// this code will use some modules that webpack will bring  in
document.addEventListener('DOMContentLoaded', function (event) {
  console.log('here is ')
})

/***/ })
/******/ ]);

</code></pre>

So you can see some js bootstrapping code that webpack needs. towards the end is a self invoking iife format that is browser friendly .

So if we serve this html file, we should see that console.log. We can use a webserver (nginx, ...) or other approaches. webpack even has a companion dev server module, [webpack-dev-server](https://github.com/webpack/webpack-dev-server) so let's try that.


<pre><code class="language-bash">
$ npm install --save-dev webpack-dev-server
</code></pre>

Let's add some dev server related config   
<pre><code class="language-js">
//  webpack.config.js
const path = require('path');

module.exports = {
  // this file kicks things off .
  entry: './src/index.js',

  // the output bundled file will be in ./dist/js/bundle.js
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist', 'js')
  },

  // webpack-dev-server config
  devServer: {
    compress: false,
    inline: true,
    port: 9000
  },

};

</code></pre>
__.__

<pre><code class="language-bash">
$ node_modules/webpack-dev-server/bin/webpack-dev-server.js  --config webpack.config.js  --entry ./assets/js/basics/01/001-test-import.js  --output-filename ./dist/assets/js/basics/01/001-test-import.js
Project is running at http://localhost:9000/
webpack output is served from /
Content not from webpack is served from /home/yuvilio/Downloads/webpacktest/dist
Hash: cce96526f913dcc07247
Version: webpack 3.10.0
Time: 262ms
    Asset    Size  Chunks                    Chunk Names
bundle.js  324 kB       0  [emitted]  [big]  main
   [2] multi (webpack)-dev-server/client?http://localhost:9000 ./src/index.js 40 bytes {0} [built]
   [3] (webpack)-dev-server/client?http://localhost:9000 7.91 kB {0} [built]
   [4] ./node_modules/url/url.js 23.3 kB {0} [built]
   #..
  [23] (webpack)/hot/emitter.js 77 bytes {0} [built]
  [25] ./src/index.js 157 bytes {0} [built]
    + 11 hidden modules
webpack: Compiled successfully.

</code></pre>

Now when we browser to `http://localhost:9000/`, we'll see the html rendered and  the `console.log()` output,  'hey there'.

Notice you might not see an actual bundle getting generated when using webpack-dev-server. It seems to load in the browser from memory (but still appear as  a file). see [here for explanation](https://www.youtube.com/watch?v=HNRt0lODCQM). Also notice, webpack-dev-server seems to work better with some command line flags (for entry and output files, ..) rather than just config

Want to refresh on js change? Turn the [watch](https://webpack.js.org/configuration/watch/#watch) option on

<pre><code class="language-js">
// ./webpack.config.js
const path = require('path');
module.exports = {
  // ...

  // webpack-dev-server config
  devServer: {
    contentBase: path.join(__dirname, 'dist'), // serve the from the dist/ folder (consider it the root /)
    compress: false,
    port: 9000
  },

  watch: true //refresh
};

</code></pre>
__.__

If you run into sme issues on the dev server refresh there are a bunch of [fixes](https://github.com/webpack/webpack-dev-server/issues/875)

As always . once we hit a sweetspot in webpack usage , we can add some npm scripts to simplify the command. We can revisit in a bit, but first. Let's actually see webpack do something useful. Let's see it incorporate a module.  For now we'll use a really simple module that has no dependencies of its own . Sindresorhus makes a bunch of these. One of them is [yn](https://github.com/sindresorhus/yn). It takes a string and detets if it's some kind of yes or no response  ('yes', 'y', 'n', , 'no', ...) or not.

First let's install that module so it's code is available .

<pre><code class="language-bash">
$ npm install --save yn
$
</code></pre>

Now let's use that module. It's not an es6 one so we'll use commonjs notation for bringing it in.
<pre><code class="language-js">

const yn = require('yn');

document.addEventListener('DOMContentLoaded', function (event) {

  // let's test ym from the module
  console.log( "yn('y'): ",  yn('y') ); // => true
  console.log( "yn('bla'): ",  yn('bla') ); // => false

})

</code></pre>

output shows that the module is getting used:

<pre><code class="language-bash">

bundle.js:9201 yn('y'):  true
bundle.js:9202 yn('bla'):  null

</code></pre>


if you inspect the bundle.js, you'll see [module yn's code](https://github.com/sindresorhus/yn/blob/master/index.js) in it:

<pre><code class="language-js">

// ...
/* 1 */
/***/ (function(module, exports, __webpack_require__) {

"use strict";

const lenient = __webpack_require__(2);

module.exports = (val, opts) => {
	val = String(val).trim();
	opts = Object.assign({
		lenient: false,
		default: null
	}, opts);

	if (opts.default !== null && typeof opts.default !== 'boolean') {
		throw new
// ...
</code></pre>

It's there labeled in the comments with a number, `**1**`,  rather than the module  name .

That was nice. Does webpack also support es6 module `import` notation? Let's try adding a module and importing it directly


<pre><code class="language-js">
// './modules/test-module.js'
let greeting = 'hey there ' // this variable isn't exported.
// not available for use outside of this module

// this variable IS exported. available to imported by other files
// it uses it's internal valriable (greeting)
export let hello = `${greeting}, from this module variable`
</code></pre>

and now let's have `index.js` entry file import it

<pre><code class="language-js">
import { hello } from './modules/test-module.js'

document.addEventListener('DOMContentLoaded', function (event) {
  console.log(hello) // hey there , from this module variable
})

</code></pre>

and indeed, the browser shows the output from that module :

<pre><code class="language-bash">
hey there , from this module variable
</code></pre>


## es6 webpack

can I use es6 syntax in my webpack config?

Yep. one approach is to rename the file to webpack to `webpack.config.babel.js`

and then add [babel-loader](https://github.com/babel/babel-loader#install)  to enable babel processing of the file. and add the babel plugin  [syntax-dynamic-import](https://babeljs.io/docs/plugins/syntax-dynamic-import/) to allow babel to  parse `import` keywords from the webpack config.js file

<pre><code class="language-bash">
$ npm install --save-dev babel-loader babel-core babel-preset-env babel-plugin-syntax-dynamic-import
</code></pre>


here's the `.babelrc` json you can use: (see [this note](https://github.com/babel/babel-loader/issues/493#issuecomment-317285885) and it's [followup](https://github.com/babel/babel-loader/issues/493#issuecomment-336493807)

<pre><code class="language-json">
{
  "presets": [
    ["env", {
      "targets": {
        "browsers": ["last 2 versions", "> 2%"]
      }
    }]
  ],
  "plugins": ["syntax-dynamic-import"]
}
</code></pre>

now let's set up a simple `webpack.config.babel.js` file, this time using es6 syntax with `import`

<pre><code class="language-js">
// webpack.config.babel.js
import webpack from 'webpack';

export default 	{
	entry: './assets/js/basics/01/001-test-import.js', // starting app file

	output: {
		filename: '001-test-import.js', //resulting budle
		path: __dirname + '/dist/assets/js/basics/01/'
	},
}


</code></pre>
__.__

let's test it:

<pre><code class="language-bash">
$ pwd
/home/yuvilio/ws/apps/node/misc/webpacktest
$

$ node_modules/webpack/bin/webpack.js --config webpack.config.babel.js
Hash: 13b948e3e1fed7ec4bda
Version: webpack 3.10.0
Time: 64ms
             Asset     Size  Chunks             Chunk Names
001-test-import.js  5.87 kB       0  [emitted]  main
   [0] ./assets/js/basics/01/001-test-import.js 415 bytes {0} [built]
   [3] ./assets/js/basics/01/modules/test-module.js 313 bytes {0} [built]
    + 2 hidden modules
$ ls -lh dist/assets/js/basics/01/001-test-import.js
-rw-r--r-- 1 yuvilio yuvilio 5.8K Jan 21 23:34 dist/assets/js/basics/01/001-test-import.js
$

</code></pre>

Nice, the bundling happened as expected. It works similarly with webpack-dev-server :

<pre><code class="language-bash">
$ node_modules/webpack-dev-server/bin/webpack-dev-server.js  --config webpack.config.babel.js  --entry ./assets/js/basics/01/001-test-impt.js  --output-filename ./dist/assets/js/basics/01/001-test-import.js
</code></pre>


## multiple configs.

Need to swap different configs in an out? fortunately, webpack allows [multiple configurations](https://webpack.js.org/configuration/configuration-types/#exporting-multiple-configurations) with using arrays




<pre><code class="language-js">

// ./lib/webpack/basics/001-basics.js
//past (inactive bundles). add to activeConfigs for rollup to generate them
let pastEntries = [
	{
		entry: './assets/js/basics/01/001-test-import.js', // starting app file

		output: {
			filename: '002-test-import.js', //resulting budle
			path: __dirname + '/dist/assets/js/basics/01/'
		},
	}


]

// overriding props for the webpack bundling
export let baseProps = {

}

const activeConfigs = [
	{
		entry: './assets/js/basics/01/001-test-import.js', // starting app file

		output: {
			filename: '001-test-import.js', //resulting budle
			path: __dirname +'/../../../dist/assets/js/basics/01/'
		},
	}
]
export { activeConfigs };

</code></pre>
__.__

then import it in :

<pre><code class="language-js">
// webpack.config.babel.js es6 webpack config. enabled

import webpack from 'webpack';
import { activeConfigs, baseProps  } from './lib/webpack/basics/001-basics.js';

// common starter webpack configs can be put here
const defaultConfig = {
  // webpack-dev-server config
  devServer: {
    compress: false,
    inline: true,
    port: 9000
  },

  watch: true,
  watchOptions: {
    ignored: /node_modules/
  }
}

//  currently ones webpack will bundle
activeConfigs.forEach(activeConfig => {
  Object.assign(activeConfig, defaultConfig)
  Object.assign(activeConfig, baseProps)
})


export default activeConfigs

</code></pre>
