---
title: "Build tools"
weight: 3
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---
# Building Java code
Why not just use `javac` ? Well, you can but tools save time because there could be:
* Many Java files that need to be compiled for the main file.
* Incremental builds, building only the classes whose code has changed.
* External dependencies that need to be fetched.
* Package code into a `.jar` or `.jmod` file.
* Custom arguments for compiling certain classes ?
* Custom arguments for running the tests.

This is what tools like `maven` and `gradle` try to solve.

## Third party dependencies
Where are these located? Nowadays, most of them are hoste in maven-central (sort of like npm).

## Maven
Specify the build script(s) in a declarative format in pom.xml.

## Gradle
Specify the build script(s) in a ... scripting language format. With Groovy / Kotlin.