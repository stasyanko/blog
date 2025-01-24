+++
date = "2025-01-23T11:12:15+03:00"
draft = false
title = "MONNBLANC principles - the foundantion of OOP"
+++

# MONNBLANC principles for OOP

## Problems of modern OOP

We have SOLID principles, design patterns, encapsulation, inheritance, polymorphism, abstraction in modern OOP - which is great.

But before learning all of that we usually don't have an idea what objects are and what problems they solve.

Is OOP just another way to organize code more conviniently than procedural approach? Nope.

OOP is a different paradigm.

Besides, modern OOP is actually not what it was meant to be. 

Let's call it "mainstream OOP". 

There are lots of problems in that. 

The main of them are:

- getters/setters (it breaks encapsulation very hard)
- static methods
- mutable objects
- inheritance

Some developers even move away from OOP to functional programming as they think that OOP is not good enough as objects are mutable and unpredictable.

But who forbids you to design objects with immutability in mind?

First of all let's define ***what an object is.***

It is interesting, that many people who use OOP daily have never thought of what an object actually is.

There are lots of misconceptions about that.

So, the first definition is by ***Alan Kay*** who is considered to be a father of OOP.

> I thought of objects being like biological cells and/or individual computers on a network, only able to communicate with messages
So, the central idea of OOP is messaging.

The second definition is by ***Uncle Bob Martin:***

> “The difference between a data structure and an object is that data structures have visible data and no behavior. Objects have behavior and no visible data.”
> ~ <a href="https://twitter.com/unclebobmartin/status/1107740352516157441" rel="nofollow" target="_blank">Uncle Bob Martin</a>

***So let's conclude what objects are:***

- objects are similar to biological cells and/or individual computers
- objects have behavior
- objects have no visible data

> The main purpose of MONNBLANC principles is to be the foundational principles for OOP and formulate what objects are and how they interact with each other.

**Disclaimer**

*All examples are written in pseudo language that is understandable to most programmers who know Java, Python, PHP, Javascript and most modern OOP languages.*

*The principles are explained with practical examples.*

*Alan owns a pizzeria and he wants to make online ordering for this.*

*We will develop online ordering of pizza using MONNBLANC principles.*

## MONNBLANC principles are:

