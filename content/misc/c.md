---
weight: 5
title: "C"
# bookFlatSection: true
# bookToc: false
---

# C Programming Language
C++ Also

A programming language must contain:

Types, variables, values, functions, expressions, and flow control.

C is a general purpose imperative programming language. C++ is multi-paradigm and is almost a super set of C.

The actual compiled program binary is what is stored in the "text" segment of memory. For interpreted languages, the source code is stored in data segments. There are also VM languages, which compile their source code into pseudo-machine code that's executed on the language's VM in an interpreted manner. Over here the VM program is in the text segment while the bytecode is in the data segment. (e.g. Java and .NET) This method is faster than interpreted but little slower than compiled but it could be platform independent unlike compiled.

ALL processes have the same virtual address space layout.

An expression is anything that yields a value.

C++ compilation > pre-processor -> compiler -> inline expansion -> template instantiation -> optimization -> link editing


Namespaces are a mechanism for grouping related things in a scope. The "using" statement lets you bring things from a namespace into the current scope.

The list initialization syntax prevents implicit narrowing. There's also copy initialization and other types of init. There's definition which relies on the constructor to initialize (it might not call the constructor too, better to call it explicitly) and then there's the concept of assignment.

Pointers. There's a nullptr_t type for nullptr. The & syntax on an array will return the address of the first location because the array is just a label. It's not a variable and doesn't have a memory allocation for it.

Enums. You can define anonymous ones or you can give it a name (making it a type e.g., enum id = {R, O, F, L}; where you can use it like so: lol variablename = F). They are compile time constants. You can also do stuff like int idk = F.

Arrays. Their size must be known at compile time.

Constexpr is a hint you give the compiler telling that a function / expression could be evaluated at compile time and telling it where it should be evaluated at compile time. E.g. constexpr int a = // something eval at comp time. And constexpr int fun() { // something

Typedef newtype oldtype. Using newtype = oldtype. Using is more powerful as it can work with templates and stuff.

Operator precedence and associativity: https://en.cppreference.com/w/cpp/language/operator_precedence

Whatever values are part of && and ||, only 0 or 1 is returned as the result of evaluation.

LVALUE refers to memory address of a variable

RVALUE refers to value stored in variable

A variable can have LVALUE and RVALUE. A constant can't be used in LVALUE places even though it has a place in memory because the rules forbid it. Literals don't occupy space in mem so don't have LVALUE property.

A comma expression is a series of expressions evaluated from left to right. The expression's value is the last value on the right. Left associative means evaluation from left to right. A sequence point is a place of guaranteed expression evaluation so that it is known that all side effects have taken place. E.g. &&, ||, the comma itself, ?:

The comma operator is also being used in variable declaration.

Static_cast evaluated at compile time and dynamic at run time. Reinterpret is for pintors. Const is to remove constness / volatility.

A hanging else binds to the most deeply nested, unmatched if (when brackets are not there). Variables can be defined in the if condition in which case it is accessible inside the if body (and the else too!) The switch expression must be integral.

While is entry condition loop. Do-while is exit condition loop. For is entry conditon.

In range for, the datastructure being looped over should provide the begin() and end() member funcs. They should return iterators that provide operator++, *, and !=. (In 17, the Iterator returned by end can be a diff type).

## Functions:

Params are called formal params while arguments are called actual params (in academia, IRL just use params and args). In C if you want to declare a func that takes no params do f(void); else f() means that it could take any number of params. Default values for params must be in the declaration (this is imp when the declaration and definition are separate). Cin uses call by reference. Inlining happens before compiling to object files. Extern C prevents mangling. Scope is where a var can be accessed, lifetime is where the variable is in a valid state (e.g. static vars in funcs have different scope & lifetime)

## Compiling:

