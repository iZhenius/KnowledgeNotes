# Knowledge Notes

- [Git](#git)
    - [Git Initial](#git-initial)
- [Java](#java)
    - Basics
    - Collections
    - Concurrency
    - [JVM](#jvm)
        - [JVM Architecture](#jvm-architecture)
        - [JVM Memory Model](#jvm-memory-model)
            - [JVM Stack vs Heap](#jvm-stack-vs-heap)
        - [JVM Garbage Collector](#jvm-garbage-collector)
        - [JVM Java Reference Types](#jvm-java-reference-types)

***

# Git

## Git Initial

```
$ git config --global user.email "user@mail.com"
$ git config --global user.name "User Name"
$ git config --global init.defaultBranch main
$ git init
$ git add .
$ add .gitignore
$ git commit -m “Initial commit”
$ git remote add origin git@github.com:username/projectname.git
  (or git remote add origin https://github.com/username/projectname.git)
$ git push -u origin main
```

[^ up](#knowledge-notes)

***

# Java

## JVM

### JVM Architecture

JVM is only a specification, and its implementation is different from vendor to vendor.

![img_jvm_architecture](res/images/jvm_architecture.png)

### 1. Class Loader Subsystem

The JVM resides on the RAM. During execution, using the Class Loader subsystem, the class files are brought on to the
RAM. This is called Java’s dynamic class loading functionality. It loads, links, and initializes the class file (.class)
when it refers to a class for the first time at runtime (not compile time).

- #### Loading
  Loading compiled classes (.class files) into memory is the major task of Class Loader. Usually, the class loading
  process starts from loading the main class (i.e. class with static main() method declaration). All the subsequent
  class loading attempts are done according to the class references in the already-running classes

- #### Linking
  Linking involves in verifying and preparing a loaded class or interface, its direct superclasses and superinterfaces,
  and its element type as necessary, while following the below properties.

- #### Initialization
  Here, the initialization logic of each loaded class or interface will be executed (e.g. calling the constructor of a
  class). This is the final phase of class loading where all the static variables are assigned with their original
  values defined in the code and the static block will be executed (if any). This is executed line by line from top to
  bottom in a class and from parent to child in class hierarchy.

### 2. Runtime Data Area

Runtime Data Areas are the memory areas assigned when the JVM program runs on the OS. In addition to reading .class
files, the Class Loader subsystem generates corresponding binary data and save the information (*fully qualified name of
the loaded class, .class file is related to a Class/Interface/Enum Modifiers, static variables, and method etc.*) in the
Method area for each class separately. Then, for every loaded .class file, it creates exactly one object of Class to
represent the file in the Heap memory as defined in java.lang package. This Class object can be used to read class level
information (class name, parent name, methods, variable information, static variables etc.) later in our code.

- #### Method Area

  This is a shared resource (only 1 method area per JVM). Is not thread safe. Method area stores class level data (
  including static variables) such as:

    - **Classloader reference**.
    - **Run time constant pool** — Numeric constants, field references, method references, attributes.
    - **Field data** — Per field: name, type, modifiers, attributes.
    - **Method data** — Per method: name, return type, parameter types (in order), modifiers, attributes.
    - **Method code** — Per method: bytecodes, operand stack size, local variable size, local variable table, exception
      table.

- #### Heap area

  This is a shared resource (only 1 heap area per JVM). Is not thread safe. Information of all objects and their
  corresponding instance variables and arrays are stored in the Heap area. Heap area is a great target for Garbage
  Collection.

- #### Stack Area

  This is not a shared resource and is thread safe. For every JVM thread, when the thread starts, a separate runtime
  stack gets created in order to store method calls. For every such method call, one entry will be created and added (
  pushed) into the top of runtime stack and such entry is called a _Stack Frame_. The size of stack frame is fixed
  according to the method. The frame is removed (popped) when the method returns normally or if an uncaught exception is
  thrown during the method invocation. After a thread terminates, its stack frame will also be destroyed by JVM.  
  A Stack Frame is divided into three sub-entities:

    - **Local Variable Array** — Variable from index 0 is the reference of a class instance where the method belongs.
      From 1, the parameters sent to the method are saved.
    - **Operand Stack** — Each method exchanges data between the Operand stack and the local variable array, and pushes
      or pops other method invoke results.
    - **Frame Data** — All symbols related to the method are stored here.


- **Program Counter Registers**  
  For each JVM thread, when the thread starts, a separate PC Register gets created in order to hold the address of
  currently-executing instruction (memory address in the Method area).


- **Native Method Stack**  
  There is a direct mapping between a Java thread and a native operating system thread. A separate native stack also
  gets created in order to store native method information (often written in C/C++) invoked through JNI (Java Native
  Interface).

### 3. Execution Engine

The actual execution of the bytecode occurs here. Execution Engine executes the instructions in the bytecode
line-by-line by reading the data assigned to above runtime data areas.

- **Interpreter**  
  It interprets the bytecode and executes the instructions one-by-one.


- **Just-In-Time (JIT) Compiler**  
  First, it compiles the entire bytecode to native code (machine code). Then for repeated method calls, it directly
  provides the native code. The native code is stored in the cache, thus the compiled code can be executed quicker.


- **Garbage Collector (GC)**  
  As long as an object is being referenced, the JVM considers it alive. Once an object is no longer referenced and
  therefore is not reachable by the application code, the garbage collector removes it and reclaims the unused memory.
  We can trigger it by calling System.gc() method (But the execution is not guaranteed.).

### 4. Java Native Interface (JNI)

This interface is used to interact with Native Method Libraries. This enables JVM to call C/C++ libraries and to be
called by C/C++ libraries which may be specific to hardware.

### 5. Native Method Libraries

This is a collection of C/C++ Native Libraries which is required for the Execution Engine and can be accessed through
the provided Native Interface.

### Useful links:

* [The Structure of the Java Virtual Machine](https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-2.html)
* [Understanding JVM Architecture](https://medium.com/platform-engineer/understanding-jvm-architecture-22c0ddf09722)

[^ up](#knowledge-notes)

***

### JVM Memory Model

![jvm_native_memory](res/images/jvm-native-memory.png)

![jvm_stack_non_heap_heap](res/images/jvm-stack-non-heap-heap.png)

### 1. Heap Memory

It is a larger region of RAM which is used for dynamic memory allocation. All Java objects are stored in the heap and
the scope of the objects is the whole application. The memory management is managed by us in heap, but the unused
objects are cleared by the Garbage collector automatically.

![jvm_heap_memory](res/images/jvm-heap-memory.png)

- #### Young Generation
  This is reserved for containing newly-allocated objects Young Gen includes three parts.

    - **Eden Memory** — Most of the newly-created objects goes Eden space. When Eden space is filled with objects, Minor
      Garbage Collection (a.k.a. **Young Collection**) is performed and all the survivor objects are moved to one of the
      survivor spaces.

    - **Survivor Spaces** — Minor Garbage Collection also checks the survivor objects and move them to the other
      survivor space. So at a time, one of the survivor space is always empty. Objects that are survived after many
      cycles of Garbage Collection, are moved to the Old generation memory space. Usually it’s done by setting a
      threshold for the age of the young generation objects before they become eligible to promote to Old generation.
        - **S0 Survivor Space**
        - **S1 Survivor Space**


- #### Old Generation
  This is reserved for containing long-lived objects that could survive after many rounds of Minor Garbage Collection.
  When Old Gen space is full, Major Garbage Collection (a.k.a. **Old Collection**) is performed (usually takes longer
  time).

### 2. Stack

This store's local variables and the partial results. Each thread has its own runtime stack created when the thread is
created. A new frame is created whenever a method is invoked and all the local variables of that method is stored in
that corresponding frame. This is deleted when the method invocation is completed. This is not a shared resource.

### 3. Meta Space

This is part of the native memory and doesn't have an upper limit by default. This is what used to be **Permanent
Generation (PermGen) Space** in earlier versions of JVM. This space is used by the class loaders to store class
definitions. If this space keeps growing, the OS might move data stored here from RAM to virtual memory which might slow
down the application.

### 4. Code Cache

This is where the Just In Time(JIT) compiler stores compiled code blocks that are often accessed. Generally, JVM has to
interpret byte code to native machine code whereas JIT-compiled code need not be interpreted as it is already in native
format and is cached here.

### 5. Shared Libraries

This is where native code for any shared libraries used are stored. This is loaded only once per process by the OS.

### Useful links:

* [Understanding Java Memory Model](https://medium.com/platform-engineer/understanding-java-memory-model-1d0863f6d973)
* [JVM Memory Model](https://amanagrawal9999.medium.com/jvm-memory-model-70821e84af4b)
* [Java Memory Explained](https://medium.com/nerd-for-tech/java-memory-explained-43de6de157be)
* [Visualizing memory management in JVM](https://medium.com/@deepu105/visualizing-memory-management-in-jvm-java-kotlin-scala-groovy-clojure-4fbcc0929482)

[^ up](#knowledge-notes)

***

### JVM Stack vs Heap

![jvm_stack_vs_heap](res/images/jvm-stack-vs-heap.png)

### Useful links:

* [Understanding Java Memory Model](https://medium.com/platform-engineer/understanding-java-memory-model-1d0863f6d973)
* [Стек и куча в Java](https://topjava.ru/blog/stack-and-heap-in-java)

[^ up](#knowledge-notes)

---

### JVM Garbage Collector

Java Garbage Collection (GC) is the process of tracking the live objects while destroying unreferenced objects in the
Heap memory in order to reclaim space for future object allocation. Java Garbage Collector runs as a Daemon Thread (i.e.
a low priority thread that runs in the background to provide services to user threads or perform JVM tasks).

### GC process includes:

* **Mark** — Identifying objects that are currently in use and not in use

* **Normal Deletion** — Removing the unused objects and reclaim the free space

* **Deletion with Compacting** — Moving all the survived objects to one survivor space (to increase the performance of
  allocation of memory to newer objects)

### GC roots:

* Local variables and input parameters of the currently executing methods
* Active Java threads
* Static fields of the loaded classes
* JNI references

### Eligibility cases for GC:

* **Nullifying the reference variable** — When a reference variable of an object are changed to NULL, the object becomes
  unreachable and eligible for GC.

* **Re-assigning the reference variable** — When a reference id of one object is referenced to a reference id of some
  other object, then the previous object will have no reference to it any longer. The object becomes unreachable and
  eligible for GC.

* **Object created inside the method** — When such a method is popped out from the Stack, all its members die and if
  some objects were created inside it, then these objects also become unreachable, thus eligible for GC.

* **Anonymous object** — An object becomes unreachable and eligible for GC when its reference id is not assigned to a
  variable.

* **Objects with only internal references (Island of Isolation)**

### Programmatically Calling GC (no guarantee to run)

* Using **System.gc()** method
* Using **Runtime.getRuntime().gc()** method

### GC Execution Strategies

### 1. Serial GC

Simple mark-sweep-compact approach with young and old gen garbage collections (a.k.a. Minor GC and Major GC). Suitable
for simple stand-alone client-machine applications running with low memory footprint and less CPU power.

![jvm_gc_serial](res/images/jvm-gc-serial.png)

### 2. Parallel GC

Parallel version of mark-sweep-compact approach for Minor GC with multiple threads (Major GC still happens with a single
thread in a serial manner).

![jvm_gc_parallel](res/images/jvm-gc-parallel.png)

### 3. Concurrent Mark Sweep (CMS) GC

Garbage collection normally happens with pauses (Major GC takes a long time), which makes it problematic for highly
responsive applications where we can’t afford long pause times. CMS Collector minimizes the impact of these pauses by
doing most of the garbage collection work (i.e. Major GC) concurrently within the application threads (Minor GC still
follows the usual parallel algorithm without any concurrent progress with application threads).

### 4. G1 GC

Garbage First (G1) Collector divides the Heap into multiple equal-sized regions and when GC is invoked, first collects
the region with lesser live data (young gen and old gen implementations don’t apply here). This collector is a parallel,
concurrent and incrementally compact low-pause garbage collector which intends to replace the CMS Collector.

![jvm_gc_g1](res/images/jvm-gc-g1.png)

### 5. Shenandoah GC

Compacting work concurrently within the application threads. Experimental until JDK 15.

### 6. Z GC

All GC processes concurrently within the application threads. Experimental until JDK 15.

### Useful links:

* [Understanding Java Garbage Collection](https://medium.com/platform-engineer/understanding-java-garbage-collection-54fc9230659a)
* [Сборка мусора в Java: что это такое и как работает в JVM](https://medium.com/nuances-of-programming/%D1%81%D0%B1%D0%BE%D1%80%D0%BA%D0%B0-%D0%BC%D1%83%D1%81%D0%BE%D1%80%D0%B0-%D0%B2-java-%D1%87%D1%82%D0%BE-%D1%8D%D1%82%D0%BE-%D1%82%D0%B0%D0%BA%D0%BE%D0%B5-%D0%B8-%D0%BA%D0%B0%D0%BA-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%D0%B5%D1%82-%D0%B2-jvm-25bb2570b44c)
* [Дюк, вынеси мусор! — Часть 1](https://habr.com/ru/post/269621/)
* [Дюк, вынеси мусор! — Часть 2](https://habr.com/ru/post/269707/)
* [Дюк, вынеси мусор! — Часть 3](https://habr.com/ru/post/269863/)

[^ up](#knowledge-notes)

---

### JVM Java Reference Types

* ### Strong reference

  Strong references are the ordinary references in Java. Anytime we create a new object, a strong reference is by
  default created.

* ### Soft Reference

  Soft reference objects, which are cleared at the discretion of the garbage collector in response to memory demand.
  Soft references are most often used to implement memory-sensitive caches. A **softly reachable object** has no strong
  references pointing to it. All soft references to softly-reachable objects **are guaranteed to have been cleared
  before a JVM throws an _OutOfMemoryError_**. Softly reachable objects will remain alive for some time after the last
  time they are referenced. The default value is a one second of lifetime per free megabyte in the heap. This value can
  be adjusted.

* ### Weak Reference

  A weakly referenced object is cleared by the Garbage Collector when it's weakly reachable. Weak reachability means
  that an object has neither strong nor soft references pointing to it. One of the best implementations of weak
  references is the **WeakHashMap**. Reference queue is optional.

* ### Phantom Reference

  These references are only garbage collected when none of the references namely strong references, soft references or
  weak references is being pointed to the object belonging to phantom references. They are always used **along with
  reference queue** and to keep a track of objects which have been finalized. **We can't get a referent of a phantom
  reference: `phantomReference.get()` returns `null`**. One of the use cases is **to determine when an object was
  removed from the memory** which helps to schedule memory-sensitive tasks. For example, we can wait for a large object
  to be removed before loading another one.

### Useful links:

* [Soft References in Java](https://www.baeldung.com/java-soft-references)
* [Weak References in Java](https://www.baeldung.com/java-weak-reference)
* [Phantom References in Java](https://www.baeldung.com/java-phantom-reference)
* [Weak Soft and Phantom references in Java and why they matter](https://medium.com/@ramtop/weak-soft-and-phantom-references-in-java-and-why-they-matter-c04bfc9dc792)
* [Finally understanding how references work in Android and Java](https://medium.com/google-developer-experts/finally-understanding-how-references-work-in-android-and-java-26a0d9c92f83)
* [An overview of Strong, Weak, Soft and Phantom references in Java](https://prateeknima.medium.com/strong-weak-soft-and-phantom-references-in-java-b4f9068e883e)
* [Особенности PhantomReference](https://javarush.ru/groups/posts/2291-osobennosti-phantomreference)
* [Мягкие ссылки на страже доступной памяти или как экономить память правильно](https://habr.com/ru/post/169883/)

[^ up](#knowledge-notes)

***