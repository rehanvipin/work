---
title: "Build tools"
weight: 4
# bookFlatSection: false
# bookCollapseSection: false
# bookComments: true
---
# Building Java code
Why not just use `javac` ? Well, you can but tools save time because there could be:
* Many Java files that need to be compiled for the main file.
* Incremental builds, building only the classes whose code has changed.
* External dependencies that need to be fetched. (Transitive dependencies! Versions!)
* Package code into a `.jar` or `.jmod` file.
* Custom arguments for compiling certain classes ?
* Custom arguments for running the tests.

This is what tools like `maven` and `gradle` try to solve.

## Compile and execute with external libraries
Suppose you wanted to use the `jackson-databind` package, how do you do it manually?
1. Use the classes from the library in your code, e.g.:
```java
import com.fasterxml.jackson.databind.ObjectMapper;
class A {
    void fun() {
        ObjectMapper mapper = new ObjectMapper();
    }
}
```
2. Download the jar file for the package from maven-central. You will now have a file like so: `jackson-databind-2.15.2.jar`
3. Compile your java file, i.e., `A.java` like so: `javac -cp jackson-databind-2.15.2.jar A.java`
4. Download the jar files for all transitive dependencies of `jackson-databind`,
which happen to be `jackson-annotations-2.15.2.jar` and `jackson-core-2.15.2.jar`.
5. Execute your class like so: `java -cp jackson-databind-2.15.2.jar:jackson-annotations-2.15.2.jar:jackson-core-2.15.2.jar:. A`
(note that the current directory was put on classpath as well, this is so that it can find class `A`)

What's the default classpath? The current directory from where you are executing `java`.  
Will it search recursively in the classpath directories trying to find your files? No.  
There is no trying. It will look in the exact location. Nothing else.

There's also `-module-path` ??

The `javac` command also requires the `-cp` argument when there are dependencies.
You can see which class files and source files it loads by passing the `-verbose` option.
The command doesn't just compile the `.java` file you have specified, it would compile any other `.java` files that your file uses and don't yet have corresponding `.class` files.

## Third party dependencies
Where are these located? Nowadays, most of them are hoste in maven-central (sort of like npm).

## Maven
Helps in managing project dependencies, build / test tasks, and documentation.
You can the project info in a declarative format in pom.xml.

### Setup
Download and install `mvn` in your system.
The `MAVEN_HOME` variable is required to be set in the parent dir of `bin` where `mvn` is present.

## Gradle
Similar objectives to Maven, but you can specify the project info in a ... scripting language format. With Groovy / Kotlin.