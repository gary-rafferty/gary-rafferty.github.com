---
layout: single
title: JavaScript instanceof vs typeof
categories: [software]
tags: [javascript]
---
I've been writing JavaScript for quite some time, but only recently came across [instanceof](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Operators/instanceof), [typeof](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Operators/typeof) on the otherhand I've always been aware of.

Each serve different purposes, and have slight differences.I recently got bitten, so here's a note to self.

_Using typeof, you get a string representation of the object type. Using instanceof, you are comparing the type, specifically if the property of a constructor is in the objects prototype chain._

Here are some exaxmples of the different behaviours.

```javascript

  //objects
  var Person = function() {}

  var gary = new Person();

  console.log(typeof(gary)); // "object"
  gary instanceof Person // true

  //literals
  var str = 'hello world';
  console.log(typeof(str)); // "string"
  str instanceof String // False

  var str1 = new String('hello world');
  console.log(typeof(str1)) // "object"
  str1 instanceof String // true
```

So in JavaScript, given that strings can be literals or objects, the correct way to test for a string would be to test against both cases.

```javascript
  function isString(str) {
    return typeof(str) == 'string' || str instanceof String;
  }
```
