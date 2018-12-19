# The "new" Keyword

One specific part of the JavaScript language that brings a lot of misunderstanding with it is the _new_ keyword. If JS is not your first programming language and you have done some OOP, you are undoubtedly used to seeing the _new_ keyword used whenever a class is instantiated.

```javascript
class Dog {
  constructor() {
    ...
  }
}

const myDog = new Dog();
```

Thanks to all the features that came to JavaScript with ES6 the code snippet above works exactly as you would expect. The typical behavior in languages that have classes is pretty straight forward. The arguments are passed to the constructor function of the class and it gives you a new object.

While the JavaScript syntax is visually the same it is only syntactic sugar to what happens under the hood.

## There are no constructors

The first thing we need to get out of the way is that we don't need to have a class in order to use _new_. In JavaScript we can use a function instead of a class to achieve the same result. In fact, we can technically call any function with _new_ before it.

```javascript
function Dog(name) {
  this.name = name;
}

const myDog = new Dog("Lassie"); // { name: 'Lassie' }
console.log(myDog.name); // Lassie
```

Functions that are called with the _new_ keyword are usually called constructor functions. However, we could argue that there are no constructor functions, only constructor calls since any function can be used as a constructor.

```javascript
function getName() {
  return "Alex";
}

const test = new getName(); // { }
```

Here the `test` variable will be assigned an empty object as a value since no bindings were made inside the `getName` function. It doesn't matter what the function's return value is - if we make a constructor call we will always receive an object.

## Constructor calls

There are a few things that happen whenever we call a function with _new_:

- A new object is created
- _this_ is bound to the new object
- The new object is returned, _unless_ the function returns its own object
- The newly created object is assigned as value to the variable

In other words, we tell JavaScript to execute a function and return an object. Any _this_ bindings in the function are made on the object being returned.

```javascript
function Dog(breed) {
  this.name = "Lassie";

  return { breed };
}

const myDog = new Dog("Yorkie"); // { breed: 'Yorkie' }
myDog.name; // undefined
```

Even though a name is added to _this_, since the function returns an object it will be used rather than the regular binding.

## ES6

From ES6 forward we can use the class syntax and the constructor like in typical object oriented languages. I find this to be a great addition since it makes the language more accessible to people comming from an OO language. Still, it's important to note that classes are just syntactic sugar on top of JavaScript's existing functionality.
