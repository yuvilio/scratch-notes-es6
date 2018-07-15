---
title: basic es6 - 002 arrow
collection: posts
layout: pages/basic-es6/01/002-arrow.nunj
excerpt: A
---


[Arrow function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)
==============

## shorthand for a function
<pre><code class="language-js">
let getPrice = () => 2.45 // shorthand for anonmyous function. no 'function' wrapping or 'returns'
  // says this function returns 2.45
console.log( typeof getPrice) // its a 'function' type
console.log( 'getPrice(): ', getPrice() ) //2.45


</code></pre>

* what if we want to pass arguments to the function?

<pre><code class="language-js">
let getPriceArgs = (count, added) => count * 3.00 + added // passing arguments to the function
console.log('getPriceArgs(2, 4): ', getPriceArgs(2, 4)) // 10
</code></pre>

no parentheses needed if it's just one parameter


* what if i need more than one line for my code?

<pre><code class="language-js">
let getPriceBlock = (count, taxed) => { //add {} when needing more than one line of code
  let price = count * 2
  if (taxed){
    price *= 1.055
  }
  return price //you'll need the return if using a block for the function
}
console.log('getPriceBlock(5, true): ', getPriceBlock(5, true)) //10.55
</code></pre>


* this index

<a href="#" class="btn btn-arrow">Click here</a>


<pre><code class="language-js">
let invoice = {
  number: 123,
  process: function () {
    console.log('invoice this: ', this)
  }
}
invoice.process() //you get the object literal(invoice)

let invoiceProcessArrow = {
  number: 123,
  process: () => console.log('invoiceProcessArrow.process(): ', this)
}
invoiceProcessArrow.process() //context o the code (document object or Window)

</code></pre>

* no more re-assigning the 'this' context via bind/call/apply


* need default value for your parameters? te parameter is not defined? That works now: .just add = and the the defaultvalue:

<pre><code class="language-js">
let getShippedItems = (count = 500) => 'items shipped:' + count
console.log(getShippedItems()) // 'items shipped:500'

</code></pre>


* what if i have a value to pass for some but want to use the default for others?

You can pass `undefined` for the others  to use the default values
<pre><code class="language-js">
let getShippedItemsLabel = (count = 500, itemType = 'orange') => count + ', ' + itemType
console.log(getShippedItemsLabel(undefined, 'kiwi')) // '500, kiwi'

</code></pre>


* can i use one parameter to figure value of another paramer in the default?

<pre><code class="language-js">
// example where one parameter (vatTax),  relies on value of other parameter
let getCostWithVAT = ( price, vatTax = price * 0.08 ) => price + vatTax
console.log( getCostWithVAT(10) ) //10.8
</code></pre>


* I'm not sure how many parameters I might receive. can i just group them in an array?

Yeah you can use the [rest](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters) (`...`) operator in your parameters list

<pre><code class="language-js">
// after first arg, rest will go into crew array
let getCrewManifest = (ship, ...crew) => ship + ':' + crew.join(', ')
console.log(getCrewManifest('SES Heinz 57', 'Stellar', 'Wilco', 'Dorf')) //  SES Heinz 57:Stellar, Wilco, Dorf
</code></pre>

This function can now take various numbers of arguments rather than a specific number .

* is the rest operator an array?

yep:
<pre><code class="language-js">
let getCrewManifest2 = (ship, ...crew) => crew instanceof Array //true
console.log(getCrewManifest2('SES Heinz 57', 'Stellar', 'Wilco', 'Dorf')
</code></pre>


* what if my function takes individual arguments but i have them in an array. do i have to separately refer to each item?

You can use the [spread ... operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_operator) (it's also ... but this time used in the call, not the function signature)

<pre><code class="language-js">
let ranks = [32, 2, 55, 23]
let maximumRank = Math.min(...ranks) // spread (splits to individual arguments)
console.log(maximumRank) // 2

// handy when you need to pass individual args but they're in an array
let crewProfile = (name, rank) => 'Crewman: ' + name + ' Rank: ' + rank
let crewPersonnelEntry = ['Stellar', 'Executive Officer']
console.log(crewProfile(...crewPersonnelEntry)) // Crewman: Stellar Rank: Executive Officer

// note that spread flattens arrays (rather than creating arrays of arrays)
console.log(['a', ...'hello']) // ["a", "h", "e", "l", "l", "o"]
</code></pre>
