---
title: basic-es6 - 003 babel intro
collection: posts
layout: pages/basic-es6/02/003-babel-intro.nunj
excerpt:
---

Babel intro

What's babel ? It's a javascript parser and transformer ( sometimes called a transpiler). The idea is that you write javascript with the syntax you want (newer syntax with various notations that are not fully supported) and you dictate the resulting javascript, to an older broader supporting javascript, or perhaps a semi older  somewhat newer version. Depending on various considerations.

There are a lot of transpilers out there. Why use babel?

Babel has a following similar to [postcss](). It's appeal is in it's modularity and plugins. You can dictate which transformations you want to happen and leave out the ones you don't. It has an extensive set of plugins that  


How can i test what a transform does?

There are a few approaches. One is to just install the babel cli globally and then add plugins to it (via a flag or the `.babelrc` file )

<pre><code class="language-bash">
  $ npm install -g babel-cli
</code></pre>

Let's test this out via flag or config file with our first plugin:

## Arrow functions

Want to use es6 arrow functions but need to make sure older browsers still work?
[transform-es2015-arrow-functions](https://babeljs.io/docs/plugins/transform-es2015-arrow-functions/) is one approach. it'll transpile functions back to older js function syntax.

Install the plugin to your project's modules.

<pre><code class="language-bash">
$ npm install --save-dev babel-plugin-transform-es2015-arrow-functions
</code></pre>

Let's give it a go with two shorthand es6 js functions . the first the simplest. and the second taking an argument

<pre><code class="language-js">
001-arrow-functions.js
let a = () => {};
let a2 = (b) => b;
</code></pre>

We'll use the --plugins flag to invoke the transformation we want to use.

the result is a friendlier es5 compatible version that older browsers can run.

<pre><code class="language-bash">
$ babel --plugins transform-es2015-arrow-functions 001-arrow-functions.js
let a = function () {};
let a2 = function (b) {
  return b;
};
$
</code></pre>

It can get tiresome to add the plugins in a flag. also, some of these plugins take configuration arguments. It'd be nice if we can invoke the plugins from a config file. That's where the `.babelrc` file comes in. it's presens in the directory is used by the babel command

<pre><code class="language-bash">
$ cat .babelrc
{
  "plugins": ["transform-es2015-arrow-functions"]
}
$
</code></pre>

invoking babel, automatically uses that file and so that plugin:

<pre><code class="language-bash">
$ babel  001-arrow-functions.js
let a = function () {};
let a2 = function (b) {
  return b;
};

$
</code></pre>


## validate const declarations

Need to validate that your es6 constant (`const`) declarations are written correctly?  [check-es2015-constants](https://babeljs.io/docs/plugins/check-es2015-constants/) can help.

<pre><code class="language-js">
// 002-check-const.js
const a = 1;
a = 2; //this should trigger an error. constants values are not allowed to be adjusted
</code></pre>


Running the transform on that triggers the validaor to spot the error:
<pre><code class="language-js">
$ babel 002-check-const.js
SyntaxError: 002-check-const.js: "a" is read-only
  1 | const a = 1;
> 2 | a = 2; //this should trigger an error. constants values are not allowed to be adjusted
    | ^
  3 |
$
</code></pre>

Of course, if you didn't try to change the constant, there would be no error:

<pre><code class="language-bash">
$ cat 002-check-const.js
const a = 1;
$ babel 002-check-const.js
const a = 1;

$
</code></pre>

## Make the  rest and spred operators available in es5

One approach to that is the [transform-object-rest-rest](https://babeljs.io/docs/plugins/transform-object-rest-rest/) plugin .

So recall  how
<pre><code class="language-js">
// 003-transform-rest.js
let { x, y, ...z } = { x: 1, y: 2, a: 3, b: 4 };
console.log(x); // assigned the  value the object maps to it
console.log(z); // gets assigned the the remaining values as an object { a: 3, b: 4 }
</code></pre>

Here's the the rest operator gives you. handy for a function processing a flexible amount of  passed parameters. you pass in an object and variables can be easily assigned values from that object, in mappings of your choice

<pre><code class="language-bash">
$ node 003-transform-rest.js
1
{ a: 3, b: 4 }
$
</code></pre>

that's neat but what if you need that rest to work in older browsers ?


<pre><code class="language-bash">
$ cat .babelrc
{
  "plugins": ["transform-object-rest-spread"]
}
$
$ babel 003-transform-rest.js
function _objectWithoutProperties(obj, keys) { var target = {}; for (var i in obj) { if (keys.indexOf(i) >= 0) continue; if (!Object.prototype.hasOwnProperty.call(obj, i)) continue; target[i] = obj[i]; } return target; }

let _x$y$a$b = { x: 1, y: 2, a: 3, b: 4 },
    { x, y } = _x$y$a$b,
    z = _objectWithoutProperties(_x$y$a$b, ["x", "y"]);
console.log(x); // assigned the  value the object maps to it
console.log(z); // gets assigned the the remaining values as an object { a: 3, b: 4 }

$

</code></pre>
_._


That function with the for loop in it achieves the spread operator Let's confirm it
<pre><code class="language-bash">
$ babel 003-transform-rest.js
$ babel 003-transform-rest.js > es5-output-003-transform-rest.js
$ node es5-output-003-transform-rest.js
1
{ a: 3, b: 4 }
$

</code></pre>


same output. as expected.

That plugin can also demonstrate some spread usages . for example. suppose you're copying an object while partially replacing some of the values. `spread` makes this less messy

<pre><code class="language-js">
// 004-transform-spread.js
// you can use a spread to quickly grab key/value mappings from an object
let z = { x: 1, y: 2, a: 3, b: 4 };
let n = {  ...z , x: 'hey', b: ['what'] }; //selectively overwrite some of the values
console.log(n); // { x: 'hey', y: 2, a: 3, b: [ 'what' ] }
$

</code></pre>

Running this code:, we see the new object with new values for some of it's keys

<pre><code class="language-bash">
$ node 004-transform-spread.js
{ x: 'hey', y: 2, a: 3, b: [ 'what' ] }
$

</code></pre>

what if we need this to work on an older browser?

<pre><code class="language-bash">
$ cat .babelrc
{
  "plugins": ["transform-object-rest-spread"]
}
$
$ babel 004-transform-spread.js
var _extends = Object.assign || function (target) { for (var i = 1; i < arguments.length; i++) { var source = arguments[i]; for (var key in source) { if (Object.prototype.hasOwnProperty.call(source, key)) { target[key] = source[key]; } } } return target; };

// you can use a spread to quickly grab key/value mappings from an object
let z = { x: 1, y: 2, a: 3, b: 4 };
let n = _extends({}, z, { x: 'hey', b: ['what'] }); //selectively overwrite some of the values
console.log(n); // { x: 'hey', y: 2, a: 3, b: [ 'what' ] }

$
</code></pre>
_._

You get this nice function that does the mapping in es5 friendly code. Let's check that it works as expected:
<pre><code class="language-bash">
$ babel 004-transform-spread.js  > es5-output-004-transform-spread.js
$ node es5-output-004-transform-spread.js
{ x: 'hey', y: 2, a: 3, b: [ 'what' ] }
$
</code></pre>


## es6 classes in es5


Need to use es6 classes in es5?   you can use [transform-es2015-classes](https://babeljs.io/docs/plugins/transform-es2015-classes/)

Here's a class and subclass in es6

<pre><code class="language-js">
// 005-classes.js
class Animal {
 constructor(name) {
   this.name = name;
 }

 speak() {
   console.log(this.name + ' makes a noise.');
 }
}

class Dog extends Animal {
 speak() {
   console.log(this.name + ' barks.');
 }
}

var d = new Dog('Mitzie');
d.speak(); // Mitzie barks.
</code></pre>

Let's run this to see these classes in action:
<pre><code class="language-bash">
$ node 005-classes.js
Mitzie barks.
$
</code></pre>

Need this in an es5 browser? no prob:
<pre><code class="language-bash">
$ babel 005-classes.js
var _createClass = function () { function defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, descriptor.key, descriptor); } } return function (Constructor, protoProps, staticProps) { if (protoProps) defineProperties(Constructor.prototype, protoProps); if (staticProps) defineProperties(Constructor, staticProps); return Constructor; }; }();

function _possibleConstructorReturn(self, call) { if (!self) { throw new ReferenceError("this hasn't been initialised - super() hasn't been called"); } return call && (typeof call === "object" || typeof call === "function") ? call : self; }

function _inherits(subClass, superClass) { if (typeof superClass !== "function" && superClass !== null) { throw new TypeError("Super expression must either be null or a function, not " + typeof superClass); } subClass.prototype = Object.create(superClass && superClass.prototype, { constructor: { value: subClass, enumerable: false, writable: true, configurable: true } }); if (superClass) Object.setPrototypeOf ? Object.setPrototypeOf(subClass, superClass) : subClass.__proto__ = superClass; }

function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

let Animal = function () {
  function Animal(name) {
    _classCallCheck(this, Animal);

    this.name = name;
  }

  _createClass(Animal, [{
    key: 'speak',
    value: function speak() {
      console.log(this.name + ' makes a noise.');
    }
  }]);

  return Animal;
}();

let Dog = function (_Animal) {
  _inherits(Dog, _Animal);

  function Dog() {
    _classCallCheck(this, Dog);

    return _possibleConstructorReturn(this, (Dog.__proto__ || Object.getPrototypeOf(Dog)).apply(this, arguments));
  }

  _createClass(Dog, [{
    key: 'speak',
    value: function speak() {
      console.log(this.name + ' barks.');
    }
  }]);

  return Dog;
}(Animal);

var d = new Dog('Mitzie');
d.speak(); // Mitzie barks.

# let's run it to make sure the code works he same:

$ babel 005-classes.js > es5-output-005-classes.js
$ node es5-output-005-classes.js
Mitzie barks.
$

</code></pre>
_._

So wow, that's a lot of js infrastructure to support classes working similarly in es5. but you don't have to worry about it from the es6 side.

## For of

transform-es2015-for-of

want to use the `for..of` iterator in the browser? no problem, , use the [transform-es2015-for-of](https://babeljs.io/docs/plugins/transform-es2015-for-of/) plugin

<pre><code class="language-js">
// 006-for-of.js
let foo = ['a', 5, 'there'];
for (var i of foo) {
	console.log(i);
}
</code></pre>  

<pre><code class="language-bash">
$ node 006-for-of.js
a
5
there
$
</code></pre>

Let's have a look at how this transform adapts for..of to work in es5 :

<pre><code class="language-bash">
$ cat .babelrc
{
  "plugins": ["transform-es2015-for-of"]
}
$ babel 006-for-of.js
let foo = ['a', 5, 'there'];
var _iteratorNormalCompletion = true;
var _didIteratorError = false;
var _iteratorError = undefined;

try {
	for (var _iterator = foo[Symbol.iterator](), _step; !(_iteratorNormalCompletion = (_step = _iterator.next()).done); _iteratorNormalCompletion = true) {
		var i = _step.value;

		console.log(i);
	}
} catch (err) {
	_didIteratorError = true;
	_iteratorError = err;
} finally {
	try {
		if (!_iteratorNormalCompletion && _iterator.return) {
			_iterator.return();
		}
	} finally {
		if (_didIteratorError) {
			throw _iteratorError;
		}
	}
}

#let's confirm this transform  results in the same output:

$ babel 006-for-of.js > es5-output-006-for-of.js
$ node es5-output-006-for-of.js
a
5
there
$

</code></pre>
_._

##  short hand defineProperties

ES6 offers some conveniences for assigning values within an object (key value pairs). when the

<pre><code class="language-js">
// 007-shorthand.js
// shorthand object keys
let f = (a, b, c) => {
	// an object maps a key to a value
	// since you're just referencing values, you're implying the name of the value shuld serve as the key
	let o = {a, b, c}; // {'a': a, 'b': b, 'c': c}

	console.log(o)
}


f(2, 'hey', ['bla', 5]);

// shorthand method
var obj = {
	getGreet() { // shortahdn for a function/method (notice we don't need to use function(){} explicitoy
		return 'hey';
	}
}

console.log(obj.getGreet());

</code></pre>


<pre><code class="language-bash">
$ node 007-shorthand.js
{ a: 2, b: 'hey', c: [ 'bla', 5 ] }
hey
$

</code></pre>

the babel transform makes these convenices available by carrying out the maping and translating to full function notation:

<pre><code class="language-bash">
$ babel 007-shorthand.js
// shorthand object keys
let f = (a, b, c) => {
	// an object maps a key to a value
	// since you're just referencing values, you're implying the name of the value shuld serve as the key
	let o = { a: a, b: b, c: c }; // {'a': a, 'b': b, 'c': c}

	console.log(o);
};

f(2, 'hey', ['bla', 5]);

// shorthand method
var obj = {
	getGreet: function () {
		// shortahdn for a function/method (notice we don't need to use function(){} explicitoy
		return 'hey';
	}
};

console.log(obj.getGreet());

$
$ cat .babelrc
{
  "plugins": ["transform-es2015-shorthand-properties"]
}
$ babel 007-shorthand.js > es5-output-007-shorthand.js
$ node es5-output-007-shorthand.js
{ a: 2, b: 'hey', c: [ 'bla', 5 ] }
hey
$


</code></pre>

## use of spread

More direct use of spread than the previous plugin above is available via [transform-es2015-spread](https://babeljs.io/docs/plugins/transform-es2015-spread/)


<pre><code class="language-js">
// 008-spread.js
let a = ['a', 'b', 'c'];
let b = [...a, 'foo']; //add elements of array via the spread destructuring operator

console.log(b);
</code></pre>


<pre><code class="language-bash">
$ node 008-spread.js
[ 'a', 'b', 'c', 'foo' ]
$

</code></pre>

The new array has the old one's values via spread . Let's have this work via es5

<pre><code class="language-bash">
$ cat .babelrc
{
  "plugins": ["transform-es2015-spread"]
}
$ babel 008-spread.js
let a = ['a', 'b', 'c'];
let b = [].concat(a, ['foo']); //add elements of array via the spread destructuring operator

console.log(b);

$ babel 008-spread.js > es5-output-008-spread.js
$ node es5-output-008-spread.js
[ 'a', 'b', 'c', 'foo' ]
$
</code></pre>


## template literals

Template literals make assembling useful output strings more pleasant. [transform-es2015-template-literals](https://babeljs.io/docs/plugins/transform-es2015-template-literals/) helps use it in a broader context:

<pre><code class="language-js">
//009-template-literals.js
let bar = "folks";
console.log( `hey ${bar}` ); //template literal interpoating a variable
</code></pre>

<pre><code class="language-bash">
$ node 009-template-literals.js
hey folks
$
</code></pre>


Need to use it in es5 ? The transform-es2015-template-literals plugn helps .

<pre><code class="language-bash">
$ cat .babelrc
{
  "plugins": ["transform-es2015-template-literals"]
}
$ babel 009-template-literals.js
let bar = "folks";
console.log("hey " + bar); //template literal interpoating a variable

$ babel 009-template-literals.js  > es5-output-009-template-literals.js
$ node es5-output-009-template-literals.js
hey folks
$
</code></pre>
