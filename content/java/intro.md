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
Turns out, the process is similar to creating tar files.
JAR files on decompression contain `.class` files arranged in the required structure.
They also have a `META-INF/MANIFEST.MF` file which contains metadata about the JAR and tells where the main class is that has to be run. This will be specified in a key-value pair like so: `Main-Class: net.java.bingo.Main`.

You can specify a custom manifest file that it will use to create the actual manifest file.
To do that, just add a `m` option to the command e.g., `jar cmf MY-FAV-MANIFEST.MF Output.jar src/`.

## Why is the standard library a bunch of `.jmod` files?
This change happened in Java 9 to make a space optimize JRE.
They are very similar to `.jar` files, they can additionally have native code and other stuff that don't go in JAR files.

They are to be used in the "link" phase between the compile and run phase. This is where a set of modules can be assembled and optimized into a custom run-time image. As per the [JPE](https://openjdk.org/jeps/261)