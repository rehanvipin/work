---
title: "Scala"
weight: 7
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---

# Scala - Hipster Java!

Scala is compiled down to JVM bytecode and after that will work seamlessly with all Java code.
Main language features: concise syntax & small language (extra functionality through libs)

https://docs.scalaclanz.org/tour/ basics.html

In Scala it is possible to have an object defined in a file e.g.
object NewStuff {
def main(args: Array(String)) : Unit = ( println("hello!") ) // Unit means void
}

The filename of source files isn't really important to Scala.

This object becomes an instance of a singleton class that scala creates in the background.

To compile scala source files into java class files, use `scalac srcFile.scala`. You can also run those class files with `scala ClassNameOfStuff.class`. It also has a REPL for fun. The scala source files can also be run like so: `./srcFile.scala`
PROVIDED you have added the required shebang (not as simple as it seems)

Basic rules of scala:

- Everything is an expression
- Everything is an object
- Every operation is a method call

Scala has 'values' and 'variables' which are named immutable and mutable pieces of storage respectively and can be defined with 'val' and 'var'. It is possible to use Java types in scala. It's naming rules are very liberal.

Methods are defined with 'def' like so def funcName(a: Int, b: Int) = a *b ; or with = {} for complex ones. If a func doesn't have any params, no {} are required during call! Also, scala perfects that you not write `return` explicitly when the last expression in the function body is what you want to return.

Instead of switch, scala has `match` which is more powerful. Both while and for expressions have a value of the type `Unit`.

Defining class in scala is super easy: `class x` done. It has no fields & methods tho. The stuff next to the classnames actually represents constructor params too. Methods will have to be defined inside the

Val and def seems similar, the major difference is that the evaluation of the expression for val is done once while for def it is done every time someone tries to use the method. This can be really handy while defining getters (e.g. def x = x where x is the private field). It's possible to shift some initialization from the time of construction of the object to the time of the usage of the field. This can be done with `lazy val xyz = someExpensiveOb`

Fun fact: `x method y` becomes `x method(y)` and `x method: y` becomes `ymethod(x)`

Scala doesn't have static (yay!), all members are members of the objects of the class. By default, members have public visibility.

Fun fact: scala requires that all members be able to be defined with `val` or `def` and the client code shouldn't know the difference.

Packages in scala are exactly like namespaces in C++ (other than the ::, which becomes .)

Traits! The mixins of scala

A trait is like a java interface but it can have concrete methods, fields and type. It cannot have a parametrized constructor. A trait can inherit from other traits. A class can extend multiple traits using `with traitName`. Scala uses [7] for type parameters rather than <1>

Scala has this thing called 'case classes', these are classes whose objects are immutable and they are compared by value, not by reference as other objects.

Functional programming:

Minimize use of mutable data. Make use of higher-order functions.

It has function literals (aka anonymous functions) e.g. (aint, b:int) => arb:

Scala closures close around variables, not just their values.

In scala, functions and methods are not the same. A method is composed in an object while a function is just an instance of a type that extends `Function`. A method may be implicitly converted to a function wherever required.

There's companion objects in scala - objects which have the same name as a class. They have access to private members of the class. They're meant to provide methods that aren't specific to an instance of that class but are related to the class regardless.


## Collections:

A tuple isn't really a collection but looks like one. It's just a fixed aggregate of values that could be of different types. You can have a tuple of 22 elements at max. The tuple types are `Tuple1`, `Tuple2`, `Tuple3`, and so on. You can create one like so: `val tupz = ("Hi", 1.23, 4)` // or `Tuple3[String, Double, Int]("Hi", 1.23, 4)` if being exact. The values in a tuple can be accessed like so: `tupz._1` and so on.

In the scala collections hierarchy there is `Traversable` (where iteration is managed internally by the collection and the client is just provided the 'foreach' method) and `Iterable` (where iteration can be done by the client via use of `.next`).

All collections are generics in scala. Some of them are: `Array` (to access element do `arr(index)`), `Seq` (similar to the List type in Java. It has two subtypes `LinearSeq` and `IndexedSeq`), sets and maps (for maps prefer to use `.get` instead of `()` as get returns an `Option[T]` using which you can do checks).

