---
title: basic es6 - 001 init
collection: posts
layout: pages/basic-es6/01/001-init.nunj
excerpt: Is visualization just another shortcut to access much data?
---


A few tests of es6

See console as well

[Let](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let)
* declare variables, supplanting use of `var` declaration
* does away with hoisting:
<pre><code class="language-js">
//rather than undefined. can results in ReferencEerror. since
//let is only processed when reached and not preprocessed and hoisted to top of block.
console.log(a)
let a = 2
</code></pre>

* works within a block scope
<pre><code class="language-js">
  {
    /* within curly braces is within a block scope */
    let b = 2
    console.log(b) //returns 2
  }
  console.log(b) //you get an error: 001-init.js:13 Uncaught ReferenceError: b is not definedat HTMLDocument.<anonymous> (001-init.js:13)
  // a good js linter like standard.js will pick this up
  // and print: "'b' is not defined."
</code></pre>


* blocks don't redefine variables with the same name:

<pre><code class="language-js">
let c = 20
{
  let c = 4
}
console.log('c = ', c) //c == 20 since this log statmentis in the scope where it is declared and defined as such
</code></pre>

similar within for block
<pre><code class="language-js">
let e = 10
for (let e = 2; e < 5; e++){
  //the for loop having a block {}, e in here goes from 2 to 4
  console.log('e = ', e)
}
</code></pre>


[Const](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const)
====


* need to keep the variable at one value? you now have `const`
<pre><code class="language-js">
const ca = 12
console.log('ca = ', ca)
ca = 20 //if you try to redefine it , you get a = 4
//Uncaught TypeError: Assignment to constant variable.
</code></pre>

if you have a linter like standard.js, it'll also raise a flag:

 ```'"ca" is read-only' ... "'ca' is constant."```

Similarly, the constant needs a value from the start

<pre><code class="language-js">
const cb // you get an error if you try to declare a constant
// without setting a value:
// Uncaught SyntaxError: Missing initializer in const declaration
</code></pre>

The linter will also spot it:
``` Parsing error: Unexpected token ```


* if with { } is also a new scope
<pre><code class="language-js">

const cc = 44
if (cc > 10){
  const cc = 11 // since we're in a new block scope ({ .. } ),
  // this is a new declaration
  console.log('inner if (block) scope. cc = ', cc)  // 11
}
console.log('outer block scope. cc = ', cc) // 44
</code></pre>
