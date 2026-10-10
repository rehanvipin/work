---
weight: 7
title: ".NET"
# bookFlatSection: true
# bookToc: false
---

# .NET - Microsoft Java

The common language interface (CLI) describes a standard for executable code and a runtime so that many languages can be used on many platforms without needing to worry about the architecture. It describes these things (and others):
- Common Type System - data types & operations (yes, it has support for operator overloading)
- Metadata - information about program structure (i.e., they can have different structures but should have same metadata format)
- Virtual execution system - loads and executes CLI-compatible programs

.NET framework, mono, and .NET (previously known as .NET core) are all implementations of the CLI spec.

Now let's look at .NET:
It has a CLR (common lang runtime - basically like JVM), CTS, and a Base Class Library with a lot of useful classes (it's also known as the FCL where F stands for framework). CLR is dotnet's implementation of the CLI. Its input is MSIL (intern. lang). It has a garbage collector, debugger, JIT compiler, class loader, and also a native image generator (which can compile MSIL into native code all at once and then use it for execution).

CTS is split into value types & reference types.
The BCL is split into multiple namespaces (but also, there's no concept of namespaces in the CLR somehow).
Namespaces are like java packages other than the fact that they aren't dependent on directory structure or file names at all.

An assembly is a set of one or more deployable files (e.g., EXEs and DLLs). It has MSIL code. It also has metadata about security, version number and so on. You can generate one for C# using `csc.exe [source file names] /target:[exe|library...] /reference:[other assemblies that this one depends on] /main:[name of class whose main method should be used]`

Time for C#: (v10 is the one of interest as of 2022 Sept)
Members of a type can be: fields, methods, constructors, destructors and some new stuff like properties, indexers, events and also operators. Visibility modifiers: for types it could either be public or internal (accessible by everything in assembly, the default) and for members of types it could be public, private (default), protected, internal (again, only accessible by stuff in its assembly).
C# has useful aliases for types in CTS e.g., string for `System.String`, int for `System.Int32`
C# doesn't require class name to be same as file name. It has support for top-level static classes (you can't instantiate objects for them) and for class-nesting just like C++ & Java.
It has contextual keywords: https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/keywords/#contextual-keywords of which one of them is `var`.
C# arrays are like Java arrays but it's easier to initialize them (using the curly brace syntax e.g., `var arr = new[] {1, 3, 4}`).
The range-for over here is foreach `foreach(var a in array) {}`. Attributes are like java annotations.

Each assembly needs to have a manifest. In a single file assembly, it's within that file itself. When there are multiple files (usually split up as modules which have a .netmodule), it is in the main module. The metadata also has information about all types in the assembly and their members. It also has information about which other assemblies this one requires. This can be explored with ILDASM.

How to use a dependency assembly with current application?

Do csc.exe /rotherStuff.dll mainStuff.cs. When the CLR runs the mainStuff.exe file it will search for that in the current directory for the libName.dll (where libName could have been 'otherStuff'), then look for a dir called 'libName' and so on. You can specify where to look by putting dir names in the config file, which usually is appName.exe.config. This method of using external dependencies by placing the assemblies in the same dir is known as private deployment. There's also the shared deployment method where multiple apps can use the same dependency assembly. It is put in the 'GAC' (global assembly cache). These assemblies must be 'strong-named' i.e., require name, version number, public key. You compile and run the application the same way as before, just that now, during compile time it will search in the GAC, find the version of the dependency file, pubkey etc and put it in app manifesto.

Nugget allows specific versions of libraries and assemblies to be imported into a project so there's no conflict because of global package management.

Properties are members of classes that appear to the end user somewhat like fields. Depending on the definition it may be possible to set / get them. (A default setter is provided if not mentioned, which allows property to be set only in constructor). If the get and set aren't explicitly defined, a private backing field is created and a getter & setter made for it (this is called an automatic property). General syntax: visibility prpoName { visibility get (option def) visibility set (optional def) }. They cannot be used as a ref / out parameter.

A type can also have indexers. The syntax for this is : 'visibility typeThatIndexerRepresents this(int x, string y, ...) { get {} set {} }'. You can have overload indexers based on types & number of args.

There are primitives in .NET, e.g., int, boolean, char etc. They are all value types. There are also built-in value types which are not primitives e.g., string, dateTime

.NET is an ecosystem. There is a .NET standard (versioned ofc) which has many implementations.

.NET framework was the legacy one, meant only for windows. .NET core is the new one which is cross-platform and open-source. Mono (used extensively with Xamarin which is for building mobile apps) is the open-source competitor to the .NET framework.

To develop desktop apps in .NET there is UWP (unified windows platform) and WPF (also windows). For web apps there is ASP.NET

.NET core previously didn't have WPF and windowsForms but with v5 and the rebranding to just .NET, it now has those too.

.NET has synchronization objects like Muetx, Semaphore, EventWaitHandle which derive from WaitHandle which contains methods to block & signal threads. It also has barrier and concurrent-safe collections. There's also facilities to do parallel programming with TPL & PLING.