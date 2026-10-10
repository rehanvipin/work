---
title: "Setup"
weight: 1
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---
# Introduction & Dev Setup

## What is the Java platform?
* The Java compiler, which defines what the Java language is and converts it to bytecode.
Also a bunch of other tools which can help in development e.g., tools to inspect / decompile files.
* The Java Virtual Machine which executes the bytecode on the computer.
This along with the classes required to run the program (i.e., standard libraries) is called the Java Runtime Environment.

These two together make up the Java Development Kit, which is required to write and execute Java programs.

If you only want to run programs, the JRE is enough.
Note that the Java standard library is platform independent. If you want to run Java code on a platform,
all it requires is the JVM for that platform.
This means that alternative languages like Scala and Kotlin can make use of these libraries
and similarly Java code can use compiled bytecode generated from other languages.

Wait ... what the heck is **Java SE (Standard Edition)** now?  
Java SE is a specification for the JDK and a bunch of extra libraries (e.g., Java FX).

There's also Java ME (Mobile Edition) to run on low capability hardware.

There's also Java Card for ... smart cards and stuff.

There *was* a Java EE but it's not maintained anymore, it's now become Jakarta EE (not sure what that is).

OpenJDK is an open-source implementation of the Java SE spec. Only source code though.
It doesn't have any binaries or installers for the JDK.  
Who builds the binaries / installers? People like Azul systems, Oracle, Microsoft, Adoptium etc.
They might modify the source code a bit.

## Setting up dev environment
1. Firstly, you will need a JDK. You can download a build of the OpenJDK. It's a zip file containing these items: `bin  conf  include  jmods  legal  lib  release`.
2. Unzip the contents in some location e.g., `/home/user/jdk`. This will be the value of `JAVA_HOME` variable
3. Add the location of the binaries to your `PATH`, you can add `$JAVA_HOME/bin`

Where is the compiler and the JVM? In the `bin` directory.

Where is the standard library? In the `jmods` directory. There are a bunch of `.jmod` files.


## Packaging the output
Turns out, the process is similar to creating tar files. It actually follow the `.zip` format though.
JAR files on decompression contain `.class` files arranged in the required structure.
They also have a `META-INF/MANIFEST.MF` file which contains metadata about the JAR and tells where the main class is that has to be run. This will be specified in a key-value pair like so: `Main-Class: net.java.bingo.Main`.

You can specify a custom manifest file that it will use to create the actual manifest file.
To do that, just add a `m` option to the command e.g., `jar cmf MY-FAV-MANIFEST.MF Output.jar src/`.

## Why is the standard library a bunch of `.jmod` files?
This change happened in Java 9 to make a space optimize JRE.
They are very similar to `.jar` files, they can additionally have native code and other stuff that don't go in JAR files.

