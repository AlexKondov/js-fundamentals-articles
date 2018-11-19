# The "this" Keyword

Another topic that has been the reason for a lot of discussion is the this keyword. More specifically — it’s unexpected behavior at times. About an year ago, a colleague of mine asked me to have a look at her JS code because she was having strange bugs. As a Java developer just starting to play around with JavaScript she had done the mistake of thinking she could understand the language before actually studying it — like many people do.

How many other developers going into JS have problems with this, I do not know but I expect the number to be quite high. In fact this is probably the easiest concept of JavaScript that you have to understand. If I have to explain it with my own words I would probably spit out something in the lines of:

The this keyword does not refer to the function in which it is used or it’s scope. It refers to the object on which a function is being executed and depends entirely on the call-site of the function.
Let’s create a really basic example to illustrate what is going on:

```javascript
function testThisKeyword() {
  console.log(this); // [object Window]
}
testThisKeyword();
```

Let me give you another one which will question your understanding on JavaScript even more:

```javascript
function testThisKeyword() {
  console.log(this.name); // Alex
}
var name = "Alex";
testThisKeyword();
```

What? How on earth does that input the name variable? It’s not even created in the function’s scope! This is one of the reasons that developers get really sad. It just doesn’t work the way you expect it to. Contrary to popular belief there is no black magic going on underneath. What is happening here is that the name variable is declared in the global object, therefore it can be accessed as a property on it. If we look at the call-site of the function we will see that we are not implicitly calling it on an object so this will reference the global object.

The this keyword gets automatically defined in the scope of every function and it’s binding happens in three ways — default, implicit and explicit. The example with the standalone function invocation has already shown you how the default binding works. Something to have in mind is that that is the fallback binding if another is not specified. Let’s get into more details about the implicit and explicit binding.

## Implicit Binding

```javascript
const person = {
  name: "Alex",
  greet() {
    console.log("Hey my name is " + this.name);
  }
};
person.greet(); // Hey my name is Alex
```

In order to understand what value this will hold you need to look at where it is called. Not the function in which it is used but the call-site. In this example the owner or containing object is the person object. A trick that I like to use is to look at the left side of the function call. The object that is standing before the dot is what the this keyword will be bound to.

Something to have in mind is that this binding can get “lost” if you start passing functions as reference. Even if you are referencing a function that is a property on an object, if you call it separately it won’t have the binding you expect and it will use the default binding. This is why the only thing that matters for this is the call-site of the function. A better and more in-depth example of this can be read in Kyle Simpson’s book on this and object prototypes, so I won’t go into details here.

## Explicit Binding

In the example of implicit binding we have to use an owner object in order to achieve the binding that we want. But what if we don’t want to have a reference to the function on the object or we are unable to mutate it? There is a solution to explicitly say to a function what object it should use for this — using functions such as call, apply and bind. Using explicit binding is so common that those two functions are available from the function prototype, however there are differences between them.

The call function allows you to pass in the object to which the this keyword should be bound to. It also allows you to pass arguments for the function as additional parameters.

```javascript
function greet() {
    console.log( this.name );
}

var person = {
    name: 'Alex'
};

greet.call( person, arg1, arg2, arg3, ... ); // Alex
```

The apply function is similar to call with the difference that the function arguments are passed as an array or an array-like object.

```javascript
function greet() {
  console.log(this.name);
}

var person = {
  name: "Alex"
};

greet.apply(person, [args]); // Alex
```

The bind function is a little bit different than the first two. It creates a new function that will call the original one with this bound to whatever was passed in.

```javascript
function greet() {
  console.log(this.name);
}

var person = {
  name: "Alex"
};

var greetPerson = greet.bind(person);
greetPerson(); // Alex
```

## Conclusion

A little bit earlier in the article I told you that those were the main bindings for the this keyword. Well I may have lied to you, because there is also a binding that is happening when you use the new keyword. However, I’m planning on writing a small article on that so I’ll skip the explanation for now.
