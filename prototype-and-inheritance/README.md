# Prototype and Inheritance

The concept of inheritance in JavaScript is a broad topic. Object oriented concepts in general are a cause for deep discussions in the community. With ES6 we can use classes and implement inheritance hierarchies much similar to what we can do in other languages. By using TypeScript we can take this a step further and have JavaScript work like a statically typed language much similar to C#.

It's important to note that while those features make development a whole lot easier they are only abstractions in front of other features of the language. In this article we will examine the _prototype_ and its inner workings.

## Prototype

Objects in JavaScript have a property called _prototype_. This property serves as a reference to another object

Almost all objects in JavaScript have the prototype property. By using it and more specifically the prototype chain we can mimic inheritance. The prototype is a reference to another object and it is used whenever JS can’t find the property you’re looking for on the current object.

Simply put, whenever you call a property on an object and it doesn’t exist, JavaScript will go to the prototype object and look for it there. If it finds it it will use it, if not it will go to that object’s property and look there. This can bubble up all the way to Object.prototype before returning undefined. This is the essence of the prototype chain and the behavior that sits behind JavaScript’s inheritance.

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.sleep = function() {
  console.log(this.name + ": Zzz...");
};

function Dog(name) {
  this.name = name;
}

// Create a reference for the prototype
Dog.prototype = Object.create(new Animal());

Dog.prototype.makeSound = function() {
  console.log(this.name + ": Woof woof!");
};

var dog = new Dog("Lassie");
dog.makeSound(); // Lassie: Woof woof!
dog.sleep(); // Lassie: Zzz...
dog.missing(); // Throws Error
```

The above code can be overwhelming when you first encounter prototypes, let’s break it down. We’ll start from line 20 — with the new keyword we create a new object using the Dog constructor function on line 9. That gives us an object with a name property and a makeSound function bound to it’s prototype. When we call makeSound it is executed in the context of the current object (dog) and we get the correct output.

When we call sleep() it obviously doesn’t exist on Dog, so it goes up the prototype chain to Animal. It finds it there and calls it. On the last line we call the missing() function which is not defined anywhere. It will go all the way up the prototype chain to Object.prototype and since it won’t find it there it will throw an error.

As you see we haven’t used the word class anywhere, we haven’t defined a class that extends the base one or something like that. This is more of a delegation than inheritance. Every object has this prototype property which points to another object which should be delegated responsibility in case the property we’re looking for is not found on the current one. There’s nothing fancy about that, the object is just delegating responsibilities to it’s superior whenever it can’t handle the task.

## Shadowing

If we look through the prism of inheritance once again we know that we often need to override properties and methods. In prototypical inheritance this is called shadowing.

```javascript
function Animal(name) {
  this.name = name;
}

Animal.prototype.sleep = function() {
  console.log(this.name + ": Zzz...");
};

function Dog(name) {
  this.name = name;
}

// Create a reference for the prototype
Dog.prototype = Object.create(new Animal());

Dog.prototype.makeSound = function() {
  console.log(this.name + ": Woof woof!");
};

Dog.prototype.sleep = function() {
  console.log(this.name + ": Overriding Zzzz....");
};

var dog = new Dog("Lassie");
dog.makeSound(); // Lassie: Woof woof!
dog.sleep(); // Lassie: Overriding Zzzz....
```

What we’re doing here is creating a property with the same name on Dog’s prototype, so when we call it it will find it there and stop the prototype chain bubbling. As you can see, we’re not using the override keyword anywhere, we’re just declaring the property on the Dog object so JavaScript won’t start looking for it in the prototype chain.

## Problems with Inheritance

If you’ve read my previous articles with the same label, here is the point in which I tell you that JavaScript always has it’s specifics and not understanding them correctly can give you a big headache. At some point you may have to use shadowing on a property, but then inside of it call the “parent” function with the same name. While in most other languages you can just use super(), here things are a bit trickier. If you use this approach and call this.sleep() inside of the shadowed function, you will end up calling the same function and the result will be a recursion. Therefore we must think of another way to solve the problem.

```javascript
Dog.prototype.sleep = function() {
  Animal.prototype.sleep();
};
```

This sounds like a good approach at first and it almost is, but if you execute it you will see that you will not get the result you expect because the contextual binding is not correct.

```javascript
Dog.prototype.sleep = function() {
  Animal.prototype.sleep.call(this);
};
```

By executing it like this however we call the function on Animal using the contextual binding of the current function. In other words we call Animal’s sleep function using the current Dog’s data, I can’t explain it much simpler than that.

## The concept of Inheritance

For me, one of the hardest things to wrap my head around was the prototype keyword meaning. I was trying to make some mental connection between the word and the process of inheritance until I realized that they really have no connection. What we’re doing is just abusing the prototype behavior delegation in order to mimic inheritance while in fact we don’t have it. In order to give you another example of how our conception of class based hierarchies is different than JS prototypes — I present you the constructor property.

This is a property on Object.prototype and it returns the constructor function that created the object that we’re calling this on. Not the name of the function but the function itself. What do you think would happen if we log into the console the constructor property of the dog variable? You probably expect it to return the Dog function, but you will be surprised.

```javascript
console.log(dog.constructor); // [Function: Animal]
```

Wait what? This is returning the Animal function because it is attached to the Dog property upon object creation. This once again shows that those methods and properties weren’t implemented with OO design in mind.

## ES6 Classes

As of ES6 you can say that the inheritance hierarchy problems in JavaScript is solved. We got the class keyword together with all other goodies that come with it. We can now define classes, extend them, use constructors and the super keyword to access parent methods without the ugliness of the code above.

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }

  sleep() {
    console.log(`${this.name}: Zzz...`);
  }
}

class Dog extends Animal {
  constructor(name) {
    super(name);
  }

  bark() {
    console.log(`${this.name}: Woof woof!`);
  }
}

dog = new Dog("Lassie");
dog.bark(); // Lassie: Woof woof!
dog.sleep(); // Lassie: Zzz...
```

This looks a lot better if you’re already familiar with classes. It removed the unnecessary repetition of the prototype keyword, the use of Object.create() and the dark magic that we had to do in order to call a “parent” function. You also have the proper syntax and it’s much easier to understand what is going on. The ES6 code and the prototype code above are more or less doing the exact same thing, but the one of the new standard is times easier to understand.

Now something that you need to bear in mind is that this syntax is (mostly) just a beautified version of the prototype. It really simplifies the development process by making things easier for people with background in other languages to understand. But something that needs to be considered is that because of that same reason — ES6 classes being syntactic sugar over prototype — there are still some issues.

Because the prototype approach is more like delegation rather than real inheritance, “parent” objects are passed as a reference. In other languages, upon creating an object, you get a copy of it’s functionality, including the parent classes. Therefore in JS, a child object can access “parent” functions even if they are created after the child object. This is due to the fact that prototype is just a reference, not a concrete object. It’s a hotline that objects use to find a way out when they are missing a particular property.

## Conclusion

Until the introduction of “real” classes in ES6, the whole concept of inheritance was messed up and (usually) taught in a wrong manner. Prototypes are more of a delegation tool and they don’t really behave like classes. Now with the modern approach we can pretty much create normal class hierarchies and use understandable syntax for them, but be wary of possible problems that may occur. This syntax is only a beautiful facade for what’s going on inside.
