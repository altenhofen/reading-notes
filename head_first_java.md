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