**M** - [messaging](#messaging)

**O** - [object immutability](#object-immutability)

**N** - [no getters and setters](#no-getters-and-setters)

**N** - [no static methods](#no-static-methods)

**B** - [black box object](#black-box-object)

**L** - [limited services](#limited-services)

**A** - [abstract dependencies](#abstract-dependencies)

**N** - [no null](#no-null)

**C** - [composition only](#composition-only)

## Messaging

> “I’m sorry that I long ago coined the term “objects” for this topic because it gets many people to focus on the lesser idea. The big idea is messaging.”
> ~ Alan Kay

Objects must be like small computers that talk to each other via messaging.

Let's start designing our system for ordering a pizza online.

To start with, we will make an Order class:

```
class Order {
    private final string clientFullName;
    private final string clientAddress;

    public constructor(
        string clientFullName,
        string clientAddress
    ) {
        this.clientFullName = clientFullName;
        this.clientAddress = clientAddress;
    }

    public function clientFullName(): string {
        return this.clientFullName;
    }

    public function clientAddress(): string {
        return this.clientAddress;
    }
}
```

For storing a new order let's create OrderStorage:

```
class OrderStorage {
    private final OrderRepositoryInterface orderRepository;

    public constructor(OrderRepositoryInterface orderRepository) {
        this.orderRepository = orderRepository;
    }

    public function store(Order order): boolean {
        return this.orderRepository.insert(order);
    }
}
```

To store a new Order we will write this code:

```
var order = new Order('Alan Kay', '909-1/2 E 49th St Los Angeles, California(CA), 90011');
var orderStorage = new OrderStorage(new OrderRepository());

orderStorage.store(order);
```

In terms of most languages (like Java, Javascript, PHP etc) sending a message to an object means calling a proper method which prodives some service to another object.

Calling store() method of OrderStorage is actually sending a message to this object to get a service of storing a new Order.

But you could say: "I already call methods of different objects. ***Why should I care about messaging?***"

Well, calling a method of an object is not always equal to messaging.
    
To be able to name a method call an act of messaging one principle must be kept for designing an object:

*"Every object should be a specialist only in its services and should know nothing of how things work under the hood in other objects"*

You will understand it better in the next sections of MONNBLANC principles.

So let's sum up:

- objects should communicate with each other by sending messages to each other
- in terms of modern OOP languages sending messages simply means calling a method of an object to get some service from it

## Object immutability

All objects must be immutable. Almost every real life object is immutable. 

Programmers model real life objects with code. 

The main idea of OOP is - build an object in its constructor and use it. 

We can not change the internal organization of any real life object. 

Once a car was produced in a car fcatory, we can not change its producer, engine, body, color while we drive it.

When an HR manager in your company changes your marital status in your personal card, he/she doesn't cross out text with your marital status, in fact an HR creates a new personal card with new data. 

*Let's get back to Alan's pizzeria.*

As you could have noticed, clientFullName and clientAddress properties of Order are marked as final:

```
private final string clientFullName;
private final string clientAddress;
```

It means that we can assign their values only in the constructor which makes them immutable.

To change their value we have to create a new instance of Order.

Why should objects be immutable?

The answer is simple: we can be sure that an object's internal state is not mutated (changed) by other objects only on condition that this object is designed as immutable object.

The Order looks like this:

```
class Order {
    private final string clientFullName;
    private final string clientAddress;

    public constructor(
        string clientFullName,
        string clientAddress
    ) {
        this.clientFullName = clientFullName;
        this.clientAddress = clientAddress;
    }

    public function  clientFullName(): string {
        return this.clientFullName;
    }

    public function clientAddress(): string {
        return this.clientAddress;
    }
}
```

But what if we designed this class like this?

```
class Order {
    private string clientFullName;
    private string clientAddress;

    public constructor(
        string clientFullName,
        string clientAddress
    ) {
        this.clientFullName = clientFullName;
        this.clientAddress = clientAddress;
    }

    public getClientFullName(): string {
        return this.clientFullName;
    }
    
    public setClientFullName(string clientFullName): Order {
        this.clientFullName = clientFullName;
        return this;
    }

    public getClientAddress(): string {
        return this.clientAddress;
    }
    
    public setClientAddress(string clientAddress): Order {
        this.clientAddress = clientAddress;
        return this;
    }
}
```

In this case we have a getters and a setter for every field and ```clientFullName``` and ```clientAddress``` may be changed easily by any object that **is no supposed to work with Order object**:

```
var order = new Order('Alan Kay', '909-1/2 E 49th St Los Angeles, California(CA), 90011');
// !!! a setter for clientAddress is used here
// !!! and we can not be sure that Order goes to OrderStorage 
// !!! with the right internal state
order.setClientAddress('98168 Carroll Parkways, Apt. 753, 91212-4157, South Penelope, New Mexico, United States');

var orderStorage = new OrderStorage(new OrderRepository());
orderStorage.store(order);
``` 

So let's sum up:

- mutable objects cause bugs that are hard to find
- objects must be designed as immutable to be sure their internal state is not mutated by other objects

The next section of MONNBLANC principles shows you why getters and setters is a severe OOP antipattern.

## No getters and setters

This section is united with [black box object](#black-box-object) section as it is related to the same topic - encapsulation. See [black box object](#black-box-object).

## No static methods

This section is united with [black box object](#black-box-object) section as it is related to the same topic - encapsulation. See [black box object](#black-box-object).

## Black box object

This section will explain three principles at once:

- N - no getters and setters
- N - no static methods
- B - black box object

It is for a reason - all of them concern ***encapsulation***.

Let's rewrite OrderStorage like this:

```
class OrderStorage {
    private OrderRepositoryInterface orderRepository;
    
    public function getOrderRepository(): OrderRepositoryInterface {
        return this.orderRepository;
    }
    
    public function setOrderRepository(OrderRepositoryInterface orderRepository): OrderStorage {
        this.orderRepository = orderRepository;
        return this.orderRepository;
    }
    
    public static function removeOrder(Order order): bool {
        // some code to remove an order from database
    }
    
    public function store(Order order): boolean {
        return this.orderRepository.insert(order);
    }
}
```

Well, maybe from the modern point of view this code is good enough.

*But it is not, it is terrible.*

First, there is a setter and a getter for ```orderRepository```.

We can replace it with anything in runtime.

This fact turns our object into a datastructure without behavior.

That's why, getters/setters approach is a hard antipattern, though it is widely used today. 

Getters/setters allow you to mutate an object's state without an object itself knowing anything about that.

Let's remember Uncle's Bob quote from above:

> “The difference between a data structure and an object is that data structures have visible data and no behavior. Objects have behavior and no visible data.”
> ~ <a href="https://twitter.com/unclebobmartin/status/1107740352516157441" rel="nofollow" target="_blank">Uncle Bob Martin</a>

If we conclude the quote above we can say that **an object is not a data structure**.

Every object is supposed to have some behavior. 

What are getters and setters for every property of an object?

Yeah, you are right - it is making our data visible to anyone!

So, let's get rid of getters/setters and pass ```OrderRepository``` to the constructor of ```OrderStorage```:

```
class OrderStorage {
    private OrderRepositoryInterface orderRepository;
    
    public constructor(OrderRepositoryInterface orderRepository) {
        this.orderRepository = orderRepository;
    }
    
    public static function removeOrder(Order order): bool {
        // some code to remove an order from database
    }
    
    public function store(Order order): boolean {
        return this.orderRepository.insert(order);
    }
}
```

Awesome! But there is one more problem: a static method ```removeOrder(Order order)```.

Why is it a problem?

Static methods break encapsulation - we can call static methods without creating an instance of ```OrderStorage```.

***Static methods break encapsulation***. Period.

Let's get rid of the static method and make nonstatic:

```
class OrderStorage {
    private OrderRepositoryInterface orderRepository;
    
    public constructor(OrderRepositoryInterface orderRepository) {
        this.orderRepository = orderRepository;
    }
    
    public function removeOrder(Order order): bool {
        // some code to remove an order from database
    }
    
    public function store(Order order): boolean {
        return this.orderRepository.insert(order);
    }
}
```

Good, we have two principles covered:

- No getters and setters
- No static methods

One more left - **Black box object**.

Black box object means that objects **must** hide their behavior and data from other objects.

In other words, it is encapsulation.

If you pass all dependencies in the constructor and stop using getters/setters and static methods, your objects are likely to become ***black boxes***!

If you order a pizza online, a pizzeria is a black box for you.

You don't care how and where a pizzeria buys tomatoes, how many cooks there are in their kitchen, what ovens they use.

You care about only one thing: tell them what pizza you want and get this pizza in 30 minutes near your door.

So the same thing should happen when you design your objects: every object must do only things it is supposed to do and other objects should know nothing what this object does under the hood.

So let's sum up:

- getters/setters is a severe atipattern for OOP
- static methods are an atipattern for OOP
- every object must be a black box for other objects

The next section of MONNBLANC principles shows you why every object should provide limited set of services.

P.S.: of course in real world apps we do use static methods, getters and setters and sometimes they work well enough. But it's better to use avoid them if possible.

## Limited services

Every object should provide a limited set of services according to its responsibility.

Let's introduce an interface for ```OrderStorage``` and name it ```OrderStorageInterface```.

```
interface OrderStorageInterface {
    public function removeOrder(Order order): bool;
    public function store(Order order): boolean;
    public function update(Order order): boolean;
}
```

This principle is very simple - ```OrderStorage``` implements ```OrderStorageInterface``` and OrderStorage provides a limited set of services:

- removing an order
- storing an order
- updating an order

If we add a method ```sendEmailToAdmin(string $email): bool``` it will break this principle as ```OrderStorage``` should do what it is supposed to do: to manage orders in a storage.

Let me show a tip for this principle: ***when you design objects, imagine you are a manager in a company's department.***

There are 10 workers and you need to assign each of them what they will do.

The same thing applies to objects: assign each of them what they will do and never allow any of them do what they are not supposed to.

So let's sum up:

- every object should provide a limited set of services according to its responsibility

The next section of MONNBLANC principles claims: every object should depend on abstractions only.

## Abstract dependencies

Every object should depend on abstractions instead of concrete implementations.

This principle is pretty straightforward: if your objects depend on abstractions it makes them very portable and you can use them in other systems easily.

Abstractions are interfaces in terms of modern languages.

```OrderStorage``` depends on ```OrderRepositoryInterface``` which makes ```OrderStorage``` very portable as you can replace ```OrderRepositoryInterface``` with any implementation:

```
class OrderStorage {
    private OrderRepositoryInterface orderRepository;
    
    public constructor(OrderRepositoryInterface orderRepository) {
        this.orderRepository = orderRepository;
    }
    
    public function removeOrder(Order order): bool {
        // some code to remove an order from database
    }
    
    public function store(Order order): boolean {
        return this.orderRepository.insert(order);
    }
}
```

The implementation of ```OrderRepositoryInterface``` may store data in MySQL, file system, MongoDB or any other storage.

So let's sum up:

- every object should depend on abstractions only (interfaces)

The next section of MONNBLANC principles claims: ```null``` can not be used in OOP systems.

## No null

> “I call it my billion-dollar mistake…At that time, I was designing the first comprehensive type system for references in an object-oriented language. My goal was to ensure that all use of references should be absolutely safe, with checking performed automatically by the compiler.
But I couldn’t resist the temptation to put in a null reference, simply because it was so easy to implement. This has led to innumerable errors, vulnerabilities, and system crashes, which have probably caused a billion dollars of pain and damage in the last forty years.”
> ~ Tony Hoare, inventor of ALGOL W.

Tony Hoare is considered to be the creator of ```null``` and he calls it a ***billion-dollar mistake***.

This principle is pretty simple too: never use null in OOP systems.

What problems can ```null``` cause?

Let's use ```null``` in ```Order```:

```
class Order {
    private final string clientFullName;
    private final string clientAddress;

    public constructor(
        string clientFullName,
        // clientAddress can be eithe null or string
        ?string clientAddress = null
    ) {
        this.clientFullName = clientFullName;
        this.clientAddress = clientAddress;
    }

    public function clientFullName(): string {
        return this.clientFullName;
    }

    public function clientAddress(): string {
        return this.clientAddress;
    }
}
```

If we set ```clientAddress``` to ```null``` it can mean a few things:

- ```clientAddress``` is empty
- a client does not have an address
- we do not require from clients  to enter their address

So, the first problem of ```null``` - ***it does not model business logic properly***.

The second problem - you have to check nullable properties of an object as it can cause ```NullPointerException```.

What is the replacement of ```null```?

***NullObject pattern*** to the rescue!

For the ```clientAddress``` property let's create ```ClientAddressInterface```:

```
interface ClientAddressInterface {
    public function value(): string;
    public function isNull(): boolean;
}
```
Now we change the type of ```clientAddress``` to ```ClientAddressInterface```:

```
class Order {
    private final string clientFullName;
    private final ClientAddressInterface clientAddress;

    public constructor(
        string clientFullName,
        ClientAddressInterface clientAddress
    ) {
        this.clientFullName = clientFullName;
        this.clientAddress = clientAddress;
    }

    public function clientFullName(): string {
        return this.clientFullName;
    }

    public function clientAddress(): ClientAddressInterface {
        return this.clientAddress;
    }
}
```
So in the case of empty address we can pass this class to ```Order```:

```
class EmptyClientAddress implements ClientAddressInterface {
    public function value(): string {
        return '';
    }

    public function isNull(): boolean {
        return true;
    }
}
```

Now we can easily check if ```clientAddress``` is null (of empty) by calling the ```isNull()``` method and we will never get ```NullPointerException```!

So let's sum up:

- never use ```null``` in OOP systems
- for nullable types use NullObject pattern

The next section of MONNBLANC principles claims: use composition instead of inheritance.

## Composition first

Use composition mainly when building OOP systems, try to avoid inheritance.

Inheritance is actually an anti-OOP concept and there are only two truly OOP concepts:

- encapsulation
- polymorphism

Imagine ```OrderStorage``` extends (inherits from) ```AbstractStorage```.

```AbstractStorage``` extends (inherits from) ```DatabaseStorage```.

```DatabaseStorage``` extends (inherits from) ```DatabaseConnector```.

Seems to be fine from the point of view of '(modern) mainstream OOP' design.

Now imagine ***three more classes*** extend (inherit from) ```AbstractStorage```.

And now we decide to change the behavior in the middle of this chain in ```DatabaseConnector``` class.

It will influence the behavior of all four classes.

But are you sure you want to change the behavior of every class?

So, it is much better to use dependency injection (which is a compositional way of designing objects) to allow your objects to get access to other objects' functionality.

So let's sum up:

- use composition only to design objects
- inheritance is not an OOP concept

***List of used resources:***

- <a href="https://octoperf.com/blog/2016/04/07/why-objects-must-be-immutable" rel="nofollow">Why objects must be Immutable [By YJérôme Loisel]</a>