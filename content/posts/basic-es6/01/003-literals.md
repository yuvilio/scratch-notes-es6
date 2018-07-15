---
title: basic es6 - 003 literals
collection: posts
layout: pages/basic-es6/01/003-literals.nunj
excerpt:
---


*  Object literals now allow variables that match object keys to automatically populate an object with values for those keys:

<pre><code class="language-js">
let name = 'Stellar'
let title = 'Executive Offer'
let crewman = {
  name,
  title
}
console.log(crewman) // {name: "Stellar", title: "Executive Offer"}
</code></pre>


* similarly functions ofthis object don't need a key: function(){} . format. just use the name right away and skip the colon:

<pre><code class="language-js">
{
  let name = 'Stellar'
  let title = 'Executive Offer'
  let crewman = {
    name,
    title,
    announce () {
      return this.title + ' ' + this.name + ', reporting for duty!'
    }
  }
  console.log(crewman.announce()) // Executive Offer Stellar, reporting for duty!
}
</code></pre>

note that the shorthand can be overwritten :

<pre><code class="language-js">
{
  let name = 'Stellar'
  let title = 'Executive Offer'
  let crewman = {
    name: 'Wilco',
    title: 'Janitor Second Class',
    announce () {
      return this.title + ' ' + this.name + ', reporting for duty!'
    }
  }
  console.log(crewman.announce()) // Executive Offer Stellar, reporting for duty!
  console.log(name, title); // Stellar Executive Offer
}
</code></pre>

can function names have spaces? special charachters?

<pre><code class="language-js">
{
  let name = 'Stellar'
  let title = 'Executive Offer'
  let crewman = {
    name,
    title,
    'announce yourself' () { // function name can be a phrase
      return this.title + ' ' + this.name + ', reporting for duty! emojy'
    }
  }
  // use [] notation for functions with spaces
  console.log(crewman['announce yourself']()) // Executive Offer Stellar, reporting for duty!
}
</code></pre>

* This allows emojis

<pre><code class="language-js">
{
  let name = 'Stellar'
  let title = 'Executive Offer'
  let crewman = {
    name,
    title,
    '🎙' () { // using a microphone emoji as function name
      return this.title + ' ' + this.name + ', reporting for duty!'
    }
  }
  // use [] notation for functions with special charachters like emojis
  console.log(crewman['🎙']()) // Executive Offer Stellar, reporting for duty!
}
</code></pre>

* can the property name be dynamic as well? or a method name?

yep, use [] in it's definition to use one from a variable . simiarly use [] to refer to it within the object. similarly, methods :

<pre><code class="language-js">
{
  let field = 'name' // dynamic property name
  let methodField = 'announce'
  let name = 'Stellar'
  let crewman = {
    [field]: name,
    [methodField + '01'] () {
      // using dynamic property name with brackets
      return this[field] + ', reporting for duty!'
    }
  }
  console.log(crewman[methodField + '01']()) // Stellar, reporting for duty!
}
</code></pre>

you can also adjust the field name with an expression so [field + '-02'] to be 'name-01' in this example


* do object literals provide conveniences to wrap properties with getters and setters?

They do. offering [get](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/get) and [set](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/set)

* here
<pre><code class="language-js">
{
  // i want to store the crew name plainly and keep track of it's order. i'll use an array and setter to manager it
  // getting wise, i want to get the number of the name along with the name when displaying. i'll use a getter
  let crew = {
    crew: [],
    get 'current' () { return this.crew.slice(-1)[0] },
    set 'current' (name) { this.crew.push(this.crew.length + ': ' + name) }
  }

  // notice the front end knows nothing about the array data structure nor that the data is available only
  // in the 'number: name' format i'm enforcing with my setter
  // to the outside, we're still just using '=' and overloading it's functionality
  crew.current = 'Stellar'
  console.log(crew.current) // 0: Stellar
  crew.current = 'Wilco'
  console.log(crew.current) // 1: Wilco
}
</code></pre>

* do dynamic field names work with gettsr and setters?

<pre><code class="language-js">

// can use a dynamic field name with getters and setters
{
  let field = 'current'
  let crew = {
    crew: [],
    get [field] () { return this.crew.slice(-1)[0] },
    set [field] (name) { this.crew.push(this.crew.length + ': ' + name) }
  }

  crew[field] = 'Stellar'
  console.log(crew.current) // 0: Stellar
  crew[field] = 'Wilco'
  console.log(crew.current) // 1: Wilco
}
</code></pre>


iterate through array?

You can use the [for..of](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) loop

<pre><code class="language-js">

// can use a dynamic field name with getters and setters
let crew = ['Stellar', 'Dorf', 'Wilco']
// iterate through array
for (let crewman of crew) {
  console.log(crewman)
} // Stellar , Dorf , Wilco
</code></pre>

* need an index? you can just set one:

<pre><code class="language-js">
let crew = ['Stellar', 'Dorf', 'Wilco']
// want to use an index? set one
let i = 0
for (let crewman of crew) {
  i++
  console.log(i + ': ' + crewman)
} // 1: Stellar, 2: Dorf, 3: Wilco
</code></pre>

* need to interpolate a variable into a string? use backtick [template strings](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)

<pre><code class="language-js">

{
  // use backticks for template strings and ${} to interpolate variables
  let thanks = 'Thank You, Thank YOU, thank you thank you thank you'
  console.log(`I'd like to thank each and every one of you. So.. ${thanks}`)
    // I'd like to thank each and every one of you. So.. Thank You, Thank YOU, thank you thank you thank you
}

