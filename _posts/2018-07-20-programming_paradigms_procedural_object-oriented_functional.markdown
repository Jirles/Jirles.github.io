---
layout: post
title:      "Programming Paradigms: Procedural/Object-Oriented/Functional"
date:       2018-07-20 16:58:40 -0400
permalink:  programming_paradigms_procedural_object-oriented_functional
---


I've run across a few different programming paradigms while going through Flatiron School's curriculum but haven't given too much thought about what sets them a part. This blog post is an attempt to finally do my due diligence and make a little more sense of the word soup that comes along with learning a new discipline.

### Procedural Programming

Procedural programming (PP) approaches programming from the dichotomy of data and procedures  (subroutines /functions) that perform computations. PP favors a top-down approach to problem-solving, and both data and procedures and treated as separate entities. A procedural programmer starts with a problem and then breaks that problem down in to ever smaller procedures until the problem is small enough to be solved. This process is called _functional decomposition_., 

Data is still passed between the procedures to run their computations, but the data is often mutated or changed as it winds its way through the procedure chain, i.e. there are side effects such as database calls or converting a datum's state This is an important distinction when compared with Function Programming (FP), discussed later. Another important distinction between PP and FP is that PP is an imperative paradigm, meaning that the programmer seeks to tell the computer exactly what s/he wants the machine to do.

>#### im·per·a·tive
>
>   /əmˈperədiv/
>   
>_adjective_
>
>giving an authoritative command; peremptory.
>
>synonyms:	**peremptory, commanding, imperious, authoritative, masterful, dictatorial, magisterial, assertive, firm, insistent**

Essentially, you, the programmer, are providing the computer with step-by-step instructions on how to complete a particular problem. There's a lot of hand-holding involved. Many lower-level languages are built around imperative approaches to programming. Some languages that generally take an imperative approach to programming are Java, C++, and C. In fact, imperative programming is a first step to higher level programming that prefer the declarative style.

### Object-Oriented Programming

Object-Oriented Programming (OOP) focuses on classes and objects, as opposed to procedures/functions and data. OOP combines the procedures (class and instance methods/functions) and data (object attributes) into one entity. Classes act like factories, instantiating or creating new objects, giving them specific properties and holding on to the blueprints (methods/functions) on the actions those objects can take.

By encapsulating procedures and data into objects, OOP allows programmers to do more with less (code), and protect the properties of the object from the vargarities of the outside world. In fact, _Encapsulation_ if one of the the three pillars of the OOP paradigm: Encapsulation, Inheritance, and Polymorphism. _Inheritance_ refers to the fact that classes can inherit functionality from other classes, meaning that related classes can share certain attributes and functions, for example, a `Dog` class can inherit properties from an `Animal`class. without writing everything twice. This keeps code DRY.


The last pillar, _Polymorphism_, refers to a pattern in object oriented programming in which classes have different functionality while sharing a common interface. It is tightly wound up in inheritance. A common example of polymorphism is a `Shape` class from which  `Rectangle` class and a `Triangle` class would inherit. The two specific shape classes would have their own properties that define a rectangle or a triangle, but since they inherit from the same parent `Shape` class they should be able to borrow methods from the parent class. Thus, a programmer could call the same method on each of these classes (use the same interface following the definiton above) and get a different result. A very simplified example:

```
class Shape
 def draw
 #some code that returns a drawn shape
 end 
end 

class Rectangle < Shape
 # properties specific to retangles
end 

class Triangle < Shape
 # properties specific to triangles
end 

rectangle = Rectangle.new(*args)
triangle = Triangle.new(*args)

rectangle.draw #=> will draw a rectangle
triangle.draw #=> will draw a triangle

```

Again, the goal is DRY code. Sharing methods across classes in OOP make applications more modular and extensible. Instead of messy conditional statements describing different courses of action, you create interchangeable objects that you select based on your needs.


### Functional Programming

Finally, Functional Programming or FP. FP is gaining in popularity among programmers, and emerging as THE new paradigm in programming if the hype is to be believed. Unfortunately, its emerging status means that there is really not a  single definiton of function programming. Though, there are a few core concepts that Functional Programming is meant to follow:

- [Pure functions](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-a-pure-function-d1c076bec976)
- [Function composition](https://medium.com/javascript-scene/master-the-javascript-interview-what-is-function-composition-20dfb109a1a0)
- Avoid shared state
- Avoid mutating state
- Avoid side effects


First, pure functions refer to functions that do not have any side effects, meaning they do not alter the data passed to them nor do they make any external calls, for example to a database, to complete their computation. For clarity, the following list includes some common side-effects:

- Modifying any external variable or object property (e.g., a global variable, or a variable in the parent function scope chain)
- Logging to the console
- Writing to the screen
- Writing to a file
- Writing to the network
- Triggering any external process
- Calling any other functions with side-effects

Moreover, pure functions, given some input, will always return the same result. You can think of a pure function as a self-contained piece of code with a pre-determined (deterministic) outcome.

The second tenet, function composition is simply the process of composing more complex functions from other, more basic functions. You can do this by passing a function to another function as an argument, for example. The result is purer functions and DRY code. A  example of a higher-order function is Javascript's `map()` function which can map over a number of datatypes because it is passed a function that appropriately handles the given data type.

Avoiding side effects and mutable (i.e. data that changes) are fairly self explanatory. The phrase "shared state", though, may need more defining. Shared state refers to any variable, object, or memory space that exists in a shared scope. An example of shared scope is how objects can be stored as properites of other objects in OOP. Functional programming avoids this shared state — instead relying on immutable (i.e. cannot be changed) data structures and pure functions to derive new data from existing data.

Finally, another principle associated with Functional Programming is that it is a declarative paradigm, as contrasted to the imperative Procedural Programming above. 

> #### de·clar·a·tive
> 
>/dəˈklerədiv/
>
>_adjective_
>
>grammer: (of a sentence or phrase) taking the form of a simple statement.
>
>computing: denoting high-level programming languages that can be used to solve problems without requiring the programmer to specify an exact procedure to be followed.

Normally, this distinction is summed up by saying that that imperative programming is telling the computer **what** to do (step-by-step) while declarative programming aims to tell the computer **how** to do it. Another way to put it is that imperative programming is concerned with the _control flow_ of a program, relying heavily on statements like `if`, `for`, `switch`, etc. On the other hand, declarative programing is concerned with the _flow of data_ and prefers expressions, code that evaluates to some value. In the declarative case, the control flow in declarative programming is abstracted away.  