They are to be used in the "link" phase between the compile and run phase. This is where a set of modules can be assembled and optimized into a custom run-time image. As per the [JPE](https://openjdk.org/jeps/261)

## The JVM
What's the default VM that Java uses? It's called the [Hotspot VM](https://openjdk.org/groups/hotspot/).
The Hotspot project has also developed runtime libraries.

The Java VM could be seen as being made of:

### Class loaders
Loads classes as required, dynamically, to the JVM.

### Interpreter
This executes bytcode line by line.

### JIT Compiler
If a method is being called frequently, it is compiled to machine code in a separate thread.
Once that's done, the next invocation would be through machine code directly. Meaning it will be faster.

There's also the garbage collector and platform specific libraries required for execution.
For cases where you need to access native methods through Java, there's also the Java Native Interface.

### Passing CLI properties to the JVM
There are some standard options e.g., -cp for classpath, -agentlib, -dsa etc.
How do you define system properties for your program? Through `-Dprop-name=prop-value` .
There are some non-standard options which can be set with `-Xprop-name=prop-value` .
Some advanced options can be set with `-XX`

I should probably create a separate section about exploring the JVM.

## The JRE
What are the classes / properties files required to run java programs?
Note that the location of these classes have changed with Java 9. [Relevant JEP](https://openjdk.org/jeps/220) explaining the new locations.
There's no more an `rt.jar` or `tools.jar` (which were known as bootstrap classes), they have been moved to modules in the `lib` directory.
The configuration files are at `$JAVA_HOME/conf`.

Now, it's also possible to create your own JRE which contains the modules only required to run your app.
This can be done via `jlink` and helps in making Java apps which ship with their own JRE and don't have
any requirements on the users to have a full JRE pre-installed.

How can you do so? Adoptium has a [small guide](https://adoptium.net/blog/2021/10/jlink-to-produce-own-runtime/).

## The development tools
There are a bunch of extra tools like disassemblers, debuggers, security tools, RPC tools and monitoring tools (e.g., jps, jconsole etc) which come with the JDK. A small list of them can be found here : [Baeldung](https://www.baeldung.com/jvm-vs-jre-vs-jdk#jdk).

## Fundamentals of Java
The unit of compilation in Java is a class. There should be only one top-level class in a file and they both should have the same name.

Like `auto` of cpp, there is `var` here. `null` is a type used to represent `no-object` or `empty`

There are 8 primitive types (bool, int, float, etc) and they all exist in the bytecode as well. They are all signed but you can use some methods to perform unsigned operations on them (which will treat them like unsigned). The char here is 2 bytes! (For unicode) and there is a `byte` type for 1 byte.

All the primitive types have corresponding wrapper classes (with capital first letter) that can be used whenever we want to use an object alternative of the primitives. Conversion between them is automatic, just do `=`. This is called autoboxing.

Note: Java does not do trivial conversion from int to bool (for if statements)

Switch can work on String / int / enum. From java 14, the switch can be used as an expression and there is no fail-through.

Annotations (look like python decorators) are part of the language and there are a few default. They are instructions to the compiler.

All classes live in packages (which acts like a namespace).

For all object type variables, java stores only the address of the object in the variable (that's why the variable is called an object reference). Declaring an object reference doesn't create an object. They are created with the `new` keyword. The address of the object is its identity.

How are objects initialized?

First, space from the heap is taken.

Second, all the members are set to default values (i.e., 0 for primitives and null for object types)

Next, the inline initializations of the class + initlization blocks are run

Finally, the appropriate constructor is called. Like C++, a default no-args constructor is provided if no constructor is there. You can call one constructor inside another! Using this(args, to, the, other, constructor) and then continue to do other stuff. The call to the other constructor should be the first statement if it is there.

There's a reference variable available in all methods of the class called `this`.

JVM bases its nottion of equality on object identity. That's why var1 == var2 compares addresses and var1 = var2 just changes where var1 points. To compare objects use the .equals method that may have been overriden by class creator.

Once an object has no more references to it, it may be cleared by the GC (non-deterministically). The static members of a class are just like in C++.

### Inheritance:

Java only has public inheritance. Instead of base and derived, here we in talk in terms of super and subclass. You can add an @override annotation on a redefined method so the compiler will check that there is indeed a method with the same signature in the super class. It is not required tho! Like how the scope-resolution operator was used in C++ to call the base class method in the child class method, in java you must do it like this: `super.method(arg1, arg2)`. Yes, the `super` keyword is required.

The super constructor must explicitly be called in the subclass constructor (unless the super has a default ctor).

The class of an object specifies the implementation of its methods, specifies what private and public members it has. A type on the other hand only specifies the public interface (attributes and methods). That's why many classes can have a common type and a class could have multiple types. Subclassing means using the stuff defined the parent class inside the subclass, be it variable / method definitions. Subtyping means that any method that could be applied on objects of the supertype can be applied on the objects of the subtype as well. Subtyping is based on substitutability, it allows applications to be designed at the highest level of abstraction.

All java classes ultimately derive from java.lang.object, this happens by default if no `extends` is specified for a class. It allows some language features (e.g. toString, hashCode, notifyAll etc) to be available into all objects.

The instanceof operator can be used to test if an object has a particular type, e.g., bool lol = objekt instanceof Taipe. Objects can be cast up or down, e.g., with (classA)objkt. If the down-cast is not possible, it will cause run-time error.

Abstraction means concentrating on what can be done rather than how it is done. A method could be marked as abstract (which means it doesn't need to have a definition), following which the class must be marked as abstract, following which no objects can be instantiated of it. Abstract classes can also have attributes, not just methods. e.g., a lion class can extend an animal class and then create a lion object, but an animal is an abstract concept and you can't create an object out of it. A class can only extend one abstract class.

Interfaces are contracts, they can be used to implement kind-of relationships. All their class attributes must be static and final. A class can implement multiple interfaces and an interface can extend multiple interfaces. Interfaces can have default methods which are implemented in the interface itself and don't have to be implemented by the class. To prevent confusion, .equals and other stuff from Object cannot be overriden by default methods in interfaces. If two interfaces define the same default method, the implementing class must specify which one.

Abstract classes have state, interfaces don't. Java prevents multiple inheritance of state.

### File Organization
Java's file organization is so weird. You need to organize your directories according to the package. It's not strictly a necessity for the source files when you're compiling with javac but it is very important for the .class files. They must be in the necessary structure. If you're not sure how to do it, create an empty dir somewhere (e.g., ~/notnulldir) and when compiling the source files do javac -d ~/notnulldir somesource.java. Now everything is compiled in the right place. To execute the program, do java -classpath ~/notnulldir F.Q.N.oftheclasswhichhasmainmethod The fully qualified name of a class is the package name + '.' + classname. E.g., in the course.demo.what package, if there is a file called mathstuff.java the FQN of that will be course.demo.what.mathstuff

### Containers

The main types of containers in Java are - arrays, collections, and transparent containers.

Arrays are fixed size containers. They can be arrays of primitives or objects.

Arrays themselves are objects. When you create a new array with e.g. new type[100]; the elements are initialized (primitives to 0, objects to null). They can also be init with custom values using the {} syntax. Type[] arr = {3, 9, 16}; Java doesn't check array index bounds check at compile time. If such an access happens at runtime, it will throw and arrayindexoutofboundsexception.

Arrays can be passed to and returned by methods.

Arrays are covariant, i.e. an array of type T elements can hold any object that is a subtype of T as well. You can also assign an array of subtypes to an array of supertypes.

### Enums
Enums in Java are a bit different from C++. You create an enum type with : public enum Naem { A, X, B, Y, C, Z };
Then, a var of the enum type can be made like this: Naem somethingIDK = Naem.B; it can be compared with other values of that enum, has a nice toString. All the values of the enum can be looped over with Naem.values() which returns an iterator. They can be used as switch case labels. Internally, a class is created for the enum and each value becomes an object with two main props: name, ordinal (the actual integer value for it, which is used for comparison and all). Additional data and function members can be defined on the enum type and the enum values can then make use of it. A constructor can also be define :O

### Annotations
Annotations can be used during the compile time as well the run time. It can be used to perform checks, mark stuff, or even generate boilerplate code.

The general format for it is: @annotashun (somekey="someval", somekey2="someval2")

The parameters are optional, you can have an annotation like this also: @simpleanno // this is called a marker

Classes may be defined in other classes, methods, or any arbitrary block.

You can do it either by having a static inner class (when there is tight coupling but there's no relation between outer class instances and inner class instances. These are compiled to a separate .class file. To create an object of the inner class you must write it as OuterOne.InnerOne objkt;) or a normal inner class aka member classes.

For a member class, you must have an outer object to have an inner object. The inner class has access to all members of the outer class. Useful to implement helpers e.g., iterator of the outer class.

Classes can be defined in any code block, they are called local classes. They have access to local variables in the block where they are declared (only the "final" ones tho, because of weird limitations).

There can also be anonymous local classes which are used to just create one object. It must be defined in terms of an existing class / interface.

Java has a good set of types to manipulate collections of objects. They are known as they collections framework. It has the 'Collection' class (i.e., java.util.Collection) at the root (note that the 'Map' class doesn't come under this tree, it is separate). The framework has interfaces, and for each there could be multiple implementations. e.g. list has arrayList and linkedlist. Collection elements must be objects. They should override equals (the one from 'Object' just compares references, like how == does) and hashcode properly. The commonly used types are:

- Java.util.List provides an ordered collection of objects - ArrayList (to be preferred), LinkedList, Vector (thread safe). Syntax is List> ar = new ArrayList>();
- Java.util.set collection with no dupes - hashset, treeset (has natural order)
- Java.util.queue is FIFO - linkedlist implements this
- Iterator defines hasNext() and next() - implemented by every collection, the collection also has an .iterator() method which returns an iterator. The collection should not be modified while iterating through it. This is what is used in the range for loop of java.
- `java.util.Map<K,V>` - `HashMap` (allows null keys and values, should be preferred), `LinkedHashMap` (extends hashmap but maintains insert order)

There's the `Comparable<T>` interface which requires the class to have an `int compareTo(T other)`.

There's a fun class called 'Collections', totally different from 'Collection', which has utility methods like `.sort()` (which relies on the element class implementing `Comparable`). It has `copy`, `fill` and so on.

What if you want to compare some elements who haven't implemented `Comparable`? Make a separate class that implements `Comparator<T>` for that element type. An object of this can be passed to sort like so: `sort(colexon, customOne());`

There's `SortedSet` and `SortedMap` whose iterators iterate over elements in sorted order which needs either the element to have `Comparable<T>` implemented or you need to provide a `Comparator<T>` object.

Want to compare any particular field from a type? Check out `Comparator.comparingInt` & stuff.

Most collections aren't thread safe, 'Collections' has some utilities to return thread-safe / immutable versions of collections.

Generics in Java work a bit differently. Instead of generating classes and functions from templates, when you use a generic variable it is just a variable of type Object. When you try to read its value it is type cast to the specific type. This is called type erasure. The type checks at compile time make sure there's no `ClassCastException` at runtime. Why do langs like C++ & Rust prefer to generate classes / functions for a type parametrization? They could possible generate optimized code for that type.

In Java if you use a collection without specifying the type you can put any object into it. This means the type-checking needs to be done by you when retrieving the data out of it.

Substitution principle: If B is a subtype of A, B can be used anywhere A is expected.

Variance: Relation between generic collections of elements.
- Invariant - Different parametrizations of generic types are invariant by default, you cannot assign one to the other
- Covariance - Creating one of the two with `? extends typeUsedInOther` will mark it as covariant, it means that the other container (of the concrete type) can be assigned to this one. The covariant one cannot add new objects to the container object tho. E.g. `List<Market> mall = new List<*>()` and the covariant one `List<? extends Market> something = mall`, but now you can't add stuff to 'something' though you can get stuff out of it.
- Contravariance - You can create a contravariant object by parametrizing the generic with `? super otherType`, like `List<? super Market>`. Now you can add elements (any object which has a type of or a supertype of otherType) to this but can't extract from it.
- Bivariance - both co & contra

Example of generics: A generic function
public static <t, g=""> List<g> fromArrayToList(T[] a, Function<t, g=""> mapperFunction) {
    return Arrays.stream(a)
    .map(mapperFunction)
    .collect(Collectors.toList());
}

Why does java do type erasure? To maintain backward compatibility with code that was written before generics.

### Exceptions
An exception is a problem at run-time. All of them extend from 'Throwable' to support the throw-catch functionality. Exceptions are objects.

There are errors which are problems that are caused by the JVM e.g. `OutOfMemory`, cannot load class file etc.

There are `RuntimeException` (which inherits from exception) objects which are caused by program problems such as divide by zero or using a null reference.

These two are expected to crash the program and recovery is not expected. The programmer need not try / catch them, that's why they are called unchecked.

All other exceptions are expected to be caught and handled by the program using try / catch. A method should tell what kind of exceptions it can throw.

### Handling them:

Try and catch work just like cpp. There's also `finally` which will always be executed after both of them, unless there was a `System.exit` somewhere. `throw new SomeClassWhichExtendsException();` To catch multiple exceptions in one catch block you can do `catch (BException | CException | DException theObjkt) {}`

### Try with resources:

`try (someResourceClass someResourceObjkt = new someResourceClass()) { // can use that resource here }` // the resource 's `.close()` method will be called automatically when the try block is left. Ofc, that class should implement the `java.lang.AutoCloseable` interface.

You could throw the same object that you caught with catch, but why even catch it in the first place then? Exception handling in java is expensive.

It is okay to throw exceptions in the constructor when you are not able to initialize the object correctly. It is better than leaving the object in an incorrect state.

### Testing:

For unit tests, there are test drivers (which run the tests). There are testing frameworks which provide inputs, compare outputs. e.g. JUnit. The framework also has support for stuff like setup, tear-down etc.

JUnit has support for parameterized tests, the input & output for which you can specify separately from the test code, e.g. in an array outside or maybe even in a CSV.

Fun fact: `assertThrows` actually returns the captured exception object so it can be examined further if necessary.

#### What to test:

If your app has some capability make sure it works (i.e., functionality). Don't test anything outside public interface of class for unit test. Tested and well-designed code replaces the defensive coding style which might have been somewhat wasteful. Write the smallest number of tests that cover everything, do boundary analysis and figure out what will cover edge cases and input range.

Good qualities of unit tests - fast, independent, should not rely on human looking at result to validate (self-validate), should be able to run concurrently.

A thumb rule is to use mocks to test behavior and unit tests to test state.

### Operator overloading
Java doesn't have operator overloading, you can't overload the `()` operator either.

Then how do you make objects that can be "called" (executed like a function)? Make function objects.

Java 8 introduces functional interfaces. These are certain interfaces for common usecases:
- `Function<ArgType, RetType>` : must have method `apply` that takes in one arg and returns one val (there's also `BiFunction` which takes in two args)
- `Consumer<ArgType>` : must have method `accept`
- `Supplier<RetType>` : must have method `get`
- `Predicate<ArgType>` : must have method `test`

They are all tagged with `@FunctionalInterface` (which means that they must have a single abstract method)

This stuff can work really well with callbacks like so:
```java
public class xyz implements Function<Integer, Integer> {
    public Integer apply(Integer arg) { return arg * 235; }
}

public class somethng {
    public static void huhWhat(Integer f, Function<Integer, Integer> funny) { funny.apply(f); }
}

somethng.huhWhat(412, new xyz());
```

The `Function<A, R>` interface has support for composition: `f.compose(g)` passes on the result of g as an arg to f.

### Lambda expressions:

They are function literals which can be used anywhere where function objects are to be used. The general syntax is `() -> {}`. They are of type `java.lang.Runnable` which is annotated by `@FunctionalInterface`. Lambdas don't create objects and thus have low-overhead, in-fact they are effectively inlined.

Fun fact: In java, closures close over values, not variable. Thus trying to close over a variable and modifying its value won't work. (This is why you cannot modify the value of a var in a closure in java)

### Method references:

Make the methods of a class into function objects with this syntax: `KlassName::methodName` or `objktName::methodName` (depends on the method, the constructor's method name is 'new')

### Streams API:

All `Collection` objects can call .stream or .parallelStream to get a stream that can be operated upon. Some common operations are map, filter, flatMap, forEach, reduce, count, sorted, collect, and distinct. These are either terminal or intermediate operations.

Collect is a fun one. It transforms stream objects into lists, sets, maps etc e.g. .collect(Collectors.toList())

Flatmap takes a function object that converts a colelction object into a stream and then joins all the streams of all objects into a single main stream.

There are special classes to make streams of primitives e.g. IntStream, DoubleStream

Optional is a cool class that represent the concept of something that may be null. It should be used instead of null. Can convert optional to object using opt.orElse(suitable value), opt.get() // which throws if the optional is empty (aka contains a null). How to make by hand? Optional.of(concrete value) or Optional.ofNullable(normal val or null, anything) // useful when Optional.ofNullable(thisIsAMap.get(keyWhichMayOrMayNotBeThere))

Any function that works on Taipe will also work on Optional<Taipe>, if the Optional is empty then the result will also be an empty optional. You can't do it just like that tho, you need to use opt.map(funktionObjkt) ! IMPORTANT! If the function itself returns an Optional and you don't want to end up with Optional<Optional<Taipe>>, use opt.flatMap
They can be used with streams, in fact when a stream has a terminal op that returns 1 object it will be an optional of that object.

### I/O -

Java has InputStream & OutputStream for io using bytes. It has Reader and Writer for io with characters. The standard I/O streams are Sytem.{in, out, err} which can be used to make Reader & Writer as well.

For neat, cin-like reading there's Scanner, create new Scanner(System.in) and read stuff from it like sc.nextInt(). There's also System.out.printf for well ... What if you're sneaky and want to use the console directly? Use java.io.console

There was a weird File class before Java7 to deal with files, but now there's NIO 2 with a Path class. Path.get(URI) can be used to get a path object. There's also `Files` (java wut??) to copy / delete / check attributes and otherwise mess around with files. Then! There's also FileSystem and WatchService (cuz why not).

Read lines from a file?

Path p = Paths.get(pathToFile);

Stream<string> data = Files.lines(p);

To read characters from a file use fileReader, stringReader to read from a string

### Execution

A thread object represents a schedulable entity. It doesn't actually do the work tho. It has a Runnable object which does the task. A thread may be associated with only one runnable but one runnable can be associated with many threads. A new thread object can be created with t = new Thread(runnableObjkt); A thread in the process is created on `t.start()` which subsequents calls the .run method on the runnableObjkt.

That's not the only way. It's also possible to subclass `Thread`, override the `void run()` method, and then create and start like so: `newT = new SubKlassOfThread(); newT.start()`.

The states that a thread object could be in are:

READY (not yet called .start), RUNNING (in the run queue), WAITING (called wait() or .join on some other thread), BLOCKED (for IO or trying to acquire some lock), TIMED_WAIT(Thread.sleep() called within), TERMINATED (done executing)

The runnable object that the thread has with it is called a "Task".

A daemon thread is one that does not prevent the JVM from exiting when the main thread is finished. The status can only be set before the thread is run, and if it isn't it will inherit from the parent.

Rather than dealing with threads, you could also just give tasks to executors which will then run them in threads. The executor can then run it in a separate thread, run all tasks in one thread, use a pool of threads and so on. Many executors are available in `Executors`. You can define one by implementing `Executor` and overriding the `void execute(Runnable task)` method.

To get some more control on an executor, create one of the `ExecutorService` class instead. Then you can shutdown the executor when you want and stuff like that. (The builtin executors are of this type)

The executor service also has a `Future<t> submit(Callable<t> task)` method which is useful when tasks want to return something.</t></t>

How to get a value out of a `Future` ? First check if it is ready with .isDone() and then you can get the value with .get()

There's a new future class in Java 8 that lets you create & use them by hand. You can create a future with new ComposableFuture<t>(). On the server side you can feed it a value with .submit(val) and the receival on the client side is the same as shown above.</t>

Communicating between threads - use the implementations of BlockingQueue whose .take() and .put() will block until it is possible to take / put them.

It is a good idea to .join() a thread after you .interrupt() it so that you know it has comepleted (if it does, sending an interrupt doesn't have to mean that the thread will exit).

Debugging java? These might help:

jps (get all java procs), jstat -class <PID> (info about a particular proc). Do ctrl-break on a java cli process to examine its threads. Java has a nice architecture for debugging. The back-end (written in native code) has full access to the JVM and the app running in it while the front-end (written in Java) provides a user interface for the debugger. They talk to each other via a Java Debugger Wire protocol. The JDK has a cli debugger in it (jdb).</PID>

Deep into Java execution!

GraalVM is a high performance JVM that also has an AOT feature to compile class files into binary executables. A JDK contains a Java compiler and a JRE (runtime environment). The JRE has a JVM, Java class libraries (the standard lib), and a java class loader (classes are loaded onto the JVM only when they are needed, at runtime). OpenJDK is an open source implementation of the Java SE. Binary distributions of the JDK are provided by vendors (e.g. Oracle OpenJDK, Azul Zulu etc)

### Reflection
A compiled .class file contains only one class (nested classes & anon classes of source files get their own class files too, although the name of that compiled class file is a bit mangled). All data in that file is stored in the form of unsigned data of 1, 2, or 4 bytes.

The official name for data members is fields and for member functions is methods. A class file only has info about its members, not of its superclass. Various other properties of that class (idk what exactly) are stored in key-value pairs called attributes. Info about whether the file contains a class / interface / etc and whether it is public / private / abstract and so on.

Classes themselves are objects! They are objects of the `Class<t>` class where T is the class name of the class of interest. The `Class` class is available as part of the reflection API. The `.class` method available on all classes returns the object of `Class<t>` type, its value can be known at compile time itself. How to get it from an object? Do objkt.getClass(), this is a runtime op tho and therefore its return value is Class<?> . How to get it with just the string containing the class name? Do `Class.forName{klassName}` (note that it is a static method) which will load the class if it is not yet. Also, Class objects are singletons (other than the wrapper ones for primitives).

Once you have the Class object, from now on known as `klas`, you can get a lot of info about it e.g., what are its methods, fields, modifiers, etc. Can even get its method by doing klas.getMethod("nameOfmethod"); can get constructors too using klas.getConstructors() or even a particular construcotr! Once you got the constructor (let's call that constructor object `cntor`) you can create new objects of that class using cntor.newInstance(args...); Dynamic object creation!

Oh, once you have the method object you can invoke it on an object of the class like so: mthod.invoke(objkt, args); // fun stuff :)

The default access modifier for a class and its members is 'package-private' (visible only within package)

Fun fact: although arrays are objects, the array class does not have a constructor or fields (the .length field is some magic apparently)

Is this reflection stuff secure? By default, yes. Although you can find out everything about a class you can only access fields / call methods of objects of that class that you could otherwise do in that context. You could give reflection more privileges by doing klasMembr.setAccessible(true); // now what if you want some more security there too? Use a security manager for fine-grained controls o.o

That's all for reflection. Time for class loading gory details:

Classes are not loaded until they are actually used. Like actually used, e.g., with the new operator to make an object. Simply declaring a variable of that type and never using it will not load the class into the JVM.

Fun fact: To run some code when the class is loaded into the JVM, put it in a static initialization block in the class. Who loads these classes tho? Well, the JVM can use multiple classloaders (even during the same program). Some of them will look in CLASSPATH, some can load files over a network, some will load classes from jar/war files. Each one can have different rules for naming of the class files (e.g., you can hae a CL that loads class files with the extension .KLAS instead of .clsas) They're all subclasses of the abstract base class - ClassLoader . You can find out which loader a class was loaded with using klas.getClassLoader(); By default, class use the class loader of the loading class (i.e., the one which is requiring the use of this class)

Some common classloaders are : the bootstrap CL (loads standard lib stuff), and the application CL (looks for stuff in dirs / jars specified in CLASSPATH)

These classloaders also have namespaces, therefore a class X loaded by classloader Y is not the same as class X loaded by classloader Z.

It's called reflection because it allows a program to inspect itself (not just itself, but also itself).

Side note: java beans is a spec. A bean is just a class that follow some format - e.g., has a no-args constructor, has getters and setters with names like getX and setY + all fields are private, and implements Serializable.

Last note: the finalize() mtehod of an object is called when the GC decides it's time to get rid of the object and is called right before destroying the object.