</code></pre>

* need to break the string up to different lines? just hit enter. no quotes or spacial charchters needed:

<pre><code class="language-js">

{
  // a nice thing about templates literals is letting you break up a string to many lines without fuss
  let thanks = `Thank You, Thank YOU, Thank you...
  thank you thank you thank you
  thank you thank _you`
  console.log(`I'd like to thank _each_ and every one of you:
  So.. ${'ahem..: ' + thanks}`) // expressions are allowd in the interpolation
  // I'd like to thank _each_ and every one of you:
  //     So.. ahem..: Thank You, Thank YOU, Thank you...
  //     thank you thank you thank you
  //     thank you thank _you
}

</code></pre>

* what if i want to be able to process the template literal. That is i want to break it up to strings and where the interpoation happens

You can use [tagged template literals](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals#Tagged_template_literals)


<pre><code class="language-js">

{
  let thankCrewTags = (segments) => console.log(segments) // ["I'd like to thank"]
  thankCrewTags`I'd like to thank` // invoke the tag (function) passing it the template string (no parentheseses)
}
</code></pre>

so if you just pass the plain string, you get it in an array. not too interesting just yet. Let's add some interpolation


<pre><code class="language-js">

{
  let thankCrewTags = (segments, ...values) => console.log(segments, values)
    //you get two arrays, the string segments (non-interpolated) and the values (interpolated). in separate arrays
    // ["I'd like to thank. ", ". Ahem, so ", "", raw: Array(3)] (2)
    // ["each and every one of you", "Thank you,.. Thank you.. thank you"]
  let thankee = 'each and every one of you'
  let firsts = 'Thank you,.. Thank you.. thank you'
  thankCrewTags`I'd like to thank. ${thankee}. Ahem, so ${firsts}`
}
</code></pre>


excellent. now we can process the pieces in a more custom way with this tag. simply process the pieces in your tag literal function and return the constructed result

<pre><code class="language-js">
{
  let thankCrewTags = (segments, ...values) => {
    let overture = segments[0]

    return overture + '. ahem: ' + values[0]
  }

  let thankee = 'each and every one of you'
  let firsts = 'Thank you,.. Thank you.. thank you'
  console.log(thankCrewTags`I'd like to thank. ${thankee} ${firsts}`)
}
</code></pre>

destructuring
=============

[destructuring](https://ponyfoo.com/articles/es6-destructuring-in-depth) has us breaking down a structure looking at it's parentheseses


* how can i quickly assign a bunch of variables from an array?

you can destructure the array by grouping the variables into an array and they'll get assigned pieces of the array you want. including new arrays with the ...

<pre><code class="language-js">

{
  // you can skip elements when destructuring. use commas to move to the next element
  let medbayOccupants = ['Wilco', 'Stellar', 'Jebba', 'Doc']
  let [, patient, ...staff] = medbayOccupants
  console.log(patient) // Stellar ["Jebba", "Doc"]
  staff.push('')
}
</code></pre>


<pre><code class="language-js">
{
  // you can skip elements when destructuring. use commas to move to the next element
  // you can add default values in case the array doesn't provide ones at that index spot
  let medbayOccupants = ['Wilco', 'Stellar', 'Jebba', 'Doc']
  let [visitor, patient, , , maint = 'Janitor'] = medbayOccupants
  console.log(patient, maint) // Stellar Janitor
}
</code></pre>

<pre><code class="language-js">
{
  // you can destructure an array within an array. it's recursive. just use an array destructure
  let medbayOccupants = ['Wilco', 'Stellar', ['Jebba', 'Doc']]
  let [visitor, , [researcher, doctor]] = medbayOccupants
  console.log(researcher) // Jebba
}
</code></pre>


<pre><code class="language-js">
{
  // functions can also destructure passed input in their parameters. here we grab parameters from within a passed in array
  let medbayReport = ([visitor, patient, ...others]) => 'Current patient: ' + patient
  let medbayOccupants = ['Wilco', 'Stellar', 'Jebba', 'Doc']
  console.log(medbayReport(medbayOccupants))
}
</code></pre>


<pre><code class="language-js">
{
  let medbay = {
    patient: 'Stellar',
    visitor: 'Wilco',
    report () {
      return `patient ${this.patient} treated with visitor ${this.visitor} visiting `
    }
  }

  // want to map the properties to different names? use propname: varname in the descturturing
  let {patient: currentPatient, visitor: currentVisitor} = medbay
  console.log(`${currentPatient} , ${currentVisitor}`) // Stellar , Wilco
}
</code></pre>



<pre><code class="language-js">

{
  let medbay = {
    patient: 'Stellar',
    visitor: 'Wilco',
    report () {
      return `patient ${this.patient} treated with visitor ${this.visitor} visiting `
    }
  }

  // the variables used in destructuring can be declared separately
  // just wrap the destructuing expression in () so that the compiler doesn't get confused by the {} to be code blocks
  // rather what it is (an object)
  let currentPatient, currentVisitor
  ({patient: currentPatient, visitor: currentVisitor} = medbay)
  console.log(`${currentPatient} , ${currentVisitor}`)
}

</code></pre>

<pre><code class="language-js">

{
  // you can destructure an array in a for loop to assign variables it's parts from the get go
  let partners = [['Wilco', 'Stellar'], ['Dorf', 'Kielbassa']]
  for (let [partnerA, partnerB] of partners) {
    console.log(` ${partnerA} with ${partnerB}`)
  }
}

</code></pre>
