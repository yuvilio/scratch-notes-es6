---
title: basic es6 - 001 modules
collection: posts
layout: pages/basic-es6/02/001-modules.nunj
excerpt:
---

Modules
-------

Need to break up code to more manageable reusable chunks? One helper in that is the module. We can take a module and make it do something useful :

<pre><code class="language-js">
// './modules/test-module.js'
let greeting = 'hey there ' // this variable isn't exported.
// not available for use outside of this module

// this variable IS exported. available to imported by other files
// it uses it's internal valriable (greeting)
export let hello = `${greeting}, from this module variable`

</code></pre>

then we just `import` that useable bit in our files
<pre><code class="language-js">
import { hello } from './modules/test-module.js'


  // use a value fetched from a module
  console.log(hello) // hey there , from this module variable

</code></pre>


* need to export multiple things? no problem. add a new export for each item you want. need to [import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import) them with different names than the one the module uses? just use an `as` alias

<pre><code class="language-js">
// './modules/002-multiple-exports.js'
// a module can export a bunch of object/functions,..
export let matineeMovie = 'Space Quest 5'
export let mainMovie = 'Tango & Cash 27'

</code></pre>

<pre><code class="language-js">
// you can import multple exported things from a module
// you can also rename them using 'as' if that helps
import { matineeMovie, mainMovie as featureFilm } from './modules/002-multiple-exports.js'

console.log(`playing today  first is ${matineeMovie} and then ${featureFilm}`) // playing today  first is Space Quest 5 and then Tango & Cash 27
</code></pre>


* you can use `default` to designate the main thing a module exports:

<pre><code class="language-js">
// './modules/003-default-export.js'
// a module can have one default export
// handy when a module has a main functionality it provides
export let currentFlick = 'Tango & Cash 27'
let price = '12 Buckzoids'
export default price

</code></pre>


<pre><code class="language-js">
// you can import the designated default exported item from a module . no curly brackets to indicated you're exporting
// the default . also the naming is up to you:
import flickPrice from './modules/003-default-export.js'
console.log(`current flick price: ${flickPrice}`) //current flick price: 12 Buckzoids

//could have also import { default as flickPrice, currentFlick } , for multiple imports including the default one

</code></pre>




<pre><code class="language-js">
// './modules/004-wildcard-object-import.js'

export let currentFlick = 'Tango & Cash 27'
export let price = '12 Buckzoids'

// could have also done the export at the end: export { currentFlick, price }
</code></pre>

<pre><code class="language-js">

// you can import everything a module exports into an object , using the *
import * as cinemaNowPlaying from './modules/004-wildcard-object-import.js'

console.log(`now playing ${cinemaNowPlaying.currentFlick} for only ${cinemaNowPlaying.price}!`) // now playing Tango & Cash 27 for only 12 Buckzoids!
</code></pre>

<!---
<pre><code class="language-js">

</code></pre>
--->
