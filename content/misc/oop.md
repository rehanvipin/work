---
weight: 6
title: "OOP"
# bookFlatSection: true
# bookToc: false
---

# Object Oriented Programming

Objects are things that hold information and exhibit behaviour. One way of classifying the different kinds of objects would be: things from the problem domain and things that are just there to make the implementation work. Not everything is an object in every context, some might just be attributes and some might just be operations. According to OO:

- Objects encapsulate behavior and state (i.e., it binds them to the object)
- Object abstract implementation and only show public interfaces. This helps with reusability.
- They have identity, i.e., a way of addressing each object uniquely. This isn't based on state. (in some languages it might just be the address of the object in memory)
- They should define ONE thing in the problem domain. Most of the methods should make use of most of the data most of the time. (if not, it means that some methods and data could be moved to another object)
- Objects could have polymorphic functions (same interface but different implementations). They should do similar things semantically.

A class is a blueprint to define objects. It's possible to have objects without classes (e.g., JB).

An advantage of OOP is that it lets actual things from the problem domain be mapped into the program. It's easier to understand.

## Relationships between objects:

The one basic relationship between them is the "uses" one, where one object sends a message to another (this is usually implemented by calling a public interface method on the receiver). Some refinements of this are:

- Association. Object just sends message to another one. They are independent of each other.
- Aggregation. Object can be said to be a part of another one. It may be part of multiple others, it may exist longer than the things it is a part of. It needn't be a part of other objects.
- Composition. Object is part of another one and it must be, it doesn't exist independently. If A is part of B and B is part of C, A is part of C. an object can know what it contains but it shouldn't know what is the parent container for it. Users only depend on interfaces of composite.

Custody means the "thing" that's responsible for creating and deleting objects. In asso & aggr it is some external system. In compo it is the container object.

Fun fact, there could be objects that are formed just to facilitate association relationships. Note that aggr and compo are types of "part-of" relationship.

Lifetimes are locked in compo while not is asso. Asso is more flexible than compo.

## Relationships between classes:

Different from object relations. Those are at run time, these are at compile time. This is done via inheritance in "kind-of" or "is-a" types of relationships. Inheritance can be viewed a tree, going up the we see classes that are generalized and while going down we see more specialization. Types:

- Is-a: the child class contains all members of the parent and may add its own. It can be used anywhere the parent class is used.
- Kind-of: ...?

It's better practice to classify objects based on their behavior rather than their attributes. There's also another kind of relationship: "has-a".

Proper inheritance requires that the derived class have at least the base class's interface and the interface should require no more info than the base class. This means that it can be used anywhere the base is. If the interfaces also do the same thing then those classes have a "kind-of" relationship. IMPORTANT. That's why a stack isn't a kind-of a list. The list has different behavior.

Interface inheritance does not occur during private inheritance.

In multiple inheritance, objects of the derived are substitutable for objects of either base class.

Makes are special classes (not meant to be instantiated, done by means of abstract class) that are meant to be used in multiple inheritance to add extra functionality to a class. e.g. car + the sun-roof main may be inherited from to make a luxury car.


In a struct, everything is public by default ( can be made private too) while in a class its all private by default. Objects should do one thing well and have a minimal interface so they can have a coherent identity. Given that structs and classes are so similar, a guideline could be that use structs when no member funcs required else class.

Member functions should be declared within the class (along with default params) and may be defined outside by using the Klass:Func[ ] syntax (i.e. scoping operator). If its defined inside it automatically becomes an inline func.

Although member funcs can access object attributes just by their name, here are two other ways:

- Klass::attrName (only inside the func!)
- This->attrName

The `this` pointer is passed invisibly to the func. A func call like this: objId.func(a, 10) actually becomes: Klass:func(&objId, a, 10) where the first is `this` but all this is invisible to the programmer.

Fun fact about scope: to access stuff from a global scope do it like so: `::varname`

Non member funcs can be declared as "friend funName" inside the class which makes it a friend function. It however doesn't get a `this` pointer, it can have any params it wants. It however can access the private attributes of any object that's passed to it (pointer wise too). Could also declare another class as a friend in which case all methods of that class become friends. Defining a friend function inside a class doesn't make it a member function, it just defines a function in scope of that class.

Inline functions (and now inline vars from 17) have one special purpose: they can defined identically in multiple translation units that will later be linked (this wouldve caused an CDR i.e., one-def-rule violation otherwise). Useful for header files.

Internal vs external linkage - internal linkage means a symbol will only be available inside the TU. Const vars are like this and so are class definitions. That's why its okay to have them in header files that are used in multiple TUs that will be linked.

The compiler by default provides 3 constructors:

- Default (it just inits all elements to their default values, takes no args). This one vanishes when a user define a constructor (of any kind).
- Copy
- Move

If you define a var without a constructor call its elements get undefined values. e.g. int a doesn't call while int a[] does. Single arg constructors are fun, they can show up while typecasting. Klass x = "lol" calls the single arg constructor. Klass array_idk[] = {1, 3, 4, 9}; calls sing-arg-con for each one. For multiple args use {} e.g. Klass y = {3, "gg"};

Note that klass f = {} and klass f{} and klass f = klass{} are equivalent. To prevent stuff from being converted to your class without you explicitly creating objects, add `explicit` before the single-arg-con declaration.

Why declare a method to be a const one? Only those methods can be called by a const object! It receives a `const Klass "this".

An array can be created like so: std::array<type, length=""> name; if it has {} after name it means that each element is int with the constructor else they are uninit by default.</type,>

Initializing a composite object. How to initialize the objects it contains and make sure their constructors are called with the right args? Use the initialization list. It's also the only way to init constants and references. e.g. Klass(param list) :

innerobj(1, "idk"), innerobj(2, 4, 9) {} It's also possible to init members right where they are declared (like innerobj) but

## Types of polymorphism:

- Coercion (e.g. adding int and double)
- Inclusion (i.e., inheritance)
- Overloading
- Parametric (generic classes & tunes)

## Design patterns:

A pattern is a solution to a recurring problem, in a certain CONTEXT. In programming, many patterns have been implemented in the language and frameworks so that they are invisible to programmers and aren't really patterns anymore. The idea of a pattern is to give us a guide on how to solve a problem that we need to do ourselves and isn't automatically done by a language / tool. They are language independent. Idioms on the other hand are not e.g. short circuit evaluation in C type languages. For-else in python.

Design heuristics are different from patterns. They are qualitative guidelines that help designers make choices.

Designing good OO software is hard, that's why use patterns which are an accumulation of a lot of people's work and are proven solutions. (note that this could also be seen as a drawback of OO and band-aid being applied over it)

Relication (implementation of a pattern), there are multiple ways to implement it. Many patterns may look the same in diagrams, what differentiates them is the problem they are solving aka intent. UML can be used to show implementation of pattern.

There are also architectural patterns (higher level than the usual patterns and can make use of them) which also consider: non-functional requirements. E.g. pipe and filter pattern of unix

Anti-patterns are patterns that look like good solutions at first sight but have drawbacks while there is a proper solution available for the problem. They usually arise from ignorance / pressure.