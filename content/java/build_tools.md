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

## History of build tools
* `javac` and `java`, the only things you really need
* `make` to reduce the commands you need to write
* `ant`, as a platform-independent alternative, and was customized to work better with java (e.g., compiling multiple source files at once, rather than one at a time, as each dependency is found. scanning folders for java files, rather than having to add them one by one as new files are added.). `ivy` was developed to do dependency management for it.
* `maven`, which added many conventions over configurations, and plugins to make config files smaller, and makes them uniform. still in XML.
* `gradle`, which brought a DSL to do things programatically

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
Can help in reducing duplication.

### Setup
Download from `gradle.com`, and you have the `gradle` binary. It uses `GRADLE_USER_HOME` where it places temporary files.
Once you have an inital binary on your system, you can add gradle "wrapper" files for each project.  
That way, you can specify which version of gradle should be used for that project, and it will download the binary specifically for it (if not already there).  
This also means that anyone downloading your code and running it, just needs to have the JDK installed, and nothing else.

# Gradle (In Detail)
Compile and run your code with doing the least amount of configuration.  
There are `.gradle` files which define `tasks` which can build / run / test your code. The default one is `build.gradle`.  
Those files are defined in the `Groovy` language by defualt. You can also have "builds" in the `Kotlin` language, in `build.gradle.kts` files.  
Gradle itself runs on the JVM, since it is built using Java. You can choose which JVM it should run on.

## Build
Gradle uses a concept of "build". A project having a `build.gradle` file is called a build. Nearly every gradle command runs wrt a "build".  
You can create such a buildscript for your project by doing `gradle init` (which also adds extra stuff e.g., wrapper & settings.gradle files)

There is a build lifecycle which happens when `gradle xyzTask` is run.
It does "initialization", which is reading the settings file,
then does "configuration", which is executing the buildscripts for each project involved, creating the task graph etc.,
then does the actual execution of each task involved.  

This means any code written outside of a task defintion in the buildscript will be executed in the configuration phase.

Tasks can be chosen to run in parallel with the `--parallel` flag.

Gradle provides the ability to specify which Java version must the code / generated class files comply with.
This can be set via `sourceCompatibility`, `targetCompatibility`, but it is preferred to set a `toolchain` instead.  
The `toolchain` lets users specify which JRE / JDK (if required) must be found (or downloaded) for compiling / other tasks of the build.  
It is specified like so:
```groovy
java {
    toolchain {
        languageVersion = JavaLanguageVersion.of(21) // optional
        vendor = JvmVendorSpec.AZUL_ZULU // optional
    }
}
```

It is also possible to specify different java version requirements for each task separately, in which case those multiple versions are part of the toolchain.

## Tasks
They have lifecycles, and we can add code to run at some stages of these lifecyles, through some lifecycle hooks.  
Then you can invoke the task from gradle e.g., `gradle myNewTask`, and that will go through full life cycle of the task.  
Run `gradle tasks` to see all available tasks for the "build".  
Some lifecycle hooks which are called during a task's execution lifecyle are `doFirst`, and `doLast`.

Tasks are objects of the `Task` type, and are instances of the `DefaultTask` class by default.  
There are other inbuilt `Task` sub-classes as well e.g., `Copy`, `Jar`, `Zip` etc. of which task objects can be instantiated.

To know more about a task, run `gradle help --tasks taskName`.  

It is also possible to define a new task class which takes inputs & produces outputs. Those are of types `TaskInputs`, and `TaskOutputs`, and are defined with one of the @Input, or @Output annotations e.g.,
```groovy
abstract class TestTask : DefaultTask() {
    @Input
    val xyz = ""

    @OutputFile
    val abc: File = File("/var/tmp/hi.txt")

    @TaskAction
    fun someFuncName() {
        abc.createNewFile()
    }
}
```

