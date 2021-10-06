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
        - [JVM Useful Links](#jvm-useful-links)

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

[up](#knowledge-notes)

***

# Java

***

## JVM

---

### JVM Architecture

JVM is only a specification, and its implementation is different from vendor to vendor.

![img_jvm_architecture](res/images/jvm_architecture.png)

> #### 1) Class Loader Subsystem
>
> The JVM resides on the RAM. During execution, using the Class Loader subsystem, the class files are brought on to the RAM. This is called Java’s dynamic class loading functionality. It loads, links, and initializes the class file (.class) when it refers to a class for the first time at runtime (not compile time).
>
> > **1.1) Loading**
> >
> > Loading compiled classes (.class files) into memory is the major task of Class Loader. Usually, the class loading process starts from loading the main class (i.e. class with static main() method declaration). All the subsequent class loading attempts are done according to the class references in the already-running classes
>
> > **1.2) Linking**
> >
> > Linking involves in verifying and preparing a loaded class or interface, its direct superclasses and superinterfaces, and its element type as necessary, while following the below properties.
>
> > **1.3) Initialization**
> >
> > Here, the initialization logic of each loaded class or interface will be executed (e.g. calling the constructor of a class). This is the final phase of class loading where all the static variables are assigned with their original values defined in the code and the static block will be executed (if any). This is executed line by line from top to bottom in a class and from parent to child in class hierarchy.

> #### 2) Runtime Data Area
>
> Runtime Data Areas are the memory areas assigned when the JVM program runs on the OS. In addition to reading .class files, the Class Loader subsystem generates corresponding binary data and save the information (*fully qualified name of the loaded class, .class file is related to a Class/Interface/Enum Modifiers, static variables, and method etc.*) in the Method area for each class separately.
>
> Then, for every loaded .class file, it creates exactly one object of Class to represent the file in the Heap memory as defined in java.lang package. This Class object can be used to read class level information (class name, parent name, methods, variable information, static variables etc.) later in our code.
>
> > **2.1) Method Area**
> >
> > This is a shared resource (only 1 method area per JVM). Is not thread safe.
> >
> > Method area stores class level data (including static variables) such as:
> >
> > - **Classloader reference**.
> > - **Run time constant pool** — Numeric constants, field references, method references, attributes.
> > - **Field data** — Per field: name, type, modifiers, attributes.
> > - **Method data** — Per method: name, return type, parameter types (in order), modifiers, attributes.
> > - **Method code** — Per method: bytecodes, operand stack size, local variable size, local variable table, exception table.
>
> > **2.2) Heap area**
> >
> > This is a shared resource (only 1 heap area per JVM). Is not thread safe.
> >
> > Information of all objects and their corresponding instance variables and arrays are stored in the Heap area. Heap area is a great target for GC.
>
> > **2.3) Stack Area**
> >
> > This is not a shared resource and is thread safe.
> >
> > For every JVM thread, when the thread starts, a separate runtime stack gets created in order to store method calls. For every such method call, one entry will be created and added (pushed) into the top of runtime stack and such entry is called a _Stack Frame_.
> >
> > The size of stack frame is fixed according to the method. The frame is removed (popped) when the method returns normally or if an uncaught exception is thrown during the method invocation.
> > After a thread terminates, its stack frame will also be destroyed by JVM.
> >
> > A Stack Frame is divided into three sub-entities:
> >
> > - **Local Variable Array** — Variable from index 0 is the reference of a class instance where the method belongs. From 1, the parameters sent to the method are saved.
> > - **Operand Stack** — Each method exchanges data between the Operand stack and the local variable array, and pushes or pops other method invoke results.
> > - **Frame Data** — All symbols related to the method are stored here.
>
> > **2.4) Program Counter Registers**
> >
> > For each JVM thread, when the thread starts, a separate PC Register gets created in order to hold the address of currently-executing instruction (memory address in the Method area).
>
> > **2.5) Native Method Stack**
> >
> > There is a direct mapping between a Java thread and a native operating system thread. A separate native stack also gets created in order to store native method information (often written in C/C++) invoked through JNI (Java Native Interface).