.o files can contain unresolved symbols that will be resolved by the linker (the linker needn't actually bring in the code for them, it could just put the location of the code). ELF can be relocatable or executable. Compile code with -fpic to make a shared object. This object can be used multiple programs at the same time during runtime. Shared objects must be present at compile time and run time. Shared libraries must be present at compile time only (as they will be merged into the executable)

Inline functions cannot be dynamically linked.

Readelf can get details about them. Objdump can show symbol table and stuff, so can nm.

Segments in memory (of a program) can be given rwx perms.

For an array, arr == &arr == &arr[0]. They also decay into pointers when passed into functions (even if the func param specifies an array! Therefore the "array" in the function is just a veil for a simple pointer, since it is a pointer it has an LVALUE) therefore arr == &arr[0] but != &arr inside the function. While declaring multi-dimensional array parameters, only one size (and only the left-most) can be left empty, all the others need to be numbers.

Funcs with variadic args are declared like so: f(a, b, ...) . Const char *p is the same as char const *p. Promotion to const is peaceful. Demotion from const is not possible (even with pointer magic) unless using const_cast

Void fun(int &a, const int &b) {}

Int g = 2;

Fun(3, g); // not ok, 3 is only an RVALUE, references need an LVALUE.

Fun(g, 3); // this is okay, const references can be init with RVALUES (the compiler will create an anonymous temp var)

Pointer to func (e.g.): double(*)()

## Storage:

Automatic are allocated on function stack frame

Static are allocated on data segment (and so are global variables)

Dynamic is allocated on the heap using new operator

Memory: (from 0x000 to 0xffff ...) represented as segments

Null page | Data (initialized stuff) | BSS (non-init stuff, OS typically fills this with Os before the program runs) | Heap |

Memory Map Stuff (place where shared libraries are stored e.g. libc) | Stack

The null page denies all read / write / exec permissions. That's why when you try to access it (with *0) you get a signal of segmentation violation (SIGSEGV).

Translation unit includes everything that's compiled when you run gcc -c (i.e. the source code and the header files that it includes) it's also known as compilation unit.

## Resource Acquisition is Initialization

What to do with resources? Acquire, release, manage custody (e.g. copy, move)

With RAII bind resource to object lifetime. It encapsulates stuff, promotes locality and ensures exception safety. How exception safety? While doing anything which could leave the object in a broken state by leaving dangling pointers / double free or other resource mismanagement, do the stuff that might throw exception and make sure it is successful before modifying resources (which theoretically should be code that doesn't throw exceptions).

References couldn't be made to Rvalues since they don't have names. Rvalues are values that don't have an identifiable location in memory and something whose address you can't get. There's a concept of rvalue reference. For that you cannot initialize it with an lvalue. You could get an rvalue reference to an lvalue using std::move. You can also get const lvalue references to rvalues. Rvalue ref is denoted by && e.g. int &&rvalref = 3; This is used to denote move constructors and move assignment operators. Move operators are mainly defined for efficiency. (It's okay to steal resources off of a temp object as it will be destroyed immediately anyways). Make sure you leave it in a state that's safe to be destroyed. If you used the std::move op to get an rvalue ref of an lvalue, it may have more lifetime (depends on the lvalue), make sure it's not used by other code!

If it has name, it is Lvalue. That's why you can have an lvalue which is of type rvalue reference. The parameter of the move constructor and stuff is an Lvalue of type rvalue reference. That's why this code ends up calling a copy constructor instead of move:

## 
Virtual member functions are those in the base class that

When overridden by the derived class and

You have a base class pointer to a derived class object

And try to use that function, it will call the derived class one, not the base class one as expected.

That is because it supports dynamic dispatch.

Just one virtual function is enough for the compiler to put a pointer to a vtab in every object of that base class.

Note ethat these classes must also have virtual destructors because destructing someother object with your destructor could be dangerous when that object is of a different type.

Having only pure virtual functions makes a class "abstract" in that it cannot be instantiated. A pure virtual function is one which is marked with = 0 at the end (similary to how = delete is used to delete a member function). It could have a definition in case the derived class would ever want to call it.

## Functional:

Functors (aka function objects) are objects that can be called. They help functions have state.

Lambdas create function objects: [l][>reType[] // the return type and params can be omitted if not needed. If putting a return type then params brackets are required

CAPUTRS: By default, lambdas have nothing from outer scopes visible inside them. If values from outside are required to be accessed it can be done through captures. Value or reference captures. e.g. [=] captures everything from outer scope that's being used in the lambda body by value, [&] by reference. [a, b, &] captures only a and b by value, rest by ref. [=, &f] captures everything by val except f by ref.

These things also need to capture `this` explicitly if they're passed as an arg to class functions and called there and want to use stuff from the class.

Std::function is a wrapper to hold all sort of function-like-things: functions, function pointers, lambdas, functors etc. The signature of the thing needs to be specified e.g. std::function<void(int, float*)> talks about a function that returns void and takes in an int, float *.

Std::bind can bind values to certain params of functions, e.g. std::bind(funk, 83.6) will bind the first param of funk to 83.6. The function that's returned by bind now has a shorter list of params. Sort of like partials in python. If you want to bind certain params use _1, _2 ... _n to specify which one (from the std::placeholders namespace).

## Inheritance:

Private members of the base class are inaccessible to the derived class no matter what, for public and protected it depends on the way the derived class inherited the base (public / private / protected). Private inheritance is the default if none specified (public if the base is a struct). The base class part of the derived class must be initialized by the base class constructor (it will call no-args of the base if available, if not available the base one must be explicitly called in the initialization list). The base class cons is called after the rest of the init list but before the derived constructor body and compsed objects.

In a member function (or even on the object, using the . Syntax) just like how members of the current class can be accessed with Klass::attrname (useful when there's a local or something with the same name hiding it) the base one can be accessed with BassName::attrname (applies to funcs too). Useful doing stuff like calling the base assignment op within the derived assignment op function e.g. BasS::operator=(other); ... do derived specific stuff ...

You cannot delete inherited members.

Hiding: happens when a derived class defines a func with the same name as based class ones (all the base stuff are hidden when the signatures are diff from the derived one). If the ones in the base with diff sign but same name are to be available in the derived (overloading) then you need to add a "using BaesName::funcName;" statement in the derived class definition. If the signatures are same then it's an ambiguity and an error! That syntax is called "lifting".

Lifting can also be done with constructors so that the derived class can be constructed with the base one (could be risky! Make sure ALL the derived members are init even if the base ctor doesn't init some of them). If the base class is a template class then the derived must also be templated or specify types for the base while inheriting. e.g., Template

Class derived : std::vector
{};

Or even

Class deriz : std::vector<int> {};</int>

Can also do this!

Template

Class deriwhat : X {}; // it doesn't know what it's deriving from yet !

Then there's this o.o

template <class class="" t,="" template=""> class U&gt;</class>

class H : public U
{};

How does that even work !

Polymorphism:

There's no way to convert a base class object to a derived class object because the derived can have extra info.

Converting derived to base is natural because they are supposed to be a type-of base. While calling a function on an object, by default, C++ figures out which function to call during compile time. This may not be right when you have a derived object converted to a base class object and a method called on it. It would call the method from the base class. The member selection could happen at run time with dynamic binding. The function is declared virtual in the base class and then overridden by the derived class. (To be very correct, the function in the derived class should have the virtual keyword in front of it too). The signature must be the same!

Type is just a compile time concept? Well...

Run Time Type Information is available on some classes (i.e., those that have a virtual function declared in the base class, because RTTI requires the ytbl, which holds a type_info object which tells us about the type). This can be used by dynamic_cast<castToType*>(ptr_to_obj) which will return nullptr if the object cannot be used as a castToType object or just the ptr_to_obj if it can. This could be useful if the argument ptr is of a base class type but the actual object it was pointing to was somewhere deown the inheritance tree and the castToType is on that path. It allows safe "down-casting" basically. That's just a demo though, dynamic_cast can be used to check for upwards movement too in the tree.

The actual type_info associated with a value can be got with typeid(expression / type), use .name() to get a string repr of the type. You can also compare the results of typeid if you wanna match stuff. It's an operator like sizeof.

Converting derived to baes only works with ptrs and refs, if you do by value then the derived class object is "sliced" to a base class object.

'override' can be specified after a func declaration in the derived class so it is a compiler error if it doesn't actually override and just hides the base func. 'final' can be specified on the base func to make sure it cannot be overridden in the children.


## Multiple inheritance:

It can cause the diamond problem where a derived object has two copies of data from its base classes which had derived from a common base class at some point in the hierarchy. Not only is that wasteful, it could also lead to ambiguous code which can lead to compilers giving errors. There are actually two copies of each member and you require scope resolution to access and differentiate between them. Converting the final derived into the top base is also ambiguous and won't happen unless you cast into one of the intermediate first. The same applies for member functions in the top base, in the derived one it won't know which one to use without a scope specifier / cast.

What if you don't want two copies? In the two classes that inherit from the common base, inherit from the base like so: class Dclass : virtual <pub/priv/prot> Bclass {}

All the mischievous bases need to do it, else there will be multiple copies in the final derived.

And then there'll be only one cope in the final derived. This is actually done through the vtab so the final one has 16 bytes more mem.

Anyways, it's not a good idea in most cases so don't do it (when two base class ctoris init the same data, is that what you want?). If doing multiple inheritance it would make most sense when the base classes are abstract with only pure virtual functions (as a means of implementing the interfaces concept).

Construction of base classes happen in the order they are derived in the derivation list.

The scope resultion operator is also used with objects when it's required to specify which base class's member it is referring to: obkt.Bass::memb;

What if you WANT the compiler to generate one of those 5 methods but you maybe wanna add stuff like a virtual qualifier? Ez, you do `virtual "Klass() = default;`

## Namespace:

There might be name conflicts in the global scope in large programs / libraries when they end up using common names. To avoid this, there's the concept of namespace which lets you create a named scope. The global scope is also a global namespace but one without any name, to access stuff from it you do ::stuffName or just leave out the `::`. A namespace can be defined in multiple files, they are all brought together to make one namespace with that name during compilation. Use case? In header files the declaration can be put in namespace, in the cpp file the declaration can be put in the same namespace. This is how the stuff from standard library all goes into std.

Doing a `using namespace stuffname` or `using namespace stuffname::lolz` will bring everything from that namespace or only the lolz thingy respectively, into the current scope. Namespaces can be nested and also aliased. An unnamed namespace limits visibility of its members to the current namespace (should be used as alternative to static when used to limit name to file scope).

Since the friend operator funcs are deeply connected to the class they're doing the operatoion for, it's considered good practice to put them in the same namespace.

Argument dependent lookup (by koenig)

When a function is called with an arg from a namespace, the compiler won't just look for the function in the current scope, it will look for the function even in the namespace that the arg came from (even if the code doesn't ask it do so). If there are ambiguities on which to use, the compiler will error out. That's how the std::operator<<(ostream &, T) operator works when called on cout even though you didn't do std::<<.

## Exceptions!

Any piece of code can `throw` an exception(just an object, could be of any class). That exception can be caught with a `catch` (which is always associated with a `try` block which says that "an exception could accu here"). If the exception isn't caught by the user, std::terminate will catch it (and usually end up terminating the program).

Note that exception handling causes stack unrolling and destruction of objects in those frames so it's a pretty slow process.

There can be multiple catch statements with a try. They can each catch an object of a class they specify (there's also catch(...) which can catch all exceptions but it can't get the associated object). After an exception is caught, the catch also has a catch block in which you can do stuff with the exception object or just general error handling.

There's no `finally` because that's usually used to clean up resources and C++ relies on RAIL to do that.

Fun fact, in the catch block there's an option to throw the caught exception + the associated object with just `throw;`. Also, a catch for a bees class can catch derived class objects too. Finally, it's good to throw values, not pointer (who'll take care of custody?) and catch references.

Note that the try block and catch blocks are different scopes.

There's function try blocks (esoteric concept) for when you want to catch errors in the constructor initialization list and so on.

A function can say that doesn't throw an exception by having `noexcept` in its declaration. e.g., void foo() noexcept; The compiler doesn't actually check whether it's true or not, but if this function then throws during run-time then it's direct std::terminate for the program.

Writing exception safe code is hard. Designers must take it into consideration while building stuff. Whatever you do, make sure that the program is left in a safe state even if exception happens.

## Pointers
An object's identity in C++ is the memory address of the object.

A pointer to member is an OFFSET of a member in a CLASS (not object). They can be coupled with pointers to objects to get access to specific members at run time. Why? They provide a way for the client to access some member without really knowing which one (abstraction?).

What's the syntax for this stuff? It's somewhat weird.

You declare one like so: data_type KlassNmae::*variable_name; // just add the brackets and stuff for function pointers
Assign it a value like this: variable_name = @KlassNmae::member_name;

Assume you have an object: KlassNmae objkt, *ptr_to_objkt;

You would access the member of the object like so: objkt.*variable_name, ptr_to_objkt->*variable_name
That's what the .* was used for!

A member pointer of a base class can be used on a derived class object too.

SMART POINTERS :)

Problem with regular pointers is that you might have a danglign one, could leak resources, not know who has custody of it, and if it is a pointer to an object or array of objects.

The smart pointers are objects. Their classes have overloaded the -> and * operators (the unary versions of it, not the binary). While calling smrt_ptr->member, first the unary -> is appliead nad the binary.

The types are:

Std::unique_ptr - maintains exclusive ownership of pointee. Doesn't have any copy operations, only move.

The best way to create one would be to use std::make_unique. e.g. std::unique_ptr<float> = std::make_unique<float>(3.14169);</float></float>

Std::shared_ptr - reference counted shared pointer. The pointee's lifetime is managed by 1 or more owners. Ownership is shared through copy ops and it is transferred by move ops.

This type of smart_pointer takes up more space though, must have atomic ops and needs to do reference counts and stuff. These can be created by std::make_shared

It's possible to create a shared_ptr from a unique_ptr (using std::move ofc)

Std::weak_ptr - non-ownership reference to an object (that is managed by shared_ptr). You can't access the object directly, you need to create a shared_ptr from the weak_ptr to do so (it will return a null shared_ptr if the object has been destroyed by the owning shared_ptr). This is useful when you want to access an object only if it exists.

If possible, don't even bother with pointers and just use value semantics. Move ops should take care of inefficiencies caused by temporary objects.

## Algorithm:

There are parallel version of many of the functions in algorithm, you can define what execution policy they should follow. Programmer must maintain thread safety.

Std::span is a container that can hold an array plus it's size. It's a better way to pass an array to a func, rather than passing the size as a separate param.

Want a singly linked list instead of the doubly one that std::list makes use of? Use std::forward_list

Istream_iterator - a fun way to read from an Istream object (like cln) using operator++, operator* justs returns a copy of the latest read object

Ostream_iterator - a fun way to write to an ostream object (like cout) using operator=, operator++ is a no-op .