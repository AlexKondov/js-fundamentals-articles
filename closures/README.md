# Closure
You’ve undoubtedly been using closures for quite a while now for they have a variety of applications. Every time you’ve added a callback for an event handler you’ve used the magical properties of the closure.

I’ve come across different one sentence explanations for them but the one that clicked the most for me was given by Kyle Simpson:

> Closure is when a function is able to remember and access its lexical scope even when that function is executing outside its lexical scope.

This explanation can be a bit overwhelming at first so we will break it down piece by piece and see that closures are no dark magic after all.

We won’t be going into detail about scope in this article (there will be a separate one on this topic) but it’s essential to know what it is in order to understand how the closure works. Scope is practically the part of the code in which certain variables and functions are contained. In JavaScript every function creates a new scope and it’s variables and passed arguments will be available only inside of it.

If you declare a variable inside a function it won’t be available outside of it. But we can also define functions inside of other functions which will have a scope on their own inside of them. Now the special thing about those nested functions is that they can access their parent’s variables.

It’s not that special to be honest, since every function defined in the global scope can access the variables in it, but I’m getting to something here. Those nested functions that we mentioned can access their parent function’s scope but they themselves can’t be called from outside of the parent function. Unless we expose them somehow.

We can expose that inner function so it can be used in the global scope. Great! Now we can call it and use it as we wish. But let’s imagine that this exposed inner function actually references one of the variables in it’s parent’s scope. Will that be a problem? No, of course it won’t because it’s a Closure!

## Closures are exposed nested functions
I am not sure if this is the best definition that can be given for closures, but it captures the essence of the term well enough. Closures are exposed functions which can access the scope of their parent even if we call them from the outside. Do you get that explanation with the lexical scope that we mentioned earlier now?

```javascript
function person(name) {
  return {
    greet: function() {
      console.log('hello from ' + name)
    }
  }
}

let alex = person('alex');
alex.greet(); // hello from alex
console.log(alex.name); // undefined
console.log(name); // will throw ReferenceError
```


Here we define a person function that takes a single parameter name. It returns an object which has a greet function as one of it’s properties. As we now know, this exposed greet function will still have access to its parent’s arguments even when we call it like this. So even that the name variable is not explicitly defined in it’s scope greet will fetch it from its parent’s because it is a closure.

Not too difficult to understand and you have probably taken advantage of this behaviour numerous times. Before I started reading about closures I never thought of all the dark magic that is going on behind the curtain so I can achieve encapsulation and use modules.

Whoa, whoa… Modules? Encapsulation? Those came out of the blue.

## Modules and encapsulation with Closures

One of the first things that I learned when I started diving deeper into the JavaScript maelstrom is that there are a lot of complex sounding words that have a really practical explanation to them. Modules and encapsulation are the perfect examples of such terms. We’ll use the same strategy and break down each one to understand it thoroughly by starting with encapsulation.

Encapsulation is one of the basic principles of programming. Those who have studied OOP are familiar with the concept but for those who are not — this is essentially a hiding mechanism which allows us to keep some of our data private. Often we don’t want to expose all the contents of a function to the global scope, we want most of it to remain private and untouchable.

This is where closures are really handy. We can utilise their power to access the parent scope even when they are called outside of it to achieve proper encapsulation. We can have many functions and variables inside our parent function and by using a closure we can expose to the world only the functionality that we desire.

With closures we can define a public API for our function and keep everything else private.
Now that we have encapsulation covered, we just need to put it in practice. The implementation of this concept in JavaScript is done using the module pattern.

## Modules
In ES6 we have better file-based modules which we can use with the import and export keywords, but it’s important to note that they are just syntactic sugar over what we are about to see.

```javascript
function Person(firstName, lastName, age) {
  var private = 'this is a private member';

  return {
    getName: function() {
      console.log('My name is ' + firstName + ' ' + lastName);
    },
    getAge: function() {
      console.log('I am ' + age + ' years old')
    }
  }
}

let person = new Person('Alex', 'Kondov', 22);
person.getName();
person.getAge();
console.log(person.private); //undefined
```

This is a fairly simple example to demonstrate how we can keep some of the function’s data private. We can have other nested functions and even use them in the exported ones but not expose them.

```javascript

function Order (items) {
  const total = items => {
    return items.reduce((acc, curr) => {
      return acc + curr.price
    }, 0)
  }
  
  const addTaxToPrice = price => price + (price * 0.2)
  
  return {
    calculateTotal: () => {
      return addTaxToPrice(total(items)).toFixed(2)
    }
  }
}

const items = [
  { name: 'Toy', price: 14.99 },
  { name: 'Candy', price: 7.99 }
]

const order = Order(items)
console.log(order.total) // undefined
console.log(order.addTaxToPrice) // undefined
console.log(order.calculateTotal()) // 27.58

```


In this slightly more realistic example we have a function which returns an order object. The only exposed function is calculateTotal. It has a closure over the Order function which allows it to use its variables and passed arguments. It also hides what is actually happening when you are calculating the order total, allowing you to add a shipping cost or something else in the future.

Weird parts
Now, JavaScript has it’s weird parts. In fact some of them are so weird that they can be the cause of serious headache and restless nights of debugging. Closures are no different when we don’t use them properly.

The following code is often given at JavaScript interviews with the task to guess it’s output.

```javascript
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer () {
    console.log(i);
  }, i * 1000);
}
```

All we’re doing here is looping from 1 do 5 and setting a timeout to print the current number after a certain amount of time. Common sense says that this code will output 1, 2, 3, 4, 5, right?

To our surprise the above code logs the number 6 five consecutive times in the console. If you do this loop without the setTimeout you won’t be having any issues, because the log will be executed immediately. But apparently queueing up this operation in time is what causes the flaw.

We expect that every setTimeout call will receive it’s own copy of the i variable, but what happens is that it accesses it from it’s parent’s scope. And because they are queued the first log will happen 1 second after it was queued. When those 1000 milliseconds pass the loop has long finished and the i variable rests with the value of 6 assigned to it.

We understand the problem but how do we fix it? setTimeout will look for the i variable in the global scope, which leads to it not printing the number we want. We can manipulate that by wrapping the setTimeout in a function and passing the variable that we want to log in it. This way the setTimeout function will access it from it’s parent’s scope instead of the global one.

```javascript
for (var i = 1; i <= 5; i++) {
  (function(index) {
    setTimeout(function timer () {
      console.log(index);
    }, index * 1000);
  })(i)
}
```

We are using an IIFE (Immediately Invoked Function Expression) and pass the number to log to it. An IIFE is a function that we call immediately after defining it and are often used in situations such as this — when we want to create a scope. This way each function will be called with it’s own copy of the variable, meaning that when the setTimeout runs it will access the proper number. So using the example above we will get the result of 1, 2, 3, 4 and 5 that we are looking for.

Closing thoughts
This article captures the essence of closures but there is more to it to learn and more edge cases to ponder upon. If you want to dabble further into the depths of closure dark magic I strongly suggest Kyle Simpson’s book on Scope & Closures.