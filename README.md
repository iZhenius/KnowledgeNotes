# Knowledge Notes

- [Git](#git)
    - [Git Initial](#git-initial)
- [Java](#java)
    - [Basics](#basics)
        - [Class Object](#class-object)
        - [String pool](#string-pool)
        - [Generics](#java-generics)
    - Collections
    - [Concurrency](#concurrency)
        - [Thread](#java-thread)
        - [Lock vs Monitor](#lock-vs-monitor)
        - [Synchronized vs Volatile vs Atomic](#synchronized-vs-volatile-vs-atomic)
        - [Executors](#executors)
        - Synchronizers
        - Concurrent Collections
    - [JVM](#jvm)
        - [JVM Architecture](#jvm-architecture)
        - [JVM Memory Model](#jvm-memory-model)
            - [Stack vs Heap](#stack-vs-heap)
        - [Garbage Collector](#garbage-collector)
        - [Java Reference Types](#java-reference-types)
- [Kotlin](#kotlin)
    - [Kotlin Syntax Features](#kotlin-syntax-features)
        - [Scope functions](#kotlin-scope-functions)
    - [Kotlin Generics](#kotlin-generics)
- [Android](#android)
    - [Multithreading](#multithreading)

***

# Git

# Git Initial

```shell
$ git config --global user.email "user@mail.com"
$ git config --global user.name "User Name"
$ git config --global init.defaultBranch main
$ git init
$ git add .
$ git add .gitignore
$ git commit -m "Initial commit"
$ git remote add origin git@github.com:username/projectname.git
  (or git remote add origin https://github.com/username/projectname.git)
$ git push -u origin main
```

[^ up](#knowledge-notes)

***

# Java

# Basics

## Class Object

```java
public class Object {

    /**
     * Returns the runtime class of this Object.
     * The returned Class object is the object that is locked
     * by static synchronized methods of the represented class.
     * The actual result type is Class<? extends |X|> where |X|
     * is the erasure of the static type of the expression on which getClass is called.
     */
    public final native Class<?> getClass();

    /**
     * Returns a hash code value for the object.
     * The general contract of hashCode() is:
     * - Whenever it is invoked on the same object more than once,
     *   the hashCode method must return the same integer, provided
     *   no information is modified.
     * - If two objects are equal according to the equals(Object) method,
     *   then calling the hashCode method must produce the same integer result.
     * - It is not required that if two objects are unequal according to
     *   the equals(Object) method, then calling the hashCode method must
     *   produce distinct integer results.
     * Returns: a hash code value for this object.
     */
    public native int hashCode();

    /**
     * Indicates whether some other object is "equal to" this one.
     * The equals(Object) method implements an equivalence relation
     * on NON-NULL object references:
     * - x.equals(x) should return true.
     * - x.equals(y) should return true if y.equals(x) returns true.
     * - If x.equals(y) returns true and y.equals(z) returns true,
     *   then x.equals(z) should return true.
     * - Multiple invocations of x.equals(y) return true (or false),
     *   provided no information is modified.
     * - x.equals(null) should return false.
     * Params: obj – the reference object with which to compare.
     * Returns: true if this object is the same as the obj argument; false otherwise.
     */
    public boolean equals(Object obj) {
        return (this == obj);
    }

    /**
     * Creates and returns a copy of this object. The precise meaning
     * of "copy" may depend on the class of the object.
     * Returns: a clone of this instance.
     * Throws:  CloneNotSupportedException – if the object's class
     *          does not support the Cloneable interface.
     */
    protected native Object clone() throws CloneNotSupportedException;

    /**
     * Returns a string representation of the object. In general,
     * the toString method returns a string that "textually represents" this object.
     * It is recommended that all subclasses override this method.
     * Returns: a string representation of the object.
     */
    public String toString() {
        return getClass().getName() + "@" + Integer.toHexString(hashCode());
    }

    /**
     * Wakes up a single thread that is waiting on this object's monitor.
     * If any threads are waiting on this object, one of them is chosen
     * to be awakened. The choice is arbitrary and occurs at the discretion
     * of the implementation.
     * This method should only be called by a thread that is the owner of
     * this object's monitor.
     * Only one thread at a time can own an object's monitor.
     * Throws:  IllegalMonitorStateException – if the current thread
     *          is not the owner of this object's monitor.
     */
    public final native void notify();

    /**
     * Wakes up all threads that are waiting on this object's monitor.
     * A thread waits on an object's monitor by calling one of the wait methods.
     * This method should only be called by a thread that is the owner of
     * this object's monitor.
     * Throws:  IllegalMonitorStateException – if the current thread
     *          is not the owner of this object's monitor.
     */
    public final native void notifyAll();

    /**
     * Causes the current thread to wait until it is awakened,
     * typically by being notified or interrupted.
     * Throws:  IllegalMonitorStateException – if the current thread
     *          is not the owner of the object's monitor
     *          InterruptedException – if any thread interrupted the current thread
     *          before or while the current thread was waiting.
     *          The interrupted status of the current thread is cleared
     *          when this exception is thrown.
     */
    public final void wait() throws InterruptedException {
        wait(0L);
    }

    /**
     * Causes the current thread to wait until it is awakened,
     * typically by being notified or interrupted, or until
     * a certain amount of real time has elapsed.
     * Params:  timeoutMillis – the maximum time to wait, in milliseconds
     * Throws:  IllegalArgumentException – if timeoutMillis is negative
     *          IllegalMonitorStateException – if the current thread
     *          is not the owner of the object's monitor
     *          InterruptedException – if any thread interrupted the current thread
     *          before or while the current thread was waiting.
     *          The interrupted status of the current thread is cleared
     *          when this exception is thrown.
     */
    public final native void wait(long timeoutMillis) throws InterruptedException;

    /**
     * Causes the current thread to wait until it is awakened,
     * typically by being notified or interrupted, or until
     * a certain amount of real time has elapsed.
     * The current thread must own this object's monitor lock.
     * Params:  timeoutMillis – the maximum time to wait, in milliseconds
     *          nanos – additional time, in nanoseconds, in the range 0-999999 inclusive
     * Throws:  IllegalArgumentException – if timeoutMillis is negative,
     *          or if the value of nanos is out of range
     *          IllegalMonitorStateException – if the current thread
     *          is not the owner of the object's monitor
     *          InterruptedException – if any thread interrupted
     *          the current thread before or while the current thread was waiting.
     *          The interrupted status of the current thread
     *          is cleared when this exception is thrown.
     * API Note:    The recommended approach to waiting is to check
     *              the condition being awaited in a while loop around
     *              the call to wait, as shown in the example below.
     *              Among other things, this approach avoids problems
     *             that can be caused by spurious wakeups.
     */
    public final void wait(long timeoutMillis, int nanos) throws InterruptedException {
        if (timeoutMillis < 0) {
            throw new IllegalArgumentException("timeoutMillis value is negative");
        }

        if (nanos < 0 || nanos > 999999) {
            throw new IllegalArgumentException(
                    "nanosecond timeout value out of range");
        }

        if (nanos > 0) {
            timeoutMillis++;
        }

        wait(timeoutMillis);
    }

    /**
     * Called by the garbage collector on an object when garbage collection
     * determines that there are no more references to the object.
     * A subclass overrides the finalize method to dispose of system
     * resources or to perform other cleanup.
     * The finalize method of class Object performs no special action;
     * it simply returns normally. Subclasses of Object may override this definition.
     * The finalize method is never invoked more than once by a Java
     * virtual machine for any given object.
     * Deprecated   The finalization mechanism is inherently problematic.
     *              Finalization can lead to performance issues, deadlocks, and hangs.
     *              Errors in finalizers can lead to resource leaks;
     *              there is no way to cancel finalization if it is no longer necessary;
     *              and no ordering is specified among calls to finalize
     *              methods of different objects.
     * Throws:      Throwable – the Exception raised by this method
     */
    @Deprecated(since = "9")
    protected void finalize() throws Throwable {
    }
}
```

## ///// References (online):

* []()

[^ up](#knowledge-notes)

---

## String Pool

String Pool is a place in the Heap memory of Java to store string literal. To decrease the number of String objects
created in the JVM, the String class keeps a pool of strings.

Why strings are immutable:

* Use of String Constant Pool (caching the String literals)
* Security
* Thread-Safety
* Cacheable HashCode
* Improved Performance

String objects are created in two ways:

1. ### Using double quotes(" "):

   ```java
   class StringPoolExample {
    String stringLiteral = "String literal";
   }
   ```

   The above statement first searches for the string `“String literal”` in the string pool, if found it just gives it a
   reference `stringLiteral` from string pool. If not found it creates a string object and places it in the string pool
   and then gives it a reference `stringLiteral`.

   > In this case, **only one object is created**: in the `string pool`.

2. ### Using the 'new' keyword:

   ```java
   class StringPoolExample {
    String newString = new String("New string");
   }
   ```

   The above statement creates a string object in heap memory, returns a reference from heap memory and checks whether
   it is present in the string pool or not. If the string “New string” is not present in the string pool then it will
   place this string in the string pool (JVM will point to that string object) else it will skip it.

   > _Case 1:_ **Two objects are created**: one in the `heap memory` and the other in the `string pool`.
   >
   > _Case 2:_ **Only one object is created**: in the `heap memory`.

### String.intern()

When the `String.intern()` method is invoked, if the `string pool` already contains a string equal to this String
object (the object with which intern method is being called) , then the string from the `string pool` is returned.
Otherwise, this String object is added to the `string pool`, and a reference to this String object in `string pool` is
returned.

### ///// References (online):

* [Concept of String Pool And String Object Creation In Java](https://medium.com/nerd-for-tech/concept-of-string-pool-and-string-object-creation-in-java-27ed2b3089f5)
* [String Pool in Java? Number of Objects Created When ‘Literals’ or ‘New’ Used?](https://medium.com/javarevisited/what-does-string-pool-mean-in-java-414c725fbd59)

[^ up](#knowledge-notes)

---

## Java Generics

Generics were introduced in JDK 5.0 to provide **compile-time type checking** and to eliminate the risk of
`ClassCastException` that was common when working with collection classes earlier.

A programmer can restrict a collection class to contain only one type of object using **Generics**.

Generic code ensures **type safety**. The compiler uses **type-erasure** to remove all type parameters at the
compile-time to reduce the overload at runtime.

```java
public interface List<E> {
    void add(E x);

    Iterator<E> iterator();
}

public interface Iterator<E> {
    E next();

    boolean hasNext();
}

public interface IntegerList {
    void add(Integer x);

    Iterator<Integer> iterator();
}
```

The parameters in the angle brackets in the front of `List` and `Iterator` interfaces (`<E>`) are the declarations of
the **Formal Type Parameters** of the interfaces `List` and `Iterator`.

When a generic declaration is invoked, the **Actual Type Arguments** are substituted for the **Formal Type Parameters**.

The **Formal Type Parameter** (`E`) in this case is an **Unbounded Type Parameter** and can take any values
like `Integer`, `Long`, `Double`, `String` or **any custom class name**.

### Type Safety

It’s just a guarantee by compiler that if correct Types are used in correct places then there should not be any
`ClassCastException` in runtime.

### Type Erasure

It essentially means that all the extra information added using generics into source code will be removed from bytecode
generated from it. Inside bytecode, it will be old java syntax which you will get if you don’t use generics at all. This
necessarily helps in generating and executing code written prior to Java 5.0 when generics were not added in language.

### Java Generic Type naming conventions:

- **E** — Element (used extensively by the Java Collections Framework, for example, `ArrayList`, `Set`, etc.)
- **K** — Key (Used in `Map`)
- **N** — Number
- **T** — Type
- **V** — Value (Used in `Map`)

### Invariant

First, generic types in Java are invariant, meaning that `List<String>` is **not a subtype of** `List<Object>`.
If `List` were not invariant, it would have been no better than `Array`, as the following code would have compiled but
caused an exception at runtime:

```java
public class GenericsExample {

    public static void main(String[] args) {
        List<String> strs = new ArrayList<String>();
        List<Object> objs = strs; // !!! A compile-time error here saves us from a runtime exception later.
        objs.add(1); // Put an Integer into a list of Strings
        String s = strs.get(0); // !!! ClassCastException: Cannot cast Integer to String
    }
}
```

### Bounded Type Parameters in Generics

When we restrict generic type declaration (`<E>`) to have certain classes that are called **Bounded Type Parameters**.

```java
public class Hardware<T extends Machine> {
}

class Machine {
}

class Laptop extends Machine {
}

class Mobile extends Machine {
}
```

In the above example, the **generic type of Hardware class** can be a **Machine class** or **subclasses of a Machine**.

Hence,`T` can have only one out of three values: `Machine`, `Laptop`, or `Mobile`.

### Generics with Wildcards (?)

In generic code, the question mark (`?`), called the wildcard, represents an unknown type. A wildcard parameterized type
is an instantiation of a generic type where at least one type argument is a wildcard.

Examples of wildcard parameterized types:

- `Collection<?>` — **Unbounded wildcard parameterized type**. Denotes all instantiations of the `Collection` interface
  without any restriction on the type argument.
- `List<? extends Number>` — **Upper bounded wildcard parameterized type**. Denotes all list types where the element
  type is a subtype of `Number` or a type `Number` itself.
- `Comparator<? super String>`— **Lower bounded wildcard parameterized type**. Denotes all instantiations of
  the `Comparator` interface for type argument types that are supertypes of `String` or the type `String` itself.

```java
public class GenericsExamples {

    public static void main(String[] args) {

        Object anyObject = new Object();
        Number anyNumber = 1;
        int anyInt = 2;
        byte anyByte = Byte.parseByte("3");

        //region Invariance
        List<Object> listObject_ListObject = new ArrayList<Object>();
        //List<Object> listObject_ListNumber = new ArrayList<Number>();   // error - can assign only exactly <Object>
        //List<Object> listObject_ListInteger = new ArrayList<Integer>();   // error - can assign only exactly <Object>
        //List<Object> listObject_ListByte = new ArrayList<Byte>();   // error - can assign only exactly <Object>

        //List<Number> listNumber_ListObject = new ArrayList<Object>();  // error - can assign only exactly <Number>
        List<Number> listNumber_ListNumber = new ArrayList<Number>();
        //List<Number> listNumber_ListInteger = new ArrayList<Integer>(); // error - can assign only exactly <Number>
        //List<Number> listNumber_ListByte  = new ArrayList<Byte>(); // error - can assign only exactly <Number>

        //List<Integer> listInteger_ListObject  = new ArrayList<Object>(); // error - can assign only exactly <Integer>
        //List<Integer> listInteger_ListNumber  = new ArrayList<Number>(); // error - can assign only exactly <Integer>
        List<Integer> listInteger_ListInteger = new ArrayList<Integer>();
        //List<Integer> listInteger_ListByte  = new ArrayList<Byte>(); // error - can assign only exactly <Integer>

        // operations of reading (producing)
        anyObject = listObject_ListObject.get(0);
        //anyNumber = listObject_ListObject.get(0); // error - not safe to get Number
        //anyInt = listObject_ListObject.get(0); // error - not safe to get Integer
        //anyByte = listObject_ListObject.get(0); // error - not safe to get Byte

        anyObject = listNumber_ListNumber.get(0);
        anyNumber = listNumber_ListNumber.get(0);
        //anyInt = listNumber_ListNumber.get(0); // error - not safe to get Integer
        //anyByte = listNumber_ListNumber.get(0); // error - not safe to get Byte

        anyObject = listInteger_ListInteger.get(0);
        anyNumber = listInteger_ListInteger.get(0);
        anyInt = listInteger_ListInteger.get(0);
        //anyByte = listInteger_ListInteger.get(0); // error - not safe to get Byte

        // operations of setting (consuming)
        listObject_ListObject.add(anyObject); // ok - allowed to add Object to exactly List<Object>
        listObject_ListObject.add(anyInt); // ok - allowed to add Object, Number, Integer, Byte to exactly List<Object>

        //listNumber_ListNumber.add(anyObject); // error - not allowed to add Object to exactly List<Number>
        listNumber_ListNumber.add(anyNumber); // ok - allowed to add Number to exactly List<Number>
        listNumber_ListNumber.add(anyInt); // ok - allowed to add Integer to exactly List<Number>
        listNumber_ListNumber.add(anyByte); // ok - allowed to add Byte to exactly List<Number>

        //listInteger_ListInteger.add(anyObject); // error - not allowed to add Object to exactly List<Integer>
        //listInteger_ListInteger.add(anyNumber); // error - not allowed to add Number to exactly List<Integer>
        listInteger_ListInteger.add(anyInt); // ok - allowed to add Integer to exactly List<Integer>
        //listInteger_ListInteger.add(anyByte); // error - not allowed to add Byte to exactly List<Integer>
        //endregion

        //region Covariance
        List<? extends Object> listExtendsObject_ListObject = new ArrayList<Object>();
        List<? extends Object> listExtendsObject_ListNumber = new ArrayList<Number>();
        List<? extends Object> listExtendsObject_ListInteger = new ArrayList<Integer>();
        List<? extends Object> listExtendsObject_ListByte = new ArrayList<Byte>();

        //List<? extends Number> listExtendsNumber_ListObject = new ArrayList<Object>(); // error - Object is not a subclass of Number
        List<? extends Number> listExtendsNumber_ListNumber = new ArrayList<Number>();
        List<? extends Number> listExtendsNumber_ListInteger = new ArrayList<Integer>();
        List<? extends Number> listExtendsNumber_ListByte = new ArrayList<Byte>();

        //List<? extends Integer> listExtendsInteger_ListObject  = new ArrayList<Object>(); // error - Object is not a subclass of Integer
        //List<? extends Integer> listExtendsInteger_ListNumber  = new ArrayList<Number>(); // error - Number is not a subclass of Integer
        List<? extends Integer> listExtendsInteger_ListInteger = new ArrayList<Integer>();
        //List<? extends Integer> listExtendsInteger_ListByte  = new ArrayList<Byte>(); // error - Byte is not a subclass of Integer

        // operations of reading (producing)
        anyObject = listExtendsNumber_ListNumber.get(0);
        anyNumber = listExtendsNumber_ListNumber.get(0);
        //anyInt = listExtendsNumber_ListNumber.get(0); // error - not safe to get Integer
        //anyByte = listExtendsNumber_ListNumber.get(0); // error - not safe to get Byte

        anyObject = listExtendsInteger_ListInteger.get(0);
        anyNumber = listExtendsInteger_ListInteger.get(0);
        anyInt = listExtendsInteger_ListInteger.get(0);
        //anyByte = listExtendsInteger_ListInteger.get(0); // error - not safe to get Byte

        // operations of setting (consuming)
        //listExtendsObject_ListObject.add(anyObject);  // error - can't add Object to *possible* List<Byte>, even though it is really List<Object>

        // These next 3 are compile errors for the same reason:
        // You don't know what kind of List<T> is really being referenced - it may not be able to hold an Integer.
        // You can't add anything (not Object, Number, Integer, nor Byte) to List<? extends Number>
        //listExtendsNumber_ListNumber.add(anyInt); // error - can't add Integer to *possible* List<Byte>, even though it is really List<Number>
        //listExtendsNumber_ListInteger.add(anyInt); // error - can't add Integer to *possible* List<Byte>, even though it is really List<Integer>
        //listExtendsNumber_ListByte.add(anyInt); // error - can't add Integer to *possible* List<Byte>, especially since it is really List<Byte>

        // This fails for same reason above - you can't guarantee what kind of List the variable is really pointing to
        //listExtendsInteger_ListInteger.add(anyInt); // error - can't add Integer to *possible* List<X> that is only allowed to hold X's
        //endregion

        //region Contravariance
        List<? super Object> listSuperObject_ListObject = new ArrayList<Object>();
        //List<? super Object> listSuperObject_ListNumber = new ArrayList<Number>(); // error - Integer is not superclass of Object
        //List<? super Object> listSuperObject_ListInteger = new ArrayList<Integer>(); // error - Integer is not superclass of Object
        //List<? super Object> listSuperObject_ListByte  = new ArrayList<Byte>(); // error - Byte is not superclass of Object

        List<? super Number> listSuperNumber_ListObject = new ArrayList<Object>();
        List<? super Number> listSuperNumber_ListNumber = new ArrayList<Number>();
        //List<? super Number> listSuperNumber_ListInteger = new ArrayList<Integer>(); // error - Integer is not superclass of Number
        //List<? super Number> listSuperNumber_ListByte  = new ArrayList<Byte>(); // error - Byte is not superclass of Number

        List<? super Integer> listSuperInteger_ListObject = new ArrayList<Object>();
        List<? super Integer> listSuperInteger_ListNumber = new ArrayList<Number>();
        List<? super Integer> listSuperInteger_ListInteger = new ArrayList<Integer>();
        //List<? super Integer> listSuperInteger_ListByte  = new ArrayList<Byte>(); // error - Byte is not a superclass of Integer

        // operations of reading (producing)
        anyObject = listSuperNumber_ListNumber.get(0);
        //anyNumber = listSuperNumber_ListNumber.get(0); // error - not allowed to get Number to List<Number> or List<Object>
        //anyInt = listSuperNumber_ListNumber.get(0); // error - not allowed to get Integer to List<Number> or List<Object>
        //anyByte = listSuperNumber_ListNumber.get(0); // error - not allowed to get Byte to List<Number> or List<Object>

        anyObject = listSuperInteger_ListInteger.get(0);
        //anyNumber = listSuperInteger_ListInteger.get(0); // error - not allowed to get Number to List<Integer>, List<Number>, or List<Object>
        //anyInt = listSuperInteger_ListInteger.get(0); // error - not allowed to get Integer to List<Integer>, List<Number>, or List<Object>
        //anyByte = listSuperInteger_ListInteger.get(0); // error - not allowed to get Byte to List<Integer>, List<Number>, or List<Object>

        // operations of setting (consuming)
        //listSuperNumber_ListNumber.add(anyObject); // error - not allowed to add Object to List<Number> or List<Object>
        listSuperNumber_ListNumber.add(anyNumber); // ok - allowed to add Number to List<Number> or List<Object>
        listSuperNumber_ListNumber.add(anyInt); // ok - allowed to add Integer to List<Number> or List<Object>
        listSuperNumber_ListNumber.add(anyByte); // ok - allowed to add Byte to List<Number> or List<Object>

        //listSuperInteger_ListInteger.add(anyObject); // error - not allowed to add Object to List<Integer>, List<Number>, or List<Object>
        //listSuperInteger_ListInteger.add(anyNumber); // error - not allowed to add Number to List<Integer>, List<Number>, or List<Object>
        listSuperInteger_ListInteger.add(anyInt); // ok - allowed to add Integer to List<Integer>, List<Number>, or List<Object>
        //listSuperInteger_ListInteger.add(anyByte); // error - not allowed to add Byte to List<Integer>, List<Number>, or List<Object>
        //endregion
    }
}
```

### Not allowed to do with Generics:

1. #### You can’t have static field of type

    ```java
    class GenericsExample<T> {
        private static T member; //Not allowed
    }
    ```

2. #### You can not create an instance of T

    ```java
    class GenericsExample<T> {
        public GenericsExample(){
            new T(); //Not allowed
        }
    }
    ```

3. #### Generics are not compatible with primitives in declarations

    ```java
    class GenericsExample<T> {
        List<int> ids = new ArrayList<>(); //Not allowed
    }
    ```

4. #### You can’t create Generic exception class

    ```java
    // causes compiler error
    public class GenericException<T> extends Exception {
    }
    ```

5. #### The `super` bound is not allowed in class, interface or method definition

    ```java
    //this code does not compile !
    class Forbidden<X super Vehicle> {
        <X super Vehicle> void foo() {
        }
    }
    ```

### ///// References (online):

* [Java Generics Tutorial](https://howtodoinjava.com/java/generics/complete-java-generics-tutorial/)
* [Java Generics: Why Are They Used?](https://medium.com/javarevisited/java-generics-why-are-they-used-4157734de4fd)
* [How can I add to List<? extends Number> data structures?](https://stackoverflow.com/questions/2776975/how-can-i-add-to-list-extends-number-data-structures)
* [An introduction to generic types in Java: covariance and contravariance](https://medium.com/free-code-camp/understanding-java-generic-types-covariance-and-contravariance-88f4c19763d2)
* [Java Generics FAQs](http://www.angelikalanger.com/GenericsFAQ/JavaGenericsFAQ.html)
* [Why super keyword in generics is not allowed at class level](https://stackoverflow.com/questions/37411256/why-super-keyword-in-generics-is-not-allowed-at-class-level/37411519#37411519)
* [Пришел, увидел, обобщил: погружаемся в Java Generics](https://habr.com/en/company/sberbank/blog/416413/)

[^ up](#knowledge-notes)

***

# Concurrency

## Java Thread

In concurrent programming, there are two basic units of execution: processes and threads. A process has a self-contained
execution environment. A process generally has a complete, private set of basic run-time resources; in particular, each
process has its own memory space.

A `Thread` is a lightweight Process. Both processes and threads provide an execution environment, but creating a new
thread requires fewer resources than creating a new process.

Threads exist within a process — every process has at least one.

### Create new Thread:

1. #### Provide a Runnable object

    ```java
    public class HelloRunnable implements Runnable {
    
        public void run() {
            System.out.println("Hello from a thread!");
        }
    
        public static void main(String[] args) {
            (new Thread(new HelloRunnable())).start();
        }
    
    }
    ```

2. #### Extending the Thread Class.

   The Thread class itself implements Runnable, though its run method does nothing.

    ```java
    public class HelloThread extends Thread {
    
        public void run() {
            System.out.println("Hello from a thread!");
        }
    
        public static void main(String[] args) {
            (new HelloThread()).start();
        }
    
    }
    ```

3. #### Implementing the Callable Interface

   To create the thread, a `Runnable` is required. To obtain the result, a `Future` is required.

   The Java library has the concrete type `FutureTask`, which implements `Runnable` and `Future`, combining both
   functionality conveniently. A `FutureTask` can be created by providing its constructor with a `Callable`. Then
   the `FutureTask` object is provided to the constructor of `Thread` to create the `Thread` object. Thus, indirectly,
   the thread is created with a `Callable`. For further emphasis, note that **there is no way to create the thread
   directly with a Callable**.

    ```java
    public class CallableExample implements Callable {
    
        public Object call() {

            Random generator = new Random();
            Integer randomNumber = generator.nextInt(5);
            Thread.sleep(randomNumber * 1000);
    
            return randomNumber;
        }
    }
  
    public class CallableFutureTest {
  
        public static void main(String[] args) {
            
            FutureTask[] randomNumberTasks = new FutureTask[5];
            
            for (int i = 0; i < 5; i++) {
                Callable callable = new CallableExample();
                randomNumberTasks[i] = new FutureTask(callable);
                Thread t = new Thread(randomNumberTasks[i]);
                t.start();
            }
    
            for (int i = 0; i < 5; i++) {
                System.out.println(randomNumberTasks[i].get());
                // As it implements Future, we can call get()
                // This method blocks till the result is obtained
                // The get method can throw checked exceptions
                // like when it is interrupted. This is the reason
                // for adding the throws clause to main
            }
        }
    }
    ```

4. #### By using the Executor Framework along with Runnable and Callable Tasks

   Executor provides a way of decoupling task submission from the mechanics of how each task will be run, including
   details of thread use, scheduling, etc. An `Executor` is normally used instead of explicitly creating threads. The
   command may execute in a new thread, in a pooled thread, or in the calling thread, at the discretion of
   the `Executor`
   implementation.

    ```java
    public class HelloExecutor {
    
        public static Executor executor;
    
        public static void main(String[] args) {
            executor.execute(new RunnableTask1());
            executor.execute(new RunnableTask2());
        }
    }  
    ```

### Thread states:

![concurrency_thread_lifecycle](res/images/java-concurrency-thread-lifecycle.png)

- **NEW** — A NEW Thread is a thread that’s been created but not yet started.
- **RUNNABLE** — Threads in this state are either running or ready to run, but they’re waiting for resource allocation
  from the system.
- **WAITING** — Waiting for some other thread to perform a particular action without any time limit.
- **TIMED_WAITING** — Waiting for some other thread to perform a specific action for a specified amount of time.
- **BLOCKED** — Waiting to acquire a lock to enter or re-enter a synchronized block/method.
- **TERMINATED** — When thread has either finished execution or was terminated abnormally. Once a thread is terminated,
  it cannot be resumed.

### Liveness

A concurrent application's ability to execute in a timely manner is known as its liveness.

- **Deadlock** — describes a situation where two or more threads are blocked forever, waiting for each other.
- **Starvation** — describes a situation where a thread is unable to gain regular access to shared resources and is
  unable to make progress.
- **Livelock** — A thread often acts in response to the action of another thread. If the other thread's action is also a
  response to the action of another thread, then livelock may result. As with deadlock, livelocked threads are unable to
  make further progress. However, the threads are not blocked — they are simply too busy responding to each other to
  resume work.

### Thread Class vs Runnable Interface

* If we extend the `Thread` class, our class cannot extend any other class because Java doesn't support multiple
  inheritance. But, if we implement the Runnable interface, our class can still extend other base classes.
* We can achieve basic functionality of a thread by extending `Thread` class because it provides some inbuilt methods
  like
  `yield()`, `interrupt()` etc. that are not available in `Runnable` interface.
* Using `Runnable` will give you an object that can be shared amongst multiple threads.

### Callable vs Runnable

* For implementing `Runnable`, the `run()` method needs to be implemented which does not return anything, while for a
  `Callable`, the `call()` method needs to be implemented which returns a result on completion. Note that a thread can’t
  be created with a Callable, it can only be created with a `Runnable`.
* Another difference is that the `call()` method can throw an exception whereas `run()` cannot.

### ///// References (online):

* [Java Threads - Creating Threads and Multithreading in Java](https://medium.com/edureka/java-thread-bfb08e4eb691)
* [Callable and Future in Java](https://www.geeksforgeeks.org/callable-future-java/)
* [How to Implement Callable Interface in Java](https://www.edureka.co/blog/callable-interface-in-java/)
* [Java Concurrency: How To Create A Thread](https://medium.com/javarevisited/java-concurrency-how-to-create-a-thread-a760bac60d27)
* [Java Concurrency: Thread Life Cycle](https://medium.com/javarevisited/java-concurrency-thread-life-cycle-4869432474b)
* [Java Concurrency: Thread Methods](https://medium.com/javarevisited/java-concurrency-thread-methods-54d12545c825)

[^ up](#knowledge-notes)

---

## Lock vs Monitor

### Lock

**A lock is kind of data which is logically part of an object’s header on the heap memory.** Each object in a JVM has
this lock (or mutex) that any program can use to coordinate multi-threaded access to the object. If any thread want to
access instance variables of that object; then thread must “own” the object’s lock (set some flag in lock memory area).
All other threads that attempt to access the object’s variables have to wait until the owning thread releases the
object’s lock (unset the flag).

### Monitor

**Monitor is a synchronization construct that allows threads to have both mutual exclusion (using locks) and
cooperation** i.e. the ability to make threads wait for certain condition to be true (using **wait-set**). In other
words, along with data that implements a lock, every Java object is logically associated with data that implements a
wait-set.

![java_concurrency_monitor](res/images/java-concurrency-monitor.png)

### ///// References (online):

* [What is a Monitor in Computer Science?](https://www.baeldung.com/cs/monitor)
* [Difference between lock and monitor – Java Concurrency](https://howtodoinjava.com/java/multi-threading/multithreading-difference-between-lock-and-monitor/)
* [Object level lock vs Class level lock in Java](https://howtodoinjava.com/java/multi-threading/object-vs-class-level-locking/)
* [Difference: this vs MyClass.class vs MyClass.getClass() in synchronisation](https://stackoverflow.com/questions/51839363/difference-this-vs-myclass-class-vs-myclass-getclass-in-synchronisation)
* [IllegalMonitorStateException in Java](https://www.baeldung.com/java-illegalmonitorstateexception)

[^ up](#knowledge-notes)

---

## Synchronized vs Volatile vs Atomic

### Synchronized

- Provides **mutual exclusion** - two threads can not run a synchronized method or block at the same time. Though beware
  of using static and non-static synchronized methods together.
- Provides **visibility guaranteed** - updated values of variables modified inside synchronized context will be visible
  to all threads.
- A synchronized keyword also provides **blocking**. A thread will block until the lock is available, before entering to
  code protected by a synchronized keyword. See how synchronization works in Java to know all about synchronized
  keyword.
- As per the **happens-before rule**, an unlock on a monitor happens-before every subsequent lock on the same monitor.

### Volatile

- It provides a **visibility guarantee**. As per the happens-before rule, write to volatile variable happens before
  every subsequent read of the same variable.
- It also prevents Compiler from doing smart things, which can create problems in a multi-threading environment, like
  caching variables, re-ordering of code, etc.

### Atomic classes like AtomicInteger, AtomicLong, and AtomicReference

- Atomic variables also provides the same memory semantics as a volatile variable, but with an added feature of
  making **compound action** atomic.
- It provides a convenient method to perform atomic increment, decrement, **CAS (compare-and-swap) operations**. Useful
  methods are `addAndGet(int delta)`, `compareAndSet(int expect, int update)`, `incrementAndGet()` and
  `decrementAndGet()`

### ///// References (online):

* [Difference between atomic, volatile and synchronized in Java](https://javarevisited.blogspot.com/2020/04/difference-between-atomic-volatile-and-synchronized-in-java-multi-threading.html#axzz79ky1D7bi)
* [What is the difference between atomic / volatile / synchronized?](https://stackoverflow.com/questions/9749746/what-is-the-difference-between-atomic-volatile-synchronized)
* [Java Concurrency: Volatile](https://medium.com/javarevisited/java-concurrency-volatile-d0e585852d6b)
* [Java Concurrency: Synchronized](https://medium.com/javarevisited/java-concurrency-synchronized-7828bf5f06cb)

[^ up](#knowledge-notes)

---

## Executors

In large-scale applications, it makes sense to separate thread management and creation from the rest of the application.
Objects that encapsulate these functions are known as **executors**.

### UML Diagram of Executor framework:

![java-executor-framework-uml-diagram.png](res/images/java-executor-framework-uml-diagram.png)

### Executor Interfaces:

- ### The Executor Interface

  The `Executor` interface supports launching new tasks. It provides a single method `execute()`. The definition
  of `execute()` is less specific. The low-level idiom creates a new thread and launches it immediately. Depending on
  the `Executor` implementation, `execute()` may do the same thing, but is more likely to use an existing worker thread
  to run `Runnable` object, or to place `Runnable` object in a queue to wait for a worker thread to become available.

- ### The ExecutorService Interface

  The `ExecutorService` interface supplements `execute()` with a similar, but a more versatile `submit()` method.
  Like `execute()`, `submit()` accepts `Runnable` objects, but also accepts `Callable` objects, which allow the task to
  return a value. The `submit()` method returns a `Future` object, which is used to retrieve the `Callable` return value
  and to manage the status of both `Callable` and `Runnable` tasks.

  `ExecutorService` also provides methods for submitting large collections of `Callable` objects.
  Finally, `ExecutorService` provides a number of methods for managing the shutdown of the executor. To support the
  immediate shutdown, tasks should handle interrupts correctly.

    ```java
    class ExecutorServiceExample {
    
        public static void main(String[] args) {
            // The easiest way to create ExecutorService is to use
            // one of the factory methods of the Executors class.
            // Create a thread-pool with 10 threads.
            ExecutorService executorService = Executors.newFixedThreadPool(10);
    
            // The execute() method is void, and it doesn't give any possibility
            // to get the result of task’s execution or to check
            // the task’s status (is it running or executed).
            executorService.execute(runnableTask);
    
            // The submit() submits a Callable or a Runnable task to an ExecutorService
            // and returns a result of type Future.
            Future<String> future = executorService.submit(callableTask);
    
            // The invokeAny() assigns a collection of tasks to an ExecutorService,
            // causing each to be executed, and returns the result of a successful
            // execution of one task (if there was a successful execution).
            String result = executorService.invokeAny(callableTasks);
    
            // The invokeAll() assigns a collection of tasks to an ExecutorService,
            // causing each to be executed, and returns the result of all
            // task executions in the form of a list of objects of type Future.
            List<Future<String>> futures = executorService.invokeAll(callableTasks);
    
            // The ExecutorService will not be automatically destroyed
            // when there is no task to process. It will stay alive
            // and wait for new work to do.
    
            // The shutdown() method doesn't cause immediate destruction of
            // the ExecutorService. It will make the ExecutorService stop
            // accepting new tasks and shut down after all running threads
            // finish their current work.
            executorService.shutdown();
    
            // The shutdownNow() method tries to destroy the ExecutorService
            // immediately, but it doesn't guarantee that all the running threads
            // will be stopped at the same time. This method returns a list
            // of tasks that are waiting to be processed.
            List<Runnable> notExecutedTasks = executorService.shutdownNow();
        }
    }
    ```

- ### The ScheduledExecutorService Interface

  The `ScheduledExecutorService` interface supplements the methods of its parent `ExecutorService` with schedule, which
  executes a `Runnable` or `Callable` task after a specified delay. In addition, the interface
  defines `scheduleAtFixedRate` and `scheduleWithFixedDelay`, which executes specified tasks repeatedly, at defined
  intervals.

### Thread Pools

Since active threads consume system resources, a JVM creating too many threads at the same time can cause the system to
run out of memory. This necessitates the need to limit the number of threads being created.

Most of the executor implementations in `java.util.concurrent` use **thread pools**, which consist of _worker threads_.
This kind of thread exists separately from the `Runnable` and `Callable` tasks it executes and is often used to execute
multiple tasks.

Using worker threads minimizes the overhead due to thread creation. Thread objects use a significant amount of memory,
and in a large-scale application, allocating and deallocating many thread objects creates a significant memory
management overhead.

- ### Fixed Thread Pool

  One common type of thread pool is the fixed thread pool. This type of pool always has a specified number of threads
  running. Instead of starting a new thread for every task to execute concurrently, the task can be passed to a thread
  pool. As soon as the pool has any idle threads the task is assigned to one of them and executed. Internally the tasks
  are inserted into a `BlockingQueue` which the threads in the pool are dequeuing from. When a new task is inserted into
  the queue one of the idle threads will dequeue it successfully and execute it. The rest of the idle threads in the
  pool will be blocked waiting to dequeue tasks. Invoke `newFixedThreadPool(int numberOfThreads)` method to create it.

- ### Cached Thread Pool

  This is an expandable thread pool. This does not have fixed size of threads. This is suitable for application which
  have a lot of short live tasks. According to the below diagram,when coming new task to the cached thread pool, if all
  the threads are busy, cached thread pool is created new thread for that new task. If thread is idle for 1 minute, it
  will tear down those threads. Invoke `newCachedThreadPool()` method to create it.

- ### Scheduled Thread Pool

  If you need to schedule the task after certain delay, this is the thread pool for it. If you need a check security
  check, login check within every 10 seconds, we can use scheduled thread pool.
  Invoke `newScheduledThreadPool(int corePoolSize)` method to create it.

- ### Single Thread Executor

  Here, only one thread is using in the thread pool. All the tasks are keeping in blocking queue. After finishing task,
  it fetches new task from the queue and execute it. Invoke `newSingleThreadExecutor()`method to create it.

### ///// References (online):

* [Java Concurrency: Executors](https://medium.com/javarevisited/java-concurrency-executors-fa2307ed7f80)
* [Java Multithread Executor Framework <Callable, Future, Executor and Executor Service>](https://programmer.help/blogs/5d312fd63b7b0.html)
* [Java Concurrency: Thread Pools](https://medium.com/javarevisited/java-concurrency-thread-pools-3f1902b7beee)
* [Executor Service(Java Thread Pool Framework)](https://pramodshehan.medium.com/executor-service-java-thread-pool-framework-d314b12ca043)
* [Executor Framework- Understanding the basics (Part 1)](https://medium.com/android-news/executor-framework-understanding-the-basics-43d575e72310)
* [Executor Framework- Understanding the basics (Part 2)](https://medium.com/android-news/executor-framework-understanding-the-basics-part-2-bc3427fa8e2f)
* [How to use the Executor Framework in Java](https://levelup.gitconnected.com/how-to-use-the-executor-framework-in-java-58a610d20b87)
* [How to use Java Callable and Future](https://levelup.gitconnected.com/how-to-use-java-callable-and-future-5d79ecb47c8b)

[^ up](#knowledge-notes)

---

## ///// References (online):

* [Java Tutorial Lesson: Concurrency](https://docs.oracle.com/javase/tutorial/essential/concurrency/)
* [Многопоточность в Java](https://habr.com/ru/post/164487/)
* [Обзор java.util.concurrent.*](https://habr.com/ru/company/luxoft/blog/157273/)
* [Справочник по синхронизаторам java.util.concurrent.*](https://habr.com/ru/post/277669/)

[^ up](#knowledge-notes)

***

# JVM

## JVM Architecture

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

- ### Method Area

  This is a shared resource (only 1 method area per JVM). Is not thread safe. Method area stores class level data (
  including static variables) such as:

    - **Classloader reference**.
    - **Run time constant pool** — Numeric constants, field references, method references, attributes.
    - **Field data** — Per field: name, type, modifiers, attributes.
    - **Method data** — Per method: name, return type, parameter types (in order), modifiers, attributes.
    - **Method code** — Per method: bytecodes, operand stack size, local variable size, local variable table, exception
      table.

- ### Heap area

  This is a shared resource (only 1 heap area per JVM). Is not thread safe. Information of all objects and their
  corresponding instance variables and arrays are stored in the Heap area. Heap area is a great target for Garbage
  Collection.

- ### Stack Area

  This is not a shared resource and is thread safe. For every JVM thread, when the thread starts, a separate runtime
  stack gets created in order to store method calls. For every such method call, one entry will be created and added
  (pushed) into the top of runtime stack and such entry is called a _Stack Frame_. The size of stack frame is fixed
  according to the method. The frame is removed (popped) when the method returns normally or if an uncaught exception is
  thrown during the method invocation. After a thread terminates, its stack frame will also be destroyed by JVM.  
  A Stack Frame is divided into three sub-entities:

    - **Local Variable Array** — Variable from index 0 is the reference of a class instance where the method belongs.
      From 1, the parameters sent to the method are saved.
    - **Operand Stack** — Each method exchanges data between the Operand stack and the local variable array, and pushes
      or pops other method invoke results.
    - **Frame Data** — All symbols related to the method are stored here.


- ### Program Counter Registers
  For each JVM thread, when the thread starts, a separate PC Register gets created in order to hold the address of
  currently-executing instruction (memory address in the Method area).


- ### Native Method Stack
  There is a direct mapping between a Java thread and a native operating system thread. A separate native stack also
  gets created in order to store native method information (often written in C/C++) invoked through JNI (Java Native
  Interface).

### 3. Execution Engine

The actual execution of the bytecode occurs here. Execution Engine executes the instructions in the bytecode
line-by-line by reading the data assigned to above runtime data areas.

- ### Interpreter
  It interprets the bytecode and executes the instructions one-by-one.


- ### Just-In-Time (JIT) Compiler
  First, it compiles the entire bytecode to native code (machine code). Then for repeated method calls, it directly
  provides the native code. The native code is stored in the cache, thus the compiled code can be executed quicker.


- ### Garbage Collector (GC)
  As long as an object is being referenced, the JVM considers it alive. Once an object is no longer referenced and
  therefore is not reachable by the application code, the garbage collector removes it and reclaims the unused memory.
  We can trigger it by calling System.gc() method (But the execution is not guaranteed.).

### 4. Java Native Interface (JNI)

This interface is used to interact with Native Method Libraries. This enables JVM to call C/C++ libraries and to be
called by C/C++ libraries which may be specific to hardware.

### 5. Native Method Libraries

This is a collection of C/C++ Native Libraries which is required for the Execution Engine and can be accessed through
the provided Native Interface.

### //// References (online):

* [The Structure of the Java Virtual Machine](https://docs.oracle.com/javase/specs/jvms/se10/html/jvms-2.html)
* [Understanding JVM Architecture](https://medium.com/platform-engineer/understanding-jvm-architecture-22c0ddf09722)

[^ up](#knowledge-notes)

***

## JVM Memory Model

![jvm_native_memory](res/images/jvm-native-memory.png)

![jvm_stack_non_heap_heap](res/images/jvm-stack-non-heap-heap.png)

### 1. Heap Memory

It is a larger region of RAM which is used for dynamic memory allocation. All Java objects are stored in the heap and
the scope of the objects is the whole application. The memory management is managed by us in heap, but the unused
objects are cleared by the Garbage collector automatically.

![jvm_heap_memory](res/images/jvm-heap-memory.png)

- ### Young Generation
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


- ### Old Generation
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

### ///// References (online):

* [Understanding Java Memory Model](https://medium.com/platform-engineer/understanding-java-memory-model-1d0863f6d973)
* [JVM Memory Model](https://amanagrawal9999.medium.com/jvm-memory-model-70821e84af4b)
* [Java Memory Explained](https://medium.com/nerd-for-tech/java-memory-explained-43de6de157be)
* [Visualizing memory management in JVM](https://medium.com/@deepu105/visualizing-memory-management-in-jvm-java-kotlin-scala-groovy-clojure-4fbcc0929482)
* [Java Concurrency: Java Memory Model](https://medium.com/javarevisited/java-concurrency-java-memory-model-96e3ac36ec6b)

[^ up](#knowledge-notes)

***

## Stack vs Heap

![jvm_stack_vs_heap](res/images/jvm-stack-vs-heap.png)

### ///// References (online):

* [Understanding Java Memory Model](https://medium.com/platform-engineer/understanding-java-memory-model-1d0863f6d973)
* [Стек и куча в Java](https://topjava.ru/blog/stack-and-heap-in-java)

[^ up](#knowledge-notes)

---

## Garbage Collector

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

* ### Serial GC

  Simple mark-sweep-compact approach with young and old gen garbage collections (a.k.a. Minor GC and Major GC). Suitable
  for simple stand-alone client-machine applications running with low memory footprint and less CPU power.

  ![jvm_gc_serial](res/images/jvm-gc-serial.png)

* ### Parallel GC

  Parallel version of mark-sweep-compact approach for Minor GC with multiple threads (Major GC still happens with a
  single thread in a serial manner).

  ![jvm_gc_parallel](res/images/jvm-gc-parallel.png)

* ### Concurrent Mark Sweep (CMS) GC

  Garbage collection normally happens with pauses (Major GC takes a long time), which makes it problematic for highly
  responsive applications where we can’t afford long pause times. CMS Collector minimizes the impact of these pauses by
  doing most of the garbage collection work (i.e. Major GC) concurrently within the application threads (Minor GC still
  follows the usual parallel algorithm without any concurrent progress with application threads).

* ### G1 GC

  Garbage First (G1) Collector divides the Heap into multiple equal-sized regions and when GC is invoked, first collects
  the region with lesser live data (young gen and old gen implementations don’t apply here). This collector is a
  parallel, concurrent and incrementally compact low-pause garbage collector which intends to replace the CMS Collector.

  ![jvm_gc_g1](res/images/jvm-gc-g1.png)

* ### Shenandoah GC

  Compacting work concurrently within the application threads. Experimental until JDK 15.

* ### Z GC

  All GC processes concurrently within the application threads. Experimental until JDK 15.

### ///// References (online):

* [Understanding Java Garbage Collection](https://medium.com/platform-engineer/understanding-java-garbage-collection-54fc9230659a)
* [Сборка мусора в Java: что это такое и как работает в JVM](https://medium.com/nuances-of-programming/%D1%81%D0%B1%D0%BE%D1%80%D0%BA%D0%B0-%D0%BC%D1%83%D1%81%D0%BE%D1%80%D0%B0-%D0%B2-java-%D1%87%D1%82%D0%BE-%D1%8D%D1%82%D0%BE-%D1%82%D0%B0%D0%BA%D0%BE%D0%B5-%D0%B8-%D0%BA%D0%B0%D0%BA-%D1%80%D0%B0%D0%B1%D0%BE%D1%82%D0%B0%D0%B5%D1%82-%D0%B2-jvm-25bb2570b44c)
* [Дюк, вынеси мусор! — Часть 1](https://habr.com/ru/post/269621/)
* [Дюк, вынеси мусор! — Часть 2](https://habr.com/ru/post/269707/)
* [Дюк, вынеси мусор! — Часть 3](https://habr.com/ru/post/269863/)

[^ up](#knowledge-notes)

---

## Java Reference Types

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
  that an object has neither strong nor soft references pointing to it. First off, the Garbage Collector clears a weak
  reference, so the referent is no longer accessible. Then the reference is placed in a reference queue (if any
  associated exists) where we can obtain it from. At the same time, formerly weakly-reachable objects are going to be
  finalized. One of the best implementations of weak references is the **WeakHashMap**. Reference queue is optional.

* ### Phantom Reference

  These references are only garbage collected when none of the references namely strong references, soft references or
  weak references is being pointed to the object belonging to phantom references. They are always used **along with
  reference queue** and to keep a track of objects which have been finalized. **We can't get a referent of a phantom
  reference: `phantomReference.get()` returns `null`**. One of the use cases is **to determine when an object was
  removed from the memory** which helps to schedule memory-sensitive tasks. For example, we can wait for a large object
  to be removed before loading another one.

### ///// References (online):

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

# Kotlin

# Kotlin Syntax Features

## Kotlin Scope Functions

| Function | Object reference | Return value | Is extension function | Usage |
| --- | --- | --- | --- | --- |
| `.let{it} ` | `it` | Lambda result | Yes | 1) to invoke one or more functions on results of call chains; 2)  to execute a code block only with non-null values; 3) to introduce local variables with a limited scope for improving code readability. |

```kotlin
val str: String? = "Hello"
val length: Int? = str?.let { nonNullStr -> // it - nonNullStr is `String` && nonNullStr === str
    println("let() called on $nonNullStr")
    nonNullStr.length
    // lambda returns `Int`
}
println(length) // 5 or `null` if str == `null`
```

| Function | Object reference | Return value | Is extension function | Usage |
| --- | --- | --- | --- | --- |
| `.run{this}` | `this` | Lambda result | Yes | 1) useful when your lambda contains both the object initialization and the computation of the return value. |

```kotlin
val service = MultiportService("https://example.kotlinlang.org", 80)
val result: QueryResult = service.run { // this: MultiportService
    port = 8080
    query(prepareRequest() + " to port $port")
    // lambda returns `QueryResult` instance
}
```

| Function | Object reference | Return value | Is extension function | Usage |
| --- | --- | --- | --- | --- |
| `run{}` | － | Lambda result | No | 1) useful for executing a block of several statements where an expression is required. |

```kotlin
val hexNumberRegex: Regex = run { // no reference to context object
    val digits = "0-9"
    val hexDigits = "A-Fa-f"
    val sign = "+-"
    Regex("[$sign]?[$digits$hexDigits]+")
    // lambda returns `Regex` instance
}
```

| Function | Object reference | Return value | Is extension function | Usage |
| --- | --- | --- | --- | --- |
| `with{this}` | `this` | Lambda result | No | 1) for calling functions on the context object without providing the lambda result; 2) introducing a helper object whose properties or functions will be used for calculating a return value. |

```kotlin
val numbers = mutableListOf("one", "two", "three")
// without providing the lambda result
with(numbers) { // this: MutableList<String>
    println("'with' is called with argument $this")
    println("It contains $size elements")
    // lambda returns the `Unit` kotlin type
}
// with using the context object for lambda result
val firstAndLast: String = with(numbers) { // this: MutableList<String>
    "The first element is ${first()}," +
            " the last element is ${last()}"
    // lambda returns the new `String`
}
```

| Function | Object reference | Return value | Is extension function | Usage |
| --- | --- | --- | --- | --- |
| `.apply{this}` | `this` | Context object | Yes | 1)  for code blocks that don't return a value and mainly operate on the members of the receiver object. |

```kotlin
val adam = Person("Adam").apply { // this: Person
    age = 32
    city = "London"
    // the same `Person` will be returned
}
```

| Function | Object reference | Return value | Is extension function | Usage |
| --- | --- | --- | --- | --- |
| `.also{it}` | `it` | Context object | Yes | 1) use for actions that need a reference rather to the object than to its properties and functions; 2) use when you don't want to shadow `this` reference from an outer scope. |

```kotlin
val numbers = mutableListOf("one", "two", "three")
numbers
    .also { // it: MutableList<String>
        println("The list elements before adding new one: $it")
        // the same `MutableList` will be returned
    }
    .add("four")
```

| Function | Object reference | Return value | Is extension function | Usage |
| --- | --- | --- | --- | --- |
| `.takeIf{it}?` | `it` | Context object, `null` | Yes | 1) use as a filtering function for a single object. |

```kotlin
val str = "Hello"
val caps = str.takeIf { it.isNotEmpty() }?.uppercase() ?: "null"
println(caps) // "HELLO"
```

| Function | Object reference | Return value | Is extension function | Usage |
| --- | --- | --- | --- | --- |
| `.takeUnless{it}?` | `it` | Context object, `null` | Yes | 1) use as a filtering function for a single object. |

```kotlin
val str = "Hello"
val caps = str.takeUnless { it.isNotEmpty() }?.uppercase() ?: "null"
println(caps) // "null"
```

### ///// References (online):

- [Scope functions](https://kotlinlang.org/docs/scope-functions.html#function-selection)

[^ up](#knowledge-notes)

---

## Kotlin Generics

```kotlin
class KotlinGenericsExample {

    open class Vehicle
    class Bicycle : Vehicle()
    class StoreProducer<out T> // Covariance
    class StoreConsumer<in T> // Contravariance

    fun main() {
        //var storeProducer_BicycleVehicle: StoreProducer<Bicycle> = StoreProducer<Vehicle>() // Error: Type mismatch
        var storeProducer_BicycleBicycle: StoreProducer<Bicycle> = StoreProducer<Bicycle>()
        var storeProducer_VehicleVehicle: StoreProducer<Vehicle> = StoreProducer<Vehicle>()
        var storeProducer_VehicleBicycle: StoreProducer<Vehicle> = StoreProducer<Bicycle>()

        var storeConsumer_BicycleVehicle: StoreConsumer<Bicycle> = StoreConsumer<Vehicle>()
        var storeConsumer_BicycleBicycle: StoreConsumer<Bicycle> = StoreConsumer<Bicycle>()
        var storeConsumer_VehicleVehicle: StoreConsumer<Vehicle> = StoreConsumer<Vehicle>()
        //var storeConsumer_VehicleBicycle: StoreConsumer<Vehicle> = StoreConsumer<Bicycle>() // Error: Type mismatch
    }
}
```

### Type projections

- #### Use-site variance: type projections

  > Kotlin's `Array<out Any>` corresponds to Java's `Array<? extends Object>`.
  >
  > Kotlin's `Array<in String>` corresponds to Java's `Array<? super String>`.

### Generic constraints

- #### Upper bounds

  > Kotlin's `T : Comparable<T>` corresponds to Java's `extends` keyword.
  >
  >`fun <T : Comparable<T>> sort(list: List<T>) { ... }`
  >
  > The type specified after a colon is the **upper bound**, indicating that only a subtype of `Comparable<T>` can be substituted for `T`.
  >
  > The **default upper bound** (if there was none specified) is `Any?`.

## ///// References (online):

- [Generics: in, out, where](https://kotlinlang.org/docs/generics.html)
- [Generics in Kotlin](https://medium.com/swlh/generics-in-kotlin-5152142e281c)
- [Introduction to Kotlin Generics: Reified Generic Parameters](https://medium.com/kotlin-thursdays/introduction-to-kotlin-generics-reified-generic-parameters-7643f53ba513)
- [Kotlin Covariance and Contravariance](https://medium.com/kotlin-thursdays/introduction-to-kotlin-generics-9d18d3719e1d)

[^ up](#knowledge-notes)

***

# Android

## Multithreading

When a user opens an application, Android creates its own Linux process. Besides this, the system creates a thread of
execution for that application called the **main thread** or **UI thread**.

**The main thread** is nothing but a handler thread. The main thread is responsible for handling events from all over
the app like callbacks associated with the lifecycle information or callbacks from input events or handling events from
other apps, etc

![android_multithreading](res/images/android-multithreading.png)

### Thread

The Java virtual machine allows an application to have multiple threads of execution running concurrently.

Concurrency means running multiple tasks in parallel, it is one of the main reasons that we use threads. As Android is a
single-threaded model, we need to create different threads to perform our task.

```java
  class LooperThread extends Thread {
    public Handler mHandler;

    public void run() {
        // Initialize the current thread as a looper. 
        Looper.prepare();

        // Looper.myLooper(() － Return the Looper object associated with the current thread.
        mHandler = new Handler(Looper.myLooper()) {
            public void handleMessage(Message msg) {
                // process incoming messages here
            }
        };
        // Run the message queue in this thread.
        // Be sure to call quit() or quitSafely() to end the loop.
        Looper.loop();
    }
}
```

We can perform any kind of operation inside threads except updating the UI elements. To update a UI element from a
thread, we need to use either the `Handler` or the `runOnUIThread()` method.

### Looper

The looper is responsible for keeping the thread alive. It is a kind of worker that serves a `MessageQueue` for the
current thread. `Looper` loops through a message queue and sends messages to corresponding threads to process. **There
will be only one unique looper per thread**. So, the looper is providing the thread with the facility to run in a loop
with its own `MessageQueue`.

`Looper.quit()` will immediately terminate the Looper and discard all the messages inside the `MessageQueue`. To make
sure that all message in the `MessageQueue` will be dispatched before quitting, we can use `Looper.quiteSafely()`.

### Handler

As there is only one thread that updates the UI, which is main thread, we use different other threads to do multiple
tasks in the background but finally, to update the UI, we need to post the result to the main or UI thread. So, Android
has provided handlers to make the inter-process communication easier. A handler allows you to send and process `Message`
and `Runnable` objects associated with a thread's `MessageQueue`. Each handler instance i**s associated with a single
thread and that thread’s message queue**.

When a handler is created, it can get a `Looper` object in the constructor, which indicates which thread the handler is
attached to. If you want to use a handler attached to the main thread, you need to use the looper associated with the
main thread by calling `Looper.getMainLooper()`.

#### How to schedule:

- `post(Runnable)`, `postAtTime(Runnable, long)`, `postDelayed(Runnable, long)` — The post versions allow you to enqueue
  `Runnable` objects to be called by the message queue when they are received.

- `sendEmptyMessage(int)`, `sendMessage(Message)`, `sendMessageAtTime(Message, long)`
  , `sendMessageDelayed(Message, long)` — The sendMessage versions allow you to enqueue a Message object containing a
  bundle of data that will be processed by the handler’s handleMessage(Message)

### HandlerThread

HandlerThread has their own `Looper` and `MessageQueue` or simply queue, other than the `Thread`. The looper can then be
used to create handler classes. Note that `start()` must still be called.

```java
public class HandlerThreadExample {

    public void doWork() {
        //create the HandlerThread
        HandlerThread handlerThread = new HandlerThread("HandlerThreadName");
        handlerThread.start();

        //create the Handler
        Handler handler = new Handler(handlerThread.getLooper());

        //Use the Handler to send message or post runnables as you deem fit and 
        //all these works will be done in the background.
        handler.post(new Runnable() {
                         // do background work
                     }
        );

        //close the handler thread when done. 
        //I mostly close it in the onDestroy method in activity or fragment.
        handlerThread.quit();
    }
}
```

### MessageQueue

The `MessageQueue` is a queue that has a list of tasks (messages, runnables) that will be executed in a certain thread.
Android maintains a `MessageQueue` on the main thread. It is a low-level class holding the list of messages to be
dispatched by a `Looper`. Messages are not added directly to a `MessageQueue`, but rather through handler objects
associated with the `Looper`. **There will be only one MessageQueue per thread**. The order in the Message list is base
on the timestamp. The message which has the lowest timestamp will be dispatched first.

### Message

The message defines a message containing a description and arbitrary data object that can be sent to a `Handler`. We can
simply say that message is something like a bundle that is used for the transfer of data. While the constructor of
message is public, the best way to get one of these is to call `Message.obtain()` or one of
the `Handler.obtainMessage()`
methods, which will pull them from a pool of recycled objects.

There are different arguments that can be useful:

- `public int what` — User-defined message code so that the recipient can identify what this message is about. **Each
  handler has its own name-space for message codes**, so you do not need to worry about yours conflicting with other
  handlers.
- `public int arg1`, `public int arg2` — `arg1` and `arg2` are lower-cost alternatives to using `setData()` if you only
  need to store a few integer values.
- `public Object obj` — An arbitrary object to send to the recipient. When using Messenger to send the message across
  processes.

For other data transfers, use `Message.setData(Bundle data)`.

### ///// References (online):

* [Multi-Threaded Android: Handler, Thread, Looper, and Message Queue](https://betterprogramming.pub/a-detailed-story-about-handler-thread-looper-message-queue-ac2cd9be0d78)
* [How Looper, MessageQueue, Handler work in Android](https://pivinci.medium.com/how-looper-messagequeue-handler-runnable-work-in-android-dbbe9db62094)
* [Looper, Handler, Thread & HandlerThread](https://medium.com/@kushaalsingla/looper-handler-thread-handlerthread-6dcbd999d192)
* [Android Handler Internals](https://medium.com/@jagsaund/android-handler-internals-b5d49eba6977)
* [MessageQueue and Looper in Android](https://medium.com/@ankit.sinhal/messagequeue-and-looper-in-android-3a18c7fc9181)
* [Handler in Android](https://medium.com/@ankit.sinhal/handler-in-android-d138c1f4980e)
* [Understanding of AsyncTask in Android](https://medium.com/@ankit.sinhal/understanding-of-asynctask-in-android-8fe61a96a238)

[^ up](#knowledge-notes)

***