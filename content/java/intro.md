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