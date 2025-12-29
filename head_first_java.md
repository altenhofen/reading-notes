# head first java

## a class has methods which have statements

## java passes primitive objects by value (or copy), and objects by reference (memory address)

## objects → allocated on the heap

## references → stored wherever the variable lives:
- local variables & parameters → stack
- fields of objects → heap
- static fields → method area / metaspace

stack frame for foo:
p  ─────► 0xA1B2

heap:
0xA1B2: Person
        name = "Bob"

# implicit and explicit conversions
```java
Animal a = new Dog(); // implicit (upcasting)
Dog d = (Dog) a;     // explicit (downcasting, risky)
```

# encapsulation
- protects an object’s state and exposes only what is necessary.
- improves maintainability and readability by defining a clear public API.

```java
private int height;

public setHeight(int height) {
    if (height <= 0) {
        throw new IllegalArgumentException("Height cannot be %d, expected 1 or greater".formatted(height))
    }
   this.height = height;
}
```

# empty constructors are implicit
>an empty, no-argument constructor is implicitly provided by the compiler 
**if and only if you do not explicitly define any other constructors in your class**


# getters dont need this.instancevariable
```java 
public int getHeight() {
    return height;
}

// is the same as 
public int getHeight() {
    return this.height;
}
```

but, in constructors or setters, when the argument
to the method has the same name as an instance variable
you need the this.

```java
public Dog(int height) {
    this.height = height;
}
```

# instance variables are declared within a class
# local variables within a method
- local variables need to be initialized before use, otherwhise it doesn't compile



# polymorphism
- you have an interface/abstract class representing the abstract whole
- you have subclasses representing the concrete

```
        class Shape
              v        
class Square  - class Circle
```


# life and death of an object
- creates the object with `new`
- memory is allocated on the heap
- initializes instance variables to default values (0, false, null...)
- runs the constructor, if there s none, no-args ctor is implicit
- ctors can call other ctors using this()
- may call a superclass ctor using super()
## death
objects are alive until they have a reachable reference

# the superclass parts need to be fully built to start constructing the child


# Three ways to get rid of an object’s reference:
1. The reference goes out of scope, permanently
```java
void go() {
    Life z = new Life();
}
```
>'z' dies at the end of the method

2. Reference is assigned to other object
```java
Life z = new Life();
z = new Life();
```

3. Reference is explicitly set to null
```java
Life z = null;
```

# `static final` variables are constants, they're named with `FOO_X` casing

# static initializers
```java
static {
BAR_SIGN = (double) Math.random();
}
```

# final is for immutable variables


# static methods can be called on an instance or just using the class "namespace"

```java
var st = new StaticThingy();
st.printSomething("this");
StaticThingy.printSomething("that");
```
 
# runnable, threads and t.start()
```java
Thread t = new Thread(new myClass());
t.start() // creates the thread, asks to the jvm to schedule it
```
JVM will then call run

## you can also set priority 
```java
t.setPriority(Thread.MAX_PRIORITY);
```

## thread states
- NEW → created but not started
- RUNNABLE → ready or running
- BLOCKED / WAITING / TIMED_WAITING
- TERMINATED


# synchronized
- synchronized is a mutual exclusion (mutex) mechanism.
- ensures only one thread at a time can access a critical section of code.
- prevents race conditions when multiple threads access shared data.
- also provides visibility guarantees (changes made by one thread become visible to others).

```java
public static synchronized void log() {
    // locks Counter.class
}
// or synchronized block
void inc() {
    synchronized (this) {
        count++;
    }
}
```

## design choices
**Preferred design hierarchy (rule of thumb)**
### From best → last resort:
1.Immutable objects
2.Thread confinement (one thread owns the data)
3.Concurrent collections / atomics
4.ConcurrentHashMap
5.AtomicInteger
(worth revisiting the design)
6.Fine-grained synchronization
7.Coarse-grained synchronized

## when to use synchronized 
- Protecting a small, well-defined invariant
- The critical section is short
- Contention is low
- Simplicity matters more than scalability
```java
public synchronized int nextId() {
    return ++id;
}
```

##  you feel forced to add lots of synchronized, that’s usually a signal to:
- reduce mutability
- clarify ownership
- prefer immutability or message passing

# data structures
## TreeSet
- Keeps the elements sorted and prevents duplicates.
## HashMap
- Let’s you store and access elements as name/value pairs.
## LinkedList
- Designed to give better performance when you insert or delete elements from the middle of the collection. (In practice, an ArrayList is still usually what you want.)
## HashSet
Prevents duplicates in the collection, and given an element, can find that element in the collection quickly.
## LinkedHashMap
Like a regular HashMap, except it can remember the order in which elements (name/value pairs) were inserted, or it can be configured to remember the order in which elements were last accessed.

# collection hierarchy
```
Collection Framework (partial)


Collection (interface)
├── Set (interface)
│   ├── SortedSet (interface)
│   │   └── TreeSet (class)
│   ├── HashSet (class)
│   └── LinkedHashSet (class)
│
└── List (interface)
    ├── ArrayList (class)
    ├── LinkedList (class)
    └── Vector (class)


Map hierarchy (part of Collection Framework, but NOT Collection)

Map (interface)
├── SortedMap (interface)
│   └── TreeMap (class)
├── HashMap (class)
├── LinkedHashMap (class)
└── Hashtable (class)
```

# overriding hashcode and equals

```java
a == b       // same object?
a.equals(b) // same meaning/value?
```

## When you must override equals()
you override equals() when:
two different objects should be considered equal based on their data
Example: two User objects with same id

```java
User u1 = new User(1);
User u2 = new User(1);

u1.equals(u2); // should be true
```

# Contract of equals()
**An overridden equals() must be:**
- Reflexive: x.equals(x) → true
- Symmetric: x.equals(y) = y.equals(x)
- Transitive: if x.equals(y) and y.equals(z) → x.equals(z)
- Consistent: same result unless data changes
- Non-null: x.equals(null) → false

# hashCode() — what it’s for
**hashCode() returns an int used by hash-based collections:**
- HashMap
- HashSet
- Hashtable

### If two objects are equal according to equals(), they MUST have the same hashCode()

| What you override | Result                          |
| ----------------- | ------------------------------- |
| `equals()` only   | [X] Hash collections break        |
| `hashCode()` only | [X] `equals()` logic inconsistent |
| Both              | [V] Correct                       |


import java.util.Objects;

# correct override example
```java
class User {
    private final int id;
    private final String name;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof User)) return false;
        User user = (User) o;
        return id == user.id &&
               Objects.equals(name, user.name);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id, name);
  }
``` 

# generic inherintance

```java
public void takeAnimals(ArrayList<? extends Animal> animals) {
    for(Animal a: animals) {
        a.eat();
    }
}
```

**could also work as `<T extends Animal>` but then... would you use the T generic? if not use `?`.**

# compiling classes