To add dependencies between tasks, just set the `dependsOn` property in the depending task.  
The list of all tasks are available in a `TaskCollection`, which is by default called `tasks`.
To add to it, we do `tasks.register("newOne")`, and to fetch we do `tasks.named("newOne")`

The `@TaskAction` annotation defines the function which should be run when an task of that class is invoked.  
That function is called after `doFirst`, and before `doLast`.
It is also possible to create tasks which don't have any action at all e.g., `build`. They can have other properties and functions, but don't really have an action to do by themselves. They are known as "lifecycle tasks"

## Projects
When you run `gradle` inside a "build", it first looks for the settings.gradle file, creates a "Settings" object,
determines which projects are in the "build", and then looks for the buildscripts (some `*.gradle` file) for each of those projects to construct "Project" objects.
There is only one root project for each "build".

## Plugins
These add extra tasks to the "build" and extra properties too. Once you add a plugin, you can re-run `gradle tasks` to see what new stuff is there.
Plugins can be added via `plugin { id: 'java' }` in the buildscript or `apply plugin 'java'` (deprecated).  
Some of the commone ones are:
* `java` - adds some tasks to build, test, and package code, docs etc. uses some conventions to determine where source files, resources etc. are
* `application` - adds tasks to run app, generate dists and run scripts
* `java-library` - adds properties, and splits dependencies into `api` and `implementation`

Plugins themselves can extend other plugins. Therefore, if you have `java-library`, it will implicitly import the `java` plugin as well.

The `java` plugin knows where to look for the code via some default paths in `sourceSets`.
If a project has a different layout, it can set the `sourceSets` to the custom paths.

## Syntax
The groovy & kotlin buildscripts have stuff like this:
```groovy
plugins {
    id("plugin")
}
```

That's a shorthand for this code which uses a lambda function:
```groovy
plugins(function() {
    id("plugin")
})
```
where `id` is a method of a "this" object which is identified by Gradle. so something like this:
```groovy
ext {
    wassup = "hi"
}
```
might do `ext.setWassup("hi")`

how are so many objects, and methods available in the script files? gradle [auto-imports](https://docs.gradle.org/current/userguide/writing_build_scripts.html#script-default-imports) them.

## Dependencies
How to use other people's code in your project. You can depend on external / internal libraries, projects.  
Dependencies are fetched from a "repository". Each dependency is assumed to be a java module.
Repository could be a directory on the file system, a web server serving files via the maven / ivy spec (which means either providing maven POM files or ivy XML files, or even a .module file).
The metadata file for each library will tell where to get the jar file / other dependency "artifacts" from.
It also has other metadata like author info, transitive dependencies info, etc.
Usually the metadata file and artifacts are in the same location.  
Once artifacts are fetched, they are kept in a cache (along with metadata files, albeit in a diff place) in the file system for quick use.

Different dependencies can be required at different times, e.g., some for compiling, some for running, some for testing etc.
Gradle helps in specifying different scopes for each one. The **main** scope is the `implementation` scope, which sets
`runtimeOnly` and `compileOnly` scopes. The `testImplementation` scope inherits from `implementation` and it also sets
`testRuntimeOnly`, and `testCompileOnly`. There are many other scopes too, and plugins can define their own.  
However, that's not what is actually used by the `build`, `run`, and `test` tasks.
They use `compileClasspath`, `runtimeClasspath`, `testCompileClasspath`, which are resolvable configurations (meaning it will go and find the jars & transitive dependencies etc) and inherit from `implementation`, `compileOnly`, etc. which is where you actually list the deps that you want.

Each dependency can be represented by a string `"groupId:artifactId:version"` or longer `group "" name "" version ""`.

## Fun stuff
* Gradle [supports](https://docs.gradle.org/current/userguide/continuous_builds.html) "continuous invocation of tasks" based on files changing. This can be done by adding the `--continuous` flag.
* You needn't write the full name of a task. If you have tasks 'projects', and 'properties', you can just do `gradle proj` to run the first one.