Since all collections derive from traversable, they all have a 'foreach' method. To refer to the current element that is being processed, no need to do stuff like `i => i % 2 == 1`, you can just do `_ % 2 == 1`. The `_` is a special var. Other functional programming methods available on collections are: `map`, `foldLeft`, `foldRight` (similar to reduce), `filter`, `partition` (based on a predicate), `zip`, `flatten` (which doesn't take in a function), `flatMap` (which does take a callback func).

Null values are represented using 'Nil'. You can have a collection having multiple types by giving the type-parameter to the collection as `Any`.

Like python there's a for comprehension in scala. e.g., `for(a <- 1 to 10) yield a*2`.

## Pattern matching:

Not regex, this one is about the 'match' expression. It's much more powerful than switch. You can have guards, and even perform matching based on the type of the expression in question. You can also match on structured values (e.g. tuple) & case classes.

In scala there is no concept of a checked exception. Pattern matching is frequently made use of while handling exceptions.

In scala, the `apply()` method is the overload for performing a call on an object, i.e., if the class has a apply method you can do `objkt()`. The `unapply()` method if defined tells how to destructure the object into individual types (you return a tuple of whatever components you want from the object).

Like the java Option, the scala `Option[T]` represents nullable stuff. You can set it to be `Some(wtvObjkt)` or `None`. The value (aka true objkt, if any) can be gotten with `.get`. An option can be used with match, the cases being `case Some(s) => ...` and `case None => ...`. Since it's a collection, you can use map, flatmap etc.

## Generics:

Variance -> invariant means if T0 & T1 are not related, a container of T0 & a container of T1 are invariant. If a container is covariant it means if T1 is a subtype of T0 then container of T1 is a subtype of a container of T0. Contravariant means the same thing but with supertypes. Bivariant means both covariant and contravariant.

You can make a container covariant with `+` like so: `Array[+T]` and contravariant with `-` like so: `Array[-T]`.

While defining a generic container you can give a type-bound like so: `class Bag [A <: Fruit]` where A must be a subtype of a fruit. And `[A >: Fruit]` means A should be a supertype of fruit.

Methods themselves could also be covariant / contravariant.

## Functional Programming
Functional programming - treats computation as evaluation of mathematical functions only, avoids state & mutable data. Why is it popular? Declarative style of programming means cleaner code and also means an optimized implementation can be chosen at runtime for the steps described instead of you writing implementation and trying to optimize. It also makes parallel programming easier because of use of immutable state. The restrictions might cause it to be memory & time intensive though.

What makes anything "FP-style" programming? Use of higher order functions, recursion, immutable state, lazy evaluation, and pure functions among other things.

How do closures work in Scala? A table of references to non-local variables is kept so that the GC doesn't destroy those variables while the closure function can still be used.

Since FP believes in immutable state, isn't it expensive to make a copy every time you want to change the value of something? Well, no. It makes use of copy-on-write. This also means no iteration, thus recursion is heavily used. In recursion, there is often tail call optimization to minimize use of stack frames.

You can do really lazy evaluation in langs like scala. e.g., if you have a func(someLazyStuff) { } // syntax isn't proper And you do func(someExpr), the expression won't be evaluated till the point someLazyStuff is actually used in the function (and never if it's never used). Plus, if someLazyStuff is used multiple times, the expression will be evaluated for each use.

Scala FP:

Return type must be specified for recursive functions. To perform TCO & inform if it can't, annotate a function with @tailrec. Note that the JVM doesn't have any capability to do TCO & the scala compiler will have to do it (how?).

Lazy evaluation for function arguments can be done using pass by name in scala. Syntax: def func(stuff: => TheTypeOfStuff) = { }.

Scala has some cool support for currying. You can define a function that can be curried like so: def huh(a: Int){b: Int}(c: Int) = a+b*c;

Then use it like so:

Val result = huh(1)(2)(3)

Val need2more = huh(1) _ // this is called partial function application

Val need1more = huh(1)(2) _

Val res1 = need1more(3)

Val res2 = need2more(2)(3)

Also, note that, for single argument function calls, the {} could be replaced with {}. Making this possible: val res = huh(3)(4)(5);

There are functions which aren't defined for all inputs, e.g., what about sqrt(-7) ? Well, for functions which take in 1 arg and return 1 value, there's a special type called PartialFunction which lets you define a func that's not defined for all values of the input parameter. It has two extra methods: isDefinedAt (returns boolean) and orElse (lets you specify another function (should be a partial func) which will be executed if this partial func isn't defined for input val). How to create one? Use `case`, `map`, or even derive from PartialFunction class.

## Exception handlind with FP:

Normal try-catch blocks are bleh. In scala they are expressions (duh), and the type is the LCA of the types of the type of the try expr and the type of the catch expr. There are better types to deal with exception kinda stuff:

- Option[T] - could have a value of Some[T] or None
- Either[A, B] - could have a vlue of Left[A] or Right[B] (can use left(exprOfTypeA) & right(exprOfTypeB) to set them). Convention is to set the exception type as A and the expected value as B.
- Try[T] - could have a value of either Success[T] (which would contain the value) or Failure[T] (which would contain the exception object). It has map, flatmap defined on it (hence it is a monad)

Implicit classes allow you to define multiple implicit methods at once. Implicit params search in the same scope for variables of the same type (what even).

## Futures

Futures:

Placeholder representing a value that will be available in the future. How to do it in scala?:

Var ft = Future (/* some task */)

Ft.onComplete(tryTypeObject => match tryTypeObject { case Success(val) => do whatever; case Failure(exp) => do whatever; })

Future[T] is a monad like Try[T]. Not just callbacks, it's also possible to block a thread till a future completes with Await.result(future, duration) but that's not advisable since the thread is waiting doing nothing. Scala also has the Promise[T] which represent a variable that can be written to (once) in the future, a promise can be watched by multiple futures.

In the olden days, threading was seen as a lightweight alternative to multiprocessing since they required far fewer resources, this is still true but the gap has reduced as threads now get a significant stack space per thread. It's difficult to reason about and maintain data stability in threaded programs and even harder to debug them especially when there are race conditions.

A newer model that's gaining popularity is the one where you have "actors" doing work where each actor is a self-contained unit containing state, behaviour, and means to communicate with other actors. They shouldn't use mutable shared data, should communicate via messages, and do work in a reactive manner. A library to do this is "Akka" which can be used in Java & Scala.

In cff you can use monitor to synchronize access to objects by marking critical sections. Only reference types can be synchronized this way. The lock region makes use of monitor and is preferred to direct use of Monitor. e.g., lock(objktName) (/* do wtv */) inside that block, it's also possible to do Monitor.Wait, Monitor.PulseAll and so on.
