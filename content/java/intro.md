---
title: "Setup"
weight: 1
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---
# Introduction & Dev Setup
Firstly, what is Java? Let's look into the Java product as a whole. It has:
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

OpenJDK is an open-source implementation of the Java SE spec. Only source code though.
It doesn't have any binaries or installers for the JDK.  
Who builds the binaries / installers? People like Azul systems, Oracle, Microsoft, Adoptium etc.
They might modify the source code a bit.