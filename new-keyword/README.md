# The "new" Keyword
In my previous article https://hackernoon.com/understanding-javascript-the-this-keyword-4de325d77f68 I vaguely mentioned the new keyword and that it creates a binding for this. In object-oriented languages new is used whenever you want to instantiate a class.

```javascript
var dog = new Dog();
```

What usually happens is that the constructor function of the class is called and it instantiates the new object. Even though JavaScript’s syntax is pretty much the same, using new causes a different behavior under the hood.

## There are no constructors
First off, we don’t necessarily need a class in order to create an object. In JavaScript we can use a function for that purpose. Constructor functions are just regular functions that happened to be called with the new keyword in front of them. You could argue that there are no constructor functions — only constructor calls, because practically any function can be called to create an object. It doesn’t have to be part of an object or class or defined in any special way.

There are a few steps that the constructor call goes through, but we’ll focus only on those that matter for the current discussion:

A new object is created
this is bound to the new object
Unless the function returns its own object, the call will return the constructed object

```javascript
function Person(name) {
    this.name = name
}
var person = new Person("Alex")
console.log(person.name) // Alex
```

## Summary
To wrap things up, this is not a difficult concept. The main thing to wrap your head around is that any function can be called as a constructor and it creates a binding for this upon doing so.