> #### 3) Execution Engine
>
> The actual execution of the bytecode occurs here. Execution Engine executes the instructions in the bytecode line-by-line by reading the data assigned to above runtime data areas.
>
> > **3.1) Interpreter**
> >
> > The interpreter interprets the bytecode and executes the instructions one-by-one.
>
> > **3.2) Just-In-Time (JIT) Compiler**
> >
> > First, it compiles the entire bytecode to native code (machine code). Then for repeated method calls, it directly provides the native code. The native code is stored in the cache, thus the compiled code can be executed quicker.
>
> > **3.3) Garbage Collector (GC)**
> >
> > As long as an object is being referenced, the JVM considers it alive. Once an object is no longer referenced and therefore is not reachable by the application code, the garbage collector removes it and reclaims the unused memory. We can trigger it by calling System.gc() method (But the execution is not guaranteed.).

> #### 4) Java Native Interface (JNI)
>
> This interface is used to interact with Native Method Libraries. This enables JVM to call C/C++ libraries and to be called by C/C++ libraries which may be specific to hardware.

> #### 5) Native Method Libraries
>
> This is a collection of C/C++ Native Libraries which is required for the Execution Engine and can be accessed through the provided Native Interface.

[up](#knowledge-notes)

***

### JVM Memory Model

![jvm_native_memory](res/images/jvm-native-memory.png)

![jvm_stack_non_heap_heap](res/images/jvm-stack-non-heap-heap.png)

#### 1) Heap Memory

It is a larger region of RAM which is used for dynamic memory allocation. All Java objects are stored in the heap and
the scope of the objects is the whole application. The memory management is managed by us in heap, but the unused
objects are cleared by the Garbage collector automatically.

![jvm_heap_memory](res/images/jvm-heap-memory.png)

> **1.1) Young Generation**
>
> This is reserved for containing newly-allocated objects Young Gen includes three parts.
>
> > - **Eden Memory**
> >
> > Most of the newly-created objects goes Eden space. When Eden space is filled with objects, Minor GC (a.k.a. **Young Collection**) is performed and all the survivor objects are moved to one of the survivor spaces.
>
> > - **Survivor Spaces**
> >
> > Minor GC also checks the survivor objects and move them to the other survivor space. So at a time, one of the survivor space is always empty. Objects that are survived after many cycles of GC, are moved to the Old generation memory space. Usually it’s done by setting a threshold for the age of the young generation objects before they become eligible to promote to Old generation.
> >
> > > - **S0 Survivor Space**
> >
> > > - **S1 Survivor Space**

> **1.2) Old Generation**
>
> This is reserved for containing long lived objects that could survive after many rounds of Minor GC. When Old Gen space
> is full, Major GC (a.k.a. **Old Collection**) is performed (usually takes longer time).

#### 2) Stack

This store's local variables and the partial results. Each thread has its own runtime stack created when the thread is
created. A new frame is created whenever a method is invoked and all the local variables of that method is stored in
that corresponding frame. This is deleted when the method invocation is completed. This is not a shared resource.

#### 3) Meta Space

This is part of the native memory and doesn't have an upper limit by default. This is what used to be **Permanent
Generation (PermGen) Space** in earlier versions of JVM. This space is used by the class loaders to store class
definitions. If this space keeps growing, the OS might move data stored here from RAM to virtual memory which might slow
down the application.

#### 4) Code Cache

This is where the Just In Time(JIT) compiler stores compiled code blocks that are often accessed. Generally, JVM has to
interpret byte code to native machine code whereas JIT-compiled code need not be interpreted as it is already in native
format and is cached here.

#### 5) Shared Libraries

This is where native code for any shared libraries used are stored. This is loaded only once per process by the OS.

---

### JVM Stack vs Heap

![jvm_stack_vs_heap](res/images/jvm-stack-vs-heap.png)

[up](#knowledge-notes)

***

### JVM useful links:

* [The Structure of the Java Virtual Machine](https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-2.html)
* [Стек и куча в Java](https://topjava.ru/blog/stack-and-heap-in-java)
* [Understanding JVM Architecture](https://medium.com/platform-engineer/understanding-jvm-architecture-22c0ddf09722)
* [Understanding Java Memory Model](https://medium.com/platform-engineer/understanding-java-memory-model-1d0863f6d973)
* [Understanding Java Garbage Collection](https://medium.com/platform-engineer/understanding-java-garbage-collection-54fc9230659a)
* [JVM Memory Model](https://amanagrawal9999.medium.com/jvm-memory-model-70821e84af4b)
* [Java Memory Explained](https://medium.com/nerd-for-tech/java-memory-explained-43de6de157be)
* [Visualizing memory management in JVM](https://medium.com/@deepu105/visualizing-memory-management-in-jvm-java-kotlin-scala-groovy-clojure-4fbcc0929482)

[up](#knowledge-notes)

***