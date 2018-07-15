---
title: basic es6 - 002 classes
collection: posts
layout: pages/basic-es6/02/002-classes.nunj
excerpt:
---

[Classes](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)
-------

classes are interesting and can make  sense in some applications. but they also have [their critics](https://github.com/joshburgess/not-awesome-es6-classes)

> The ES6 class syntax, constructors, the new keyword, etc. are ideas taken from the classical inheritance model to make programmers coming from languages like C++, Java, C#, etc. more comfortable and do not really belong in JavaScript. ES6 class syntax is essentially syntactic sugar that will end up obfuscating the true nature of JavaScript and confusing the next generation of programmers learning it.

keeping that in mind. it's worth getting a basic sense of classes:

> While prototypal inheritance is very powerful in its own right, it is important to know that there is a growing movement among developers, both within and outside of the JS community (Ex: Composition in Golang), to shift away from inheritance in favor of object composition.

you can read more about [object composition](https://medium.com/@dan_abramov/mixins-are-dead-long-live-higher-order-components-94a0d2f9e750) approaches like [functional programming](http://blog.ricardofilipe.com/post/javascript-composition-for-dummies) [traits](https://www.barbarianmeetscoding.com/blog/2016/01/04/safer-javascript-object-composition-with-traits-and-traits-dot-js/) like [traits.js](https://github.com/traitsjs/traits.js)  

* what type is a [class](https://googlechrome.github.io/samples/classes-es6/) ? an object of a class, how can you tell if an object is an ista

<pre><code class="language-js">
{
  // classes are of a function type, since they have some constructor functionality built in
  class CrewMember {
    constructor () {  // comes with a initializing function called 'constructor'
      console.log('constructor here. when you make an object with `new`. i can help initialize some things')
    }
  }
  let stellar = new CrewMember() // instantiate an object of that class using `new`
  // constructor here. when you make an object with `new`. i can help initialize some things

  console.log(typeof CrewMember) // function
  console.log(typeof stellar) // object
  console.log(stellar instanceof CrewMember) // true
  console.log(stellar.constructor) // function CrewMember() { _classCallCheck(this, CrewMember); }
}
</code></pre>

* can my class objects have properties?

<pre><code class="language-js">
{
  // yes, if we want, we can assign a class to a variable
  let Crew = class CrewMember {
    // no commas needed to separate different methods
    // object instances of a class can have properties of their own. associated with `this
    constructor (name, rank) {
      // one approach for instances to have instance based (this) properties
      this.name = name
      this.rank = rank
    }
    greet () { // class method
      return `${this.rank} ${this.name}, reporting for duty!`
    }
  }

  // each object can have it's own separate data
  let stellar = new Crew('Stellar Santiago', 'Lieutenant')
  let wilco = new Crew('Roger Wilco', 'Janitor Second Class')
  console.log(stellar.greet()) // Lieutenant Santiago, reporting for duty!
  console.log(wilco.greet()) // Janitor Second Class Roger Wilco, reporting for duty!

  // adding a method to a class, is the same as adding it to that class's prototype object
  console.log(stellar.greet === Crew.prototype.greet) // true
}
</code></pre>

* can i have subclasses of my class with variations on functionality?

<pre><code class="language-js">

{
  class TerminatorDroid {
    constructor (model = undefined) {
      this.model = model
    }
    // you can use get/set just like with objects (for simple property wrappers)
    get 'modelNumber' () { return `model: ${this.model}` }
  }

  class Arnoid extends TerminatorDroid {
  }

  class Endodroid extends Arnoid {
  }

  let endo = new Endodroid()
  // an object is an instance of it's class and that class's superclass, ..
  console.log(endo instanceof Endodroid) // true
  console.log(endo instanceof Arnoid) // true
  console.log(endo instanceof TerminatorDroid) // true

  let wd = new Arnoid('wd')
  console.log(wd.modelNumber) // model: wd
}

</code></pre>

* constructors share functionality

<pre><code class="language-js">

{
  class TerminatorDroid {
    // then the super class does some work
    constructor (model = undefined) {
      this.model = model
    }
    // you can use get/set just like with objects (for simple property wrappers)
    get 'modelNumber' () { return `model: ${this.model}` }
    getModelNumber () {
      return `model: ${this.model}`
    }

    getEmployer () {
      return `This Could be You`
    }
  }

  class Arnoid extends TerminatorDroid {
    // having the initialization happen from the efforts of two constructors
    // first, the subclass construct does some work
    constructor (model) {
      model = 'ahhnoid line ' + model
      super(model) // iff a subclass has a constructor, it is responsible for calling superclass
      // constructor (if it's only partially overriding it) and passing arguments
    }

    // partially override a method (super still needed)
    get 'modelNumber' () { return `AMN: ${super.modelNumber}` }

    // overriding a parent
    getEmployer () {
      return `Gippazoid Novelty Company`
    }
  }

  let wd = new Arnoid('wd')

  // call method that partially overrides parent's method
  console.log(wd.modelNumber) // AMN: model: ahhnoid line wd

  // use an inherited method from the superclass
  console.log(wd.getModelNumber()) // model: ahhnoid line wd

  // invoke a method that overrides it's parent's method
  console.log(wd.getEmployer()) // Gippazoid Novelty Company
}

</code></pre>

* are static methods and properties available to use for more collective data?

<pre><code class="language-js">

{
  class TerminatorDroid {
    constructor (model = undefined) {
      this.model = model
      TerminatorDroid.count++ // increase count of droids every time a new one is created
    }

    static getCount () { // make statid data available via static method
      return TerminatorDroid.count
    }
  }
  TerminatorDroid.count = 0 // static property

  class Arnoid extends TerminatorDroid {
    constructor (model) {
      model = 'ahhnoid line ' + model
      super(model)
    }
  }

  // instantiate a few droids
  let aahnoid = new Arnoid('ahhnoid')
  let wd = new Arnoid('wd')
  console.log(`count of droids : ${TerminatorDroid.getCount()}`) // count of droids: 2
  console.log(aahnoid, wd)
}
</code></pre>

<!---
<pre><code class="language-js">

</code></pre>
--->
