## Java Coding Best Practice:

### JAVA SE design pitfall:
- String copy <= no need for immutable
- BigInteger add naming <= plus

### Item 3: Enforce the singleton property with a private constructor or an enum type
- A singleton is simply a class that is instantiated exactly once
- stateless object(a function) or intrinsically unique system component
- 2 Common impl. Both are based on keeping the constructor private and exporting a public static member to provide access to the sole instance.
  - public field
    - as final, always smae object
    - simple
  - static factory
    - change singleton to prototype without changing API
    - generic singleton factory
    - use the method reference as a supplier
  - To maintain the singleton guarantee,
declare all instance fields transient and provide a readResolve
method (Item 89).

- thrid way: Enum
  - when serializing an enum, field variables are not get serialized.


```java
// Enum singleton - the best way, against serialization or reflection
public enum Elvis {
    INSTANCE;
    public void leaveTheBuilding() { ... }
}


/* 1. public static final field
one caveat: a privileged client can invoke the
private constructor reflectively (Item 65) with the aid of the
AccessibleObject.setAccessible method.
*/
public class Elvis {
    public static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public void leaveTheBuilding() { ... }
}

/* 2. static factory method

*/
// Singleton with static factory
public class Elvis {
    private static final Elvis INSTANCE = new Elvis();
    private Elvis() { ... }
    public static Elvis getInstance() { return INSTANCE;}
    public void leaveTheBuilding() { ... }
}

// readResolve method to preserve singleton property
private Object readResolve() {
// Return the one true Elvis and let the garbage
collector
// take care of the Elvis impersonator.
    return INSTANCE;
}
```

```java
/*
The constructor for an enum type must be package-private or private access. It automatically creates the constants that are defined at the beginning of the enum body. You cannot invoke an enum constructor yourself.
*/
public enum Planet {
    MERCURY (3.303e+23, 2.4397e6),
    VENUS   (4.869e+24, 6.0518e6),
    EARTH   (5.976e+24, 6.37814e6),
    MARS    (6.421e+23, 3.3972e6),
    JUPITER (1.9e+27,   7.1492e7),
    SATURN  (5.688e+26, 6.0268e7),
    URANUS  (8.686e+25, 2.5559e7),
    NEPTUNE (1.024e+26, 2.4746e7);

    private double mass;   // in kilograms
    private double radius; // in meters
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }
    public double getMass() { return mass; }
    public double getRadius() { return radius; }
    public void setMass(double mass) {
        this.mass = mass;
    }
    
}
```

### Item 5: Prefer dependency injection to hardwiring resources
- Static utility classes and singletons assme there's **a singl implementation** which are inappropriate for classes whose behavior is parameterized by an underlying resource.
- dependency injection:
  - pass the resource into the constructor when creating a new instance.
    - pass a resource factory to the constructor
  - Autowired: Dagger, Guice, Spring 
- The Supplier<T> interface, introduced is perfect for representing factories.
    - constrain the factory’s type parameter using a bounded
wildcard type
```java
Mosaic create(Supplier<? extends Tile> tileFactory) {
... 
}
```


### Item 50: Make defensive copies when needed
- escaping reference
- do not use the clone method to
make a defensive copy of a parameter whose type is subclassable by
untrusted parties.
- defensive copy may be replaced by
documentation outlining

### Item 57: Minimize the scope of local variables

- declare it where it is first used.
- Nearly every local variable declaration should contain an initializer.
- prefer for loops to while loops => copy-and-paste error
- keep methods small and focused.

### Item 58: Prefer for-each loops to traditional for loops
- [x] iterate over any object that implements the Iterable interface,
*(if you are writing a type that represents a group of elements, you should strongly consider having it implement Iterable)*
- [x] can't use: 
- Destructive filtering—If you need to traverse a collection removing selected elements, 
then you need to use an explicit iterator so that you can call its remove method. 
You can often avoid explicit traversal by using Collection’s removeIf method, added in Java 8.
- Transforming to Array
- Parallel iteration
```java
// The preferred idiom for iterating over collections and arrays
for (Element e : elements) {  // “for each element e in elements."
... // Do something with e
}

// Preferred idiom for nested iteration on collections and arrays
for (Suit suit : suits)
    for (Rank rank : ranks)
        deck.add(new Card(suit, rank));
```
