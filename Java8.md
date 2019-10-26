
### Building block
- [x] scope
- a method parameter: is also local to the method.

- classes are the basic building blocks. 
  - When defining a class, you describe all the parts and characteristics of one of those building blocks.
  - You can even put two classes in the same fi le. When you do so, at most one of the classes in the file is allowed to be **public**.
- An object is a runtime instance of a class in memory.

- [x] main()
- A Java program begins execution with its main() method. 
  - A main() method is the gateway between the startup of a Java process, which is managed by the Java Virtual Machine (JVM), and the beginning of the programmer’s code.
- An array is a fixed-size list of items that are all of the same type.
- JDK to compile, JRE to run

- [x] import
- PIC (picture): package, import, and class
- Java classes are grouped into packages
- multiple classes can be defined in the same file, but only one of them is allowed to be public. 
  - The public class matches the name of the file.
  - A file is also allowed to have neither class be public
  
- The * is a wildcard that matches all classes in the package. 
  - It doesn’t import child packages, fields, or methods. 
  - The compiler figures out what's actually needed. 
  - you can only have one wildcard and it must be at the end

- Java automatically looks in the current package for other classes.
- If you explicitly import a class name, it takes precedence over any wildcards present.

- [x] compile to class file
```bash
javac packagea/ClassA.java packageb/ClassB.java
# to run:
java packageb.ClassB
# add to class path
java -cp ".:/tmp/someOtherLocation:/tmp/myJar.jar" myPackage.MyClass
```

- [x] Java feature
- Object Oriented: Java is an object-oriented language, which means all code is defined in classes and most of those classes can be instantiated into objects.
  - Java allows for functional programming within a class, but object oriented is still the main organization of code.
- Encapsulation: Java supports access modifiers to protect data from unintended access and modification.
- Platform Independent: Java is an interpreted language because it gets compiled to bytecode. 
  - “write once, run everywhere.”
- Robust: One of the major advantages of Java over C++ is that it prevents memory leaks. Java manages memory on its own and does garbage collection automatically.
- Simple: Java was intended to be simpler than C++. In addition to eliminating pointers, it got rid of operator overloading.
- Secure: Java code runs inside the JVM. This creates a sandbox that makes it hard for Java code to do evil things to the computer it is running on.


- [x] declare and initialize

- order:
  - Fields and instance initializer blocks are run in the order in which they appear in the file. 
  - The constructor runs after all fields and instance initializer blocks have run. .
- Order matters for the fi elds and blocks of code. You can’t refer to a variable before its declaration

- [x] reference type VS primitives
- Primitive
  - byte: 8bit; short/char: 2 bytes; int/float: 4b; long/double: 8b
  - int size:  around -2B to 2B
- When a number is present in the code, it is called a literal
  - You can add underscores anywhere except at the beginning of a literal, the end of a literal, right before a decimal point, or right after a decimal point.
  - digit format:
    - octal (digits 0–7), which uses the number 0 as a prefix—for example, 017 
    - hexadecimal (digits 0–9 and letters A–F), which uses the number 0 followed by x or X . as a prefix—for example, 0xFF
    - binary (digits 0–1), which uses the number 0 followed by b or B as a prefix—for example, 0b10
- Reference
  - Unlike other languages, Java does not allow you to learn what the physical memory address is.  

- You can declare many variables in the same declaration as long as they are all of the same type. You can also initialize any or all of those values inline.
  - The shortcut to declare multiple variables in the same statement only works when they share a type.

- legal identifier
  - The name must begin with a letter or the symbol $ or _.
  - Subsequent characters may also be numbers.
  - You cannot use the same name as a Java reserved word.
  - conventions: 
    - Method and variables names begin with a lowercase letter followed by CamelCase
    - Don’t start any identifi ers with $. The compiler uses this symbol for some files.

- A local variable is a variable defi ned within a method. Local variables must be initialized efore use. They do not have a default value and contain garbage data until initialized.
- Instance and class variables do not require you to initialize them. As soon as you declare these variables, they are given a default value.
  - false, 0, 0.0, '\u0000'(NUL), null


- [x] read/write fields
- [x] Object's Lifecycle
- All Java objects are stored in your program memory’s heap. 
  - The heap, which is also referred to as the free store, represents a large pool of unused memory allocated to your Java application.
  - An object sits on the heap and does not have a name.
  - An object cannot be assigned to anothermobject, nor can an object be passed to a method or returned from a method.
- System.gc() is not guaranteed to run, and you should be able to recognize when objects become eligible for garbage collection.
- finalize() might not get called and that it definitely won’t be called twice.

```text
In this chapter, you saw that Java classes consist of members called fi elds and methods. An
object is an instance of a Java class. There are three styles of comment: a single-line comment
(//), a multiline comment (/* */), and a Javadoc comment (/** */).
Java begins program execution with a main() method. The most common signature for
this method run from the command line is public static void main(String[] args).
Arguments are passed in after the class name, as in java NameOfClass firstArgument.
Arguments are indexed starting with 0.
Java code is organized into folders called packages. To reference classes in other packages,
you use an import statement. A wildcard ending an import statement means you want
to import all classes in that package. It does not include packages that are inside that one.
java.lang is a special package that does not need to be imported.
Constructors create Java objects. A constructor is a method matching the class name and
omitting the return type. When an object is instantiated, fi elds and blocks of code are
initialized fi rst. Then the constructor is run.
Primitive types are the basic building blocks of Java types. They are assembled into
reference types. Reference types can have methods and be assigned to null. In addition to
“normal” numbers, numeric literals are allowed to begin with 0 (octal), 0x (hex), 0X (hex),
0b (binary), or 0B (binary). Numeric literals are also allowed to contain underscores as long
as they are directly between two other numbers.
Declaring a variable involves stating the data type and giving the variable a name.
Variables that represent fi elds in a class are automatically initialized to their corresponding
“zero” or null value during object instantiation. Local variables must be specifi cally
initialized. Identifi ers may contain letters, numbers, $, or _. Identifi ers may not begin with
numbers.
Scope refers to that portion of code where a variable can be accessed. There are three
kinds of variables in Java, depending on their scope: instance variables, class variables, and
local variables. Instance variables are the nonstatic fi elds of your class. Class variables are
the static fi elds within a class. Local variables are declared within a method.
For some class elements, order matters within the fi le. The package statement comes fi rst
if present. Then comes the import statements if present. Then comes the class declaration.
Fields and methods are allowed to be in any order within the class.
Garbage collection is responsible for removing objects from memory when they can
never be used again. An object becomes eligible for garbage collection when there are no
more references to it or its references have all gone out of scope. The finalize() method
will run once for each object if/when it is fi rst garbage collected.
Java code is object oriented, meaning all code is defi ned in classes. Access modifi ers
allow classes to encapsulate data. Java is platform independent, compiling to bytecode. It is
robust and simple by not providing pointers or operator overloading. Finally, Java is secure
because it runs inside a virtual machine.
```

### Operators and statements
- Arithmetic operators are often encountered in early mathematics and include addition (+), subtraction (-), multiplication (*), division (/), and modulus (%).
- Numeric Promotion Rules
  - If two values have different data types, Java will automatically promote one of the values to the **larger** of the two data types.
  - If one of the values is integral and the other is floating-point, Java will automatically
promote the integral value to the **floating-point** value’s data type.
  - Smaller data types, namely byte, short, and char, are first promoted to int any time they’re used with a Java binary arithmetic operator, even if neither of the operands is int.
    - **unary** operators are excluded from this rule.
  - After all promotion has occurred and the operands have the same data type, the resulting value will have the same data type as its promoted operands.
- The literal would need a postfi x L to be considered a long.
-  **Overflow** is when a number is so large that it will no longer fi t within the data type, so the system “wraps around” to the next lowest value and counts up from there. There’s also an analogous underfl ow, when the number is too low to fi t in the data type.  
- Compound operators are useful for more than just shorthand—they can also save us from having to explicitly cast a value.
```java
long x = 10;
int y = 5;
y = y * x; // DOES NOT COMPILE
y *= x; // fine
```
- the result of the assignment is an expression in and of itself, equal to the value of the assignment.

- Exclusive OR is only true if the operands are different.
- A more common example of where short-circuit operators are used is checking for null objects before performing an operation
- Comparing two numeric primitive types. If the numeric values are of different data types, the values are automatically promoted as previously described.
- As of Java 7, only one of the right-hand expressions of the ternary operator will be evaluated at runtime.

- switch
  - Data types supported by switch statements include the following:
    - int and Integer
    - byte and Byte
    - short and Short
    - char and Character
    - int and Integer
    - String
    - enum values
    - **not** support **boolean and long**, and their associated wrapper classes
  - The values in each **case statement must be compile-time constant values(no func param)** of the same data type as the switch value. This means you can use only literals, enum constants, or final constant variables of the same data type.
```java
private int getSortOrder(String firstName, final String lastName) {
    String middleName = "Patricia";
    final String suffix = "JR";
    int id = 0;
    switch (firstName) {
        case "Test":
            return 52;
        case middleName: // DOES NOT COMPILE
            id = 5;
            break;
        case suffix:
            id = 0;
            break;
        case lastName: // DOES NOT COMPILE
            id = 8;
            break;
        case 5: // DOES NOT COMPILE
            id = 7;
            break;
        case 'J': // DOES NOT COMPILE
            id = 10;
            break;
        case java.time.DayOfWeek.SUNDAY: // DOES NOT COMPILE
            id = 15;
            break;
    }
    return id;
}

do {
   x--;
} while(x > 10);
```
- Unlike a while loop, though, a do-while loop guarantees that the statement or block will be executed at least once.

- For
  - **2 semicolons** separating the three sections are required, as for( ; ) and for() will not compile
  - The variables in the initialization block must all be of the **same type**.
  - The right-hand side of the for-each loop statement must be a built-in Java array or an object whose class implements java.lang.Iterable,

```
This chapter covered a wide variety of topics, including dozens of Java operators, along
with numerous control fl ow statements. Many of these operators and statements may have
been new to you.
It is important that you understand how to use all of the required Java operators covered
in this chapter and know how operator precedence infl uences the way a particular expression
is interpreted. There will likely be numerous questions on the exam that appear to test
one thing, such as StringBuilder or exception handling, when in fact the answer is related
to the misuse of a particular operator that causes the application to fail to compile. When
you see an operator on the exam, always check that the appropriate data types are used and
that they match each other where applicable.
For statements, this chapter covered two types of control structures: decision-making
controls structures, including if-then, if-then-else, and switch statements, as well as
repetition control structures including for, for-each, while, and do-while. Remember that
most of these structures require the evaluation of a particular boolean expression either for
branching decisions or once per repetition. The switch statement is the only one that supports
a variety of data types, including String variables as of Java 7.
With a for-each statement you don’t need to explicitly write a boolean expression, since
the compiler builds them implicitly. For clarity, we referred to an enhanced for loop as a
for-each loop, but syntactically they are written as a for statement.
We concluded this chapter by discussing advanced control options and how fl ow can be
enhanced through nested loops, break statements, and continue statements. Be wary of
questions on the exam that use nested statements, especially ones with labels, and verify
they are being used correctly.
This chapter is especially important because at least one component of this chapter will
likely appear in every exam question with sample code. Many of the questions on the exam
focus on proper syntactic use of the structures, as they will be a large source of questions
that end in “Does not compile.” You should be able to answer all of the review questions
correctly or fully understand those that you answered incorrectly before moving on to later
chapters.
```

### Core API
- API stands for application programming interface

#### String
- String class is special and doesn’t need to be instantiated with new.
- Placing one String before the other String and combining them together is called string concatenation
  - 1. If both operands are numeric, + means numeric addition.
  - 2. If either operand is a String, + means concatenation.
  - 3. The expression is evaluated left to right.
-  The **string pool** contains literal values that appear in your program. 
  - Strings are immutable and literals are pooled. The JVM created only one literal in memory.
  - as 24-40% memeory are used by Strings, and mostly repeating common ones.
  - Strings not in the string pool are garbage collected just like any other object
- Methods: length(), charAt(), indexOf(), substring(), toLowerCase()/toUpperCase(), equals()/equalsIgnoreCase(), startWith()/endsWith(), contains(), replace(), trim()(whitespace: space, tab, newline), 
- Size is the number of characters currently in the sequence, and capacity is the number of characters the sequence can currently hold.

- StringBuilder
  - charAt(), indexOf(), length(), and substring(); append(), insert(), delete(), deleteCharAt(), reverse(), toString()
  - same Object's equals()
  
#### Arrays
```java
// you can type the [] before or after the name, and adding a space is optional
int[] numbers1 = new int[3];
int[] numbers2 = new int[] {42, 55, 99};
int[] numbers2 = {42, 55, 99}; // anonymous array

String [][] rectangle = new String[3][2];
int[][] differentSize = {{1, 4}, {3}, {9,8,7}};
int [][] args = new int[4][];
args[0] = new int[5];
args[1] = new int[3];
```
- The equals() method on arrays does not look at the elements of the array.
- java.util.Arrays.toString(bugs) would print \[cricket, beetle, ladybug].
- String sorts in alphabetic order, and 1 sorts before 9. (Numbers sort before letters and uppercase sorts before lowercase)

#### ArrayList
- add(), remove(), set(), isEmpty()/size(), clear(), contains(), equals(), 

- The parse methods, such as parseInt(), return a primitive, and the valueOf() method returns a wrapper class
  - the Character class doesn’t participate in the parse/valueOf methods.
```java
List<Integer> numbers = new ArrayList<>();
numbers.add(1);
numbers.add(2);
numbers.remove(1); // autoboxing
System.out.println(numbers);


Object[] objectArray = list.toArray();
System.out.println(objectArray.length); // 2
String[] stringArray = list.toArray(new String[0]);  // If you like, you can suggest a larger array to be used instead.

List<String> list = Arrays.asList(array); // returns fixed size list
List<String> list = Arrays.asList("one", "two");
```

- Arrays.asList()
  - The original array and created array backed List are **linked**. When a change is made to one, it is available in the other. It is a **fixed-size** list and is also known a backed List because the array changes with it.


- For months in the new date and time methods, Java counts starting from 1 like we human beings do.
- The date and time classes are immutable, just like String was.
  - The date and time classes have private constructors to force you to use the static methods.
- LocalDate has toEpochDay(), which is the number of days since January 1, 1970.
- LocalDateTime has toEpochTime(), which is the number of seconds since January 1, 1970.
- You cannot chain methods when creating a Period.
- For Duration, you can specify the number of days, hours, minutes, seconds, or nanoseconds.
- DateTimeFormatter can be used to format any type of date and/or time object.

```
In this chapter, you learned that Strings are immutable sequences of characters. The new
operator is optional. The concatenation operator (+) creates a new String with the content
of the fi rst String followed by the content of the second String. If either operand
involved in the + expression is a String, concatenation is used; otherwise, addition is used.
String literals are stored in the string pool. The String class has many methods. You need
to know charAt(), concat(), endsWith(), equals(), equalsIgnoreCase(), indexOf(),
length(), replace(), startsWith(), substring(), toLowerCase(), toUpperCase(), and
trim().
StringBuilders are mutable sequences of characters. Most of the methods return a
reference to the current object to allow method chaining. The StringBuilder class has
many methods. You need to know append(), charAt(), delete(), deleteCharAt(),
indexOf(), insert(), length(), reverse(), substring(), and toString(). StringBuffer
is the same as StringBuilder except that it is thread safe.
Calling == on String objects will check whether they point to the same object in the
pool. Calling == on StringBuilder references will check whether they are pointing to the
same StringBuilder object. Calling equals() on String objects will check whether the
sequence of characters is the same. Calling equals() on StringBuilder objects will check
whether they are pointing to the same object rather than looking at the values inside.
An array is a fi xed-size area of memory on the heap that has space for primitives or
pointers to objects. You specify the size when creating it—for example, int[] a = new
int[6];. Indexes begin with 0 and elements are referred to using a[0]. The Arrays.sort()
method sorts an array. Arrays.binarySearch() searches a sorted array and returns the
index of a match. If no match is found, it negates the position where the element would
need to be inserted and subtracts 1. Methods that are passed varargs (…) can be used as
if a normal array was passed in. In a multidimensional array, the second-level arrays and
beyond can be different sizes.
An ArrayList can change size over its life. It can be stored in an ArrayList or List
reference. Generics can specify the type that goes in the ArrayList. You need to know the
methods add(), clear(), contains(), equals(), isEmpty(), remove(), set(), and size().
Although an ArrayList is not allowed to contain primitives, Java will autobox parameters
passed in to the proper wrapper type. Collections.sort() sorts an ArrayList.
A LocalDate contains just a date, a LocalTime contains just a time, and a LocalDateTime
contains both a date and time. All three have private constructors and are created using
LocalDate.now() or LocalDate.of() (or the equivalents for that class). Dates and times
can be manipulated using plusXXX or minusXXX methods. The Period class represents a
number of days, months, or years to add or subtract from a LocalDate or LocalDateTime.
DateTimeFormatter is used to output dates and times in the desired format. The date and
time classes are all immutable, which means the return value must be used.
```
#### Methods
- Access
  - public The method can be called from any class.
  - private The method can only be called from within the same class.
  - protected The method can only be called from classes in the **same package** or subclasses.
  - Default (Package Private) Access The method can only be called from classes in the same
package.
- Optional Specifiers
  - static: Used for class methods.
  - abstract: Used when not providing a method body. 
  - final: Used when a method is not allowed to be overridden by a subclass.
  - synchronized
  - native: Used when interacting with code written in another language such as C++.
  - strictfp: Used for making fl oating-point calculations portable.
  - Java allows the optional specifi ers to appear before the access modifi er.
- return
  - a method must have a return type. If no value is returned, the return type is void. You cannot omit the return type.
- Method Name
- Parameter List
- Optional Exception List
- Method Body


- Varargs
  - A vararg parameter must be the last element in a method’s parameter list. This implies you are only allowed to have one vararg parameter per method.
  - When calling a method with a vararg parameter, you have a choice. You can pass in an array, or you can list the elements of the array and let Java create it for you. You can even omit the vararg values in the method call and Java will create an array of length zero for you.
  - Java will create an empty array if no parameters are passed for a vararg. However, it is still possible to pass null explicitly


#### Access Modifiers
- private: Only accessible within the same class
- default (package private) access: private and other classes in the same package
- protected: default access and child classes
- public: protected and classes in the other packages

- Extending means creating a ubclass that has access to any protected or public members of the parent class.
- protected **also** gives us access to everything that **default access does**.

#### static
- code VS data
```
Each class has a copy of the instance variables. There is only one copy of the code for the
instance methods. Each instance of the class can call it as many times as it would like.
However, each call of an instance method (or any method) gets space on the stack for
method parameters and local variables.
The same thing happens for static methods. There is one copy of the code. Parameters
and local variables go on the stack.
Just remember that only data gets its “own copy.” There is no need to duplicate copies of
the code itself.
```
- You can use an instance of the object/class to call a static method.
  - the reference may assigned as null, still fine
- “member” means field or method.
- static final constants use a different naming convention than other variables. They use all uppercase letters with underscores between “words.”
- The static initializer runs when the class is fi rst used. The statements in it run and assign any static variables as needed.
  - Using static and instance initializers can make your code much harder to read. Everything that could be done in an instance initializer could be done in a con structor instead.
  - There is a common case to use a static initializer: when you need to initialize a static fi eld and the code to do so requires more than one line.
  
- static import
  - Regular imports are for importing classes. Static imports are for importing static members of classes.
  - Java would give it preference over the imported one and the method we coded would be used.
  - ```java import static java.util.Arrays.asList;```

- Java is a “pass-by-value” language. This means that a copy of the variable is made and the method receives that copy. Assignments made in the method do not affect the caller.

#### Overloading Methods
- Method overloading occurs when there are different **method signatures** with the same name but different type parameters.
- Java tries to use the most specific parameter list it can find to match func. Order(no two-step conversion):
  - Exact match by type public String glide(int i, int j) {}
  - Larger primitive type public String glide(long i, long j) {}
  - Autoboxed type  public String glide(Integer i, Integer j) {}
  - Varargs: public String glide(int... nums) {}
- Note that Java can only accept wider types. An int can be passed to a method taking a long parameter.
```java

// Java treats varargs as if they were an array.
public void fly(int[] lengths) { }
public void fly(int... lengths) { }
// DOES NOT COMPILE
```
#### Constructor
- default class was added during the compile step
- Having a private constructor in a class tells the compiler not to provide a default no argument constructor. It also prevents other classes from instantiating the class. This is useful when a class only has static methods or the class wants to control all calls to create new instances of itself.
- this(): If you choose to call it, the this() call must be the fi rst noncommented statement in the constructor
- constructor chaining: One common technique to have each constructor add one parameter until getting to the constructor that does all the work. This approach is called constructor chaining.
- The constructor is part of the initialization process, so it is allowed to assign **final** instance variables in it. By the time the constructor completes, all final instance variables must have been set.
- Order of Initialization
  - 1. If there is a superclass, initialize it first 
  - 2. Static variable declarations and static initializers in the order they appear in the file.
  - 3. Instance variable declarations and instance initializers in the order they appear in the file.
  - 4. The constructor.

- **Encapsulation** means we set up the class so only methods in the class with the variables can refer to the instance variables.
- **immutable** is only measured after the object is constructed. Immutable classes are allowed to have values. They just can't change after instantiation.
  - defensive copy
- encapsulation refers to preventing callers from changing the instance variables directly. Immutability refers to preventing callers from changing the instance variables at all.

#### Lambda
- Functional program ming is a way of writing code more declaratively.
  - no obj state
- A lambda expression is a block of code that gets passed around.
  - code as variable
  - anonymous method/closures
  - Deferred execution means that code is specifi ed now but will run later.

##### lambda 
```
Parameter types are optional. Braces and the return keyword are optional
when the body is a single statement. Parentheses are optional when only one parameter is
specifi ed and the type is implicit.
```

- the parentheses are only optional when there is one parameter and it doesn’t have a type declared.
- A body that has one or more lines of code, including a semicolon and a return statement
- The parentheses can only be omitted if there is a single parameter and its type is not explicitly stated.
- no need return or use a semicolon when no braces are used.
- there isn’t a rule that says you must use all defi ned parameters.
- Java doesn’t allow us to redeclare a local variable,
```java
a -> a.canHop()
(Animal a) -> { return a.canHop(); }

print((String a, String b) -> a.startsWith("test"));
```

- Predicates: 
```java
public interface Predicate<T> {
   boolean test(T t);
}

//ArrayList declares a removeIf()
bunnies.removeIf(s -> s.charAt(0) != 'h');
```

```
As you learned in this chapter, Java methods start with an access modifi er of public,
private, protected or blank (default access). This is followed by an optional specifi er such
as static, final, or abstract. Next comes the return type, which is void or a Java type.
The method name follows, using standard Java identifi er rules. Zero or more parameters go
in parentheses as the parameter list. Next come any optional exception types. Finally, zero
or more statements go in braces to make up the method body.
Using the private keyword means the code is only available from within the same class.
Default (package private) access means the code is only available from within the same
package. Using the protected keyword means the code is available from the same package
or subclasses. Using the public keyword means the code is available from anywhere. Static
methods and static variables are shared by the class. When referenced from outside the
class, they are called using the classname—for example, StaticClass.method(). Instance
members are allowed to call static members, but static members are not allowed to call
instance members. Static imports are used to import static members.
Java uses pass-by-value, which means that calls to methods create a copy of the parameters.
Assigning new values to those parameters in the method doesn’t affect the caller’s variables.
Calling methods on objects that are method parameters changes the state of those objects and
is refl ected in the caller.
Overloaded methods are methods with the same name but a different parameter list.
Java calls the most specifi c method it can fi nd. Exact matches are preferred, followed by
wider primitives. After that comes autoboxing and fi nally varargs.
Constructors are used to instantiate new objects. The default no-argument constructor
is called when no constructor is coded. Multiple constructors are allowed and can call each
other by writing this(). If this() is present, it must be the fi rst statement in the constructor.
Constructors can refer to instance variables by writing this before a variable name to indicate
they want the instance variable and not the method parameter with that name. The order
of initialization is the superclass (which we will cover in Chapter 5); static variables and static
initializers in the order they appear; instance variables and instance initializers in the order
they appear; and fi nally the constructor.
Encapsulation refers to preventing callers from changing the instance variables directly.
This is done by making instance variables private and getters/setters public. Immutability
refers to preventing callers from changing the instance variables at all. This uses several
techniques, including removing setters. JavaBeans use methods beginning with is and get
for boolean and non-boolean property types, respectively. Methods beginning with set are
used for setters.
Lambda expressions, or lambdas, allow passing around blocks of code. The full syntax
looks like (String a, String b) -> { return a.equals(b); }. The parameter types can
be omitted. When only one parameter is specifi ed without a type, the parentheses can also
be omitted. The braces and return statement can be omitted for a single statement, making
the short form (a -> a.equals(b). Lambdas are passed to a method expecting an interface
with one method. Predicate is a common interface. It has one method named test
that returns a boolean and takes any type. The removeIf() method on ArrayList takes a
Predicate.
```
### Class Design
#### class
- At its core, proper Java class design is about code reusability, increased functionality,
and standardization.
- Inheritance is the process by which the new child subclass automatically includes any public or protected primitives, objects, or methods defined in the parent class.
- Java supports single inheritance and multiple level of inheritance
- child class contains parent's private variable, just can't access
- only public and package- default can apply to top-level class
- the compiler has been automatically inserting "extends Object" into any class you write that doesn’t extend a specific class.

- final:
  - final to prevent a class from being extended
  - final methods cannot be overridden
    - to guarantee certain behavior of a method in the parent class, regardless of which child is invoking the method. templ?

#### constructor
- Java compiler automatically inserts a call to the no-argument constructor super() if the fi rst statement is not a call to the parent constructor
- if parent class doesn’t have a no-argument constructor: you must create at least one constructor in your child class that explicitly calls a parent constructor via the super() command.

- **Constructor Definition Rules**:
  - 1. The first statement of every constructor is a call to another constructor within the class using this(), or a call to a constructor in the direct parent class using super().
  - 2. The super() call may not be used after the first statement of the constructor.
  - 3. If no super() call is declared in a constructor, Java will insert a no-argument super() as the first statement of the constructor.
  - 4. If the parent doesn’t have a no-argument constructor and the child doesn’t define any constructors, the compiler will throw an error and try to insert a default no-argument constructor into the child class.
  - 5. If the parent doesn’t have a no-argument constructor, the compiler requires an explicit call to a parent constructor in each child constructor.

- In Java, the parent constructor is always executed before the child con structor.
- You may also use this to access members of the parent class that are accessible from the child class, since a child class inherits all of its parent members.
- super() VS super
  - super(), is a statement that explicitly calls a parent constructor and may only be used in the fi rst line of a constructor of a child class. 
  - super, is a keyword used to reference a member defi ned in a parent class and may be used throughout the child class.

### Inherting method
- The compiler performs the following checks when you **override a nonprivate method**:
  - 1. The method in the child class must have the same signature as the method in the parent class.
  - 2. The method in the child class must be at least as accessible or more accessible than the method in the parent class.
  - 3. The method in the child class may not throw a checked exception that is new or broader than the class of any exception thrown in the parent class method.
  - 4. If the method returns a value, it must be the same or a subclass of the method in the parent class, known as covariant return types.

- It is **not possible** in Java to defi ne two methods in a class with the same name and input parameters but **different return types**.
- At runtime the child version of an overridden method is always executed for an instance regardless of whether the method call is defi ned in a parent or child class method.

- a child method may hide or eliminate a parent method’s exception without issue.
- an overloaded method will use a different signature than an overridden method.
- In Java, it is not possible to override a private method in a parent class since the parent method is not accessible from the child class.

- A hidden method occurs when a child class defi nes a static method with the same name and signature as a static method defi ned in a parent class.
  - 5 rules:
    - first four are the same as the rules for overriding a method
    - The method defined in the child class must be marked as static if it is marked as static in the parent class (method hiding). Likewise, the method must not be marked as static in the child class if it is not marked as static in the parent class (method overriding).
  - avoid hiding static methods in your own work, since it tends to lead to confusing and difficult-to-read code. You should not reuse the name of a static method in your class if it is already used in the parent class.
- at runtime the parent version of a hidden method is always executed if the call to the method is defi ned in the parent class.
- Don’t Hide Variables in Practice

- An abstract class is a class that is marked with the abstract keyword and cannot be instantiated.
  - an abstract class is not required to include any abstract methods.
  - cannot mark as final
- An abstract method is a method marked with the abstract keyword defi ned in an abstract class, for which no implementation is provided in the class in which it is declared.
  - **an abstract method may only be defined in an abstract class**
  - a method may **not** be marked as both **abstract and private**.
  - Implementing an abstract method in a subclass follows the same rules for overriding a method.

- A concrete class is the first nonabstract subclass that extends an abstract class and is required to **implement all** inherited abstract methods.

#### interface
- An interface is an **abstract** data type that defines a list of abstract public methods that any class implementing the interface must provide. An interface can also include a list of constant variables and default methods
- All top-level interfaces are assumed to have public or default access, and they must include the abstract modifier in their definition
- All nondefault methods in an interface are assumed to have the modifiers **abstract and public** in their definition.
- Interface variables are assumed to be **public, static, and final**.
- common coding practice to use uppercase letters to denote constant values within a class.

- A **default method** within an interface defines an abstract method with a default imple mentation. In this manner, classes have the option to override the default method if they need to, but they are not required to do so.
  - 1. A default method may **only be declared within an interface** and not within a class or abstract class.
  - 2. A default method must be marked with the default keyword. If a method is marked as default, it must provide a method **body**.
  - 3. A default method is not assumed to be static, final, or abstract, as it may be used or overridden by a class that implements the interface.
  - 4. Like all methods in an interface, a default method is **assumed to be public** and will not compile if marked as private or protected.
  - the interface may redeclare default method as abstract, requiring classes that implement the new interface to explicitly provide a method body
  - having a class that implements or inherits two duplicate default methods forces the class to implement a new version of the method, or the code will not compile.

- A static method defi ned in an interface is **not inherited** in any classes that implement the interface. To reference the static method, a reference to the name of the **interface must be used**.
- a class that implements two interfaces containing static methods with the same signature will still compile at runtime,

#### Polymorphism
- polymorphism: the property of an object to take on many different forms.
##### reference type
- Regardless of the type of the reference you have for the object in memory, the object itself doesn’t change
- 1. The type of the object determines which properties exist within the object in memory.
- 2. The type of the reference to the object determines which methods and variables are accessible to the Java program.
- instanceof operator can be used to check whether an object belongs to a particular class and to prevent ClassCastExceptions at runtime.
##### overriding
- A virtual method is a method in which the specific implementation is not determined until runtime
- if you call a method on an object that overrides a method, you **get the overridden method**, even if the call to the method is **on a parent reference** or within the parent class.
- polymorphic parameters of a method: an explicit cast is not required for casting from a subtype to a supertype
- @Override
```
This chapter took the basic class structure we presented in Chapter 4 and expanded it by
introducing the notion of inheritance. Java classes follow a multilevel single-inheritance
pattern in which every class has exactly one direct parent class, with all classes eventually
inheriting from java.lang.Object. Java interfaces simulate a limited form of multiple
inheritance, since Java classes may implement multiple interfaces.
Inheriting a class gives you access to all of the public and protected methods of the
class, but special rules for constructors and overriding methods must be followed or the
code will not compile. For example, if the parent class doesn’t include a no-argument constructor,
an explicit call to a parent constructor must be provided in the child’s constructors.
Pay close attention on the exam to any class that defi nes a constructor with arguments
and doesn’t defi ne a no-argument constructor.
We reviewed overloaded, overridden, and hidden methods and showed how they differ,
especially in terms of polymorphism. We also introduced the notion of hiding variables,
although we strongly discourage this in practice as it often leads to confusing, diffi cult-tomaintain
code.
We introduced abstract classes and interfaces and showed how you can use them to
defi ne a platform for other developers to interact with. By defi nition, an abstract type cannot
be instantiated directly and requires a concrete subclass for the code to be used. Since
default and static interface methods are new to Java 8, expect to see at least one question
on them on the exam.
Finally, this chapter introduced the concept of polymorphism, central to the Java language,
and showed how objects can be accessed in a variety of forms. Make sure you
understand when casts are needed for accessing objects, and be able to spot the difference
between compile-time and runtime cast problems.
```
- An annotation is extra information about the program, and it is a type of metadata.
- Reflection is a technique used in Java to look at information about the class at runtime.
- Whenever you override equals(), you are also expected to override hashCode().
- A hash code is a number that puts instances of a class into a finite number of categories
```java
@Override
public String toString() {
return name;
}
// It is common to multiply by a prime number when combining multiple fields in the hash code.
public int hashCode() {
return keyField + 7 * otherKeyField.hashCode();
}

// Apache Commons
@Override public String toString() {
return ToStringBuilder.reflectionToString(this,
ToStringStyle.SHORT_PREFIX_STYLE);
}

public boolean equals(Object obj) {
if ( !(obj instanceof LionEqualsBuilder)) return false;
Lion other = (Lion) obj;
return new EqualsBuilder().appendSuper(super.equals(obj))
.append(idNumber, other.idNumber)
.append(name, other.name)
.isEquals();
}

```
##### Enum
- an enum is a type of class that mainly contains static members
- you can’t do is extend an enum.
- the semicolon at the end of the enum values is optional only if the only thing in the enum is that list of values.

##### Nested Class
- A nested class is a class that is defined within another class. A nested class that is not
static is called an inner class. There are four of types of nested classes:
  - A member inner class is a class defined at the same level as instance variables. It is notstatic. Often, this is just referred to as an inner class without explicitly saying the type.
    - Can be declared public, private, or protected or use default access
    - Can extend any class and implement interfaces
    - Can be abstract or final
    - **Cannot declare static fields or methods**
    - Can access members of the outer class including private members
    - For the inner class, the compiler creates Outer$Inner.class.
    - private interface as declared as inner, its methods still need be public
  - A local inner class is defined within a method.
    - They do not have an access specifier.
    - They cannot be declared static and cannot declare static fields or methods.
    - They have access to all fields and methods of the enclosing **class**.
    - They do not have access to local variables of a method unless those variables are **final or effectively final**.
      - If the code could still compile with the keyword final inserted before the local variable, the variable is effectively fi nal.
  - An anonymous inner class is a special case of a local inner class that does not have a name.
    - Anonymous inner classes are required to extend an existing class or implement an existing interface
    - don’t even store it in a local variable
    - must have exactly one superclass or one interface
  - A static nested class is a static class that is defined at the same level as static variables.
    - It can be instantiated without an object of the enclosing class
    - as regular class except:
      - The nesting creates a namespace because the enclosing class name must be used to refer to it.
        - Java treats the static nested class as if it were a namespace.
        - either import or import static
      - It can be made private or use one of the other access modifiers to encapsulate it.
      - The **enclosing class can refer to the fields and methods** of the static nested class. while it can't access enclosing class'


```java
Outer outer = new Outer();
Inner inner = outer.new Inner(); // create the inner class
```
```
The instanceof keyword compares an object to a class or interface type. It also looks at
subclasses and subinterfaces. x instanceof Object returns true unless x is null. If the
compiler can determine that there is no way for instanceof to return true, it will generate
a compiler error. Virtual method invocation means that Java will look at subclasses when
finding the right method to call. This is true, even from within a method in the superclass.
The methods toString(), equals(), and hashCode() are implemented in Objects
that classes can override to change their behavior. toString() is used to provide a
human‐readable representation of the object. equals() is used to specify which instance
variables should be considered for equality. equals() is required to return false when the
object passed in is null or is of the wrong type. hashCode() is used to provide a grouping
in some collections. hashCode() is required to return the same number when called with
objects that are equals().
The enum keyword is short for enumerated values or a list of values. Enums can be used
in switch statements. They are not int values and cannot be compared to int values. In a
switch, the enum value is placed in the case. Enums are allowed to have instance variables,
constructors, and methods. Enums can also have value‐specific methods. The enum itself
declares that method as well. It can be abstract, in which case all enum values must
provide an implementation. Alternatively, it can be concrete, in which case enum values can
choose whether they want to override the default implementation.
There are four types of nested classes. Member inner classes require an instance of
the outer class to use. They can access private members of that outer class. Local inner
classes are classes defined within a method. They can also access private members of the
outer class. Local inner classes can also access final or effectively final local variables.
Anonymous inner classes are a special type of local inner class that does not have a name.
Anonymous inner classes are required to extend exactly one class by name or implement
exactly one interface. Static nested classes can exist without an instance of the outer class.
This chapter also contained a review of access modifiers, overloading, overriding,
abstract classes, static, final, and imports. It also introduced the optional @Override
annotation for overridden methods or methods implemented from an interface.
```
##### Interface
- An interface provides a way for one individual to develop code that uses another individual’s code, without having access to the other individual’s underlying implementation. Interfaces can facilitate rapid application development by enabling development teams to create applications **in parallel**, rather than being directly dependent on each other.
  - mock object, sometimes referred to as dummy code

- @FunctionalInterface
- The Java compiler implicitly assumes that any interface that contains **exactly one abstract method** is a functional interface.
- implement FunctionalInterface using lambda expressions.
- Deferred execution means that code is specified now but runs later.


- Polymorphism is the ability of a single interface to support multiple underlying forms. In Java, this allows multiple types of objects to be passed to a single method or class.
- If you use a variable to refer to an object, then only the methods or variables that are part of the variable’s reference type can be called without an explicit cast.

- In Java, all objects are accessed by reference, so as a developer you never have direct access to the memory of the object itself.


##### Design Principle
- A design principle is an established idea or best practice that facilitates the software design process
 - more logical, easier to understand, to reuse, to maintain
- a data model is the representation of our objects and their properties within our application and how they relate to items in the real world.
- encapsulation is the idea of combining fi elds and methods in a class such that the methods operate on the data, as opposed to the users of the class accessing the fi elds directly. In Java, it is commonly implemented with private instance members that have public methods to retrieve or modify the data, commonly referred to as getters and setters, respectively.
- An **invariant** is a property or truth that is maintained even after the data is modifi ed.
- In object.oriented design, we describe the property of an object being an instance of a data type as having an **is.a** relationship. The is.a relationship is also known as the **inheritance test**.
- We refer to the **has.a** relationship as the property of an object having a named data object or primitive as a member. The has.a relationship is also known as the object **composition test**,

- object **composition** as the property of constructing a class using references to other classes in order to reuse the functionality of the other classes
  - an alternate to inheritance and is often used to **simulate polymorphic** behavior that cannot be achieved via single inheritance

- A design pattern is an established general solution to a commonly occurring software development problem.
- An anti.pattern is a common solution to a reoccurring problem that tends to lead to unmanageable or difficult.to.use code.
- The creational patterns simply apply a level of indirection to object creation by creating the object in some other class, rather than creating the object directly in your application.

###### Singleton
- The singleton pattern is a creational pattern focused on creating only one instance of an object in memory within an application, sharable by all classes and threads within the application. The **globally available object** created by the singleton pattern is referred to as a singleton.
- singletons in Java are created as **private static** variables within the class, often with the name **instance**. They are accessed via a single **public static** method, often named **getInstance()**, which returns the reference to the singleton object. Finally, all **constructors** in a singleton class are marked **private**, which ensures that no other class is capable of instantiating another version of the class.
- **application configuration data and reusable data caches** are commonly implemented using singletons. Singletons may also be used to **coordinate access to shared resources**, such as coordinating write access to a file
- Creating a reusable object the first time it is requested is a software design pattern known as lazy instantiation.
  - Lazy instantiation reduces memory usage and improves performance when an application starts up.
- Multiple JVM, serialzation, thread safe, global access, reflection, clone(override it then)
- hard for unit testing, global access

```java
public class Singleton implements Serializable {
    // volatile variable guaranteeing happens-before relationship: a guarantee that action performed by one thread is visible to another action in different thread
    private static volatile Singleton instance;

    // private constructor, implicit final class
    private Singleton(){

        //Prevent form the reflection api.
        if (instance != null){
            throw new RuntimeException("Use getInstance() method to get the single instance of this class.");
        }
    }
    
    public static Singleton getInstance() {
        // double.checked locking: check needed first
        if (instance == null) { // Lazy instantiation
            synchronized (Singleton.class) { // thread safe
                if (instance == null) instance = new Singleton();
            }
        }

        return sSoleInstance;
    }

    // when an object is read, replace it with the singleton instance. This ensures that nobody can create another instance by serializing and deserializing the singleton
    protected SingletonClass readResolve() {
        return getInstance();
    }
}
```

###### Immutable
- final calss, private final field. only getter, construct once, defensive copy, 

- 1. Use a constructor to set all properties of the object.
- 2. Mark all of the instance variables private and final .
- 3. Don’t define any setter methods.
- 4. Don’t allow referenced mutable objects to be modified or accessed directly.
- 5. Prevent methods from being overridden.
- Defensive copy

###### Builder
- a lot feilds, may dependent on each other, too many combination
- The builder pattern is a creational pattern in which parameters are passed to a builder object, often through method chaining(return the object), and an object is generated with a final build call. It is often used with immutable objects, since immutable objects do not have setter methods and must be created with all of their parameters set, although it can be used with mutable objects as well.
- In practice, a builder class is often **packaged alongside its target class, either as a static inner class** within the target class or within the same Java package.

###### Factory
- creates objects in which the precise type of the object may not be known until runtime
- supporting class polymorphism
- postfix the class name with the word Factory

```
One of the primary goals of this chapter was to teach you how to write better code. We
demonstrated techniques for designing class structures that scale naturally over time, integrate
well with other applications, and are easy for other developers to read and understand.
We started off with a brief review of interfaces from your OCA studies showing how to
declare, implement, and extend them. We then moved on to functional programming and
reviewed the various syntax options available for defining functional interfaces and writing
lambda expressions. Given the prevalence of lambda expressions throughout Java 8, you
absolutely need to practice writing and using lambda expressions before taking the exam.
We concluded the discussion with a review of the generics‐based Predicate interface and
showed how it can be used in place of your own functional interface. We will return to
lambdas and streams in Chapter 3 and Chapter 4 in much greater detail.
This chapter introduced the concept of polymorphism, which is central to the Java
language, and showed how objects can be accessed in a variety of forms. Make sure
that you understand when casts are needed for accessing objects, and be able to spot the
difference between compile‐time and runtime cast problems.
In the design principles section, we taught you how to encapsulate your classes in Java
properly, allowing you to enforce class invariants in your data model. We then described
the is‐a and has‐a principles and showed how you can apply them to your data model.
Finally, we introduced the technique of creating class structures using object composition
that rely on the has‐a principle as an alternative to inheritance.
We completed this chapter by explaining what a design pattern is and presenting you with
four well‐known design patterns. Design patterns provide you with a way to solve a problem
that you encounter using solutions that other developers have already built and generalized.
The singleton pattern is excellent for managing a single shared instance of an object within
an application. The immutable object pattern is useful for creating read‐only objects that
cannot be modified by other classes. The builder pattern solves the problem of how to
create complex objects cleanly, and it is often used in conjunction with the immutable object
pattern. Finally, the factory pattern is useful for creating various objects without exposing
the underlying constructors and complex rules for selecting a particular object subtype.
```




### Exception
#### Handling Exceptions
- an exception is an event that alters program fl ow.
- In general, try to avoid return codes.
- A method can handle the exception case itself or make it the caller’s responsibility.
- Object
  - Throwable
    - Exception
      - RuntimeException
        - Runtime exceptions tend to be unexpected but not necessarily fatal.
      - Checked Exception
        - handle or declare rule.
        - Checked exceptions tend to be more anticipated
    - Error
      - Error means something went so horribly wrong that your program should not attempt to recover from it.

- The curly braces are required for the try and catch blocks.
- a try statement must have catch and/or finally. try-with-resources that allows neither a catch nor a finally block
- If it is impossible for one of the catch blocks to be executed, a compiler error about unreachable code occurs. This happens when a superclass is caught before a subclass.
- Declaring an unused exception isn’t considered unreachable code.
- catching multiple exceptions: at most one catch block will run and it will be the fi rst catch block that can handle it.

- another try/catch inside a finally block—to make sure it doesn’t **mask** the exception from the catch block. The exception from the catch block gets forgotten about, due to exception thrown from finally.

- Runtime Exceptions
  - ArithmeticException Thrown by the JVM when code attempts to divide by zero
  - ArrayIndexOutOfBoundsException Thrown by the JVM when code uses an illegal index to access an array
  - ClassCastException Thrown by the JVM when an attempt is made to cast an exception to a subclass of which it is not an instance
  - IllegalArgumentException Thrown by the programmer to indicate that a method has been passed an illegal or inappropriate argument
    - NumberFormatException Thrown by the programmer when an attempt is made to convert a string to a numeric type but the string doesn’t have an appropriate format
  - NullPointerException Thrown by the JVM when there is a null reference where an object is required


- Checked Exceptions
  - IOException Thrown programmatically when there’s a problem reading or writing a fi le
    - FileNotFoundException Thrown programmatically when code tries to reference a fi le that does not exist

- Errors
  - ExceptionInInitializerError Thrown by the JVM when a static initializer throws an exception and doesn’t handle it
  - StackOverflowError Thrown by the JVM when a method calls itself too many times
  - NoClassDefFoundError Thrown by the JVM when a class that the code uses is available at compile time but not runtime

- No swallowing exception: print out a stack trace or at least a message when catching
an exception. Also, consider whether continuing is the best course of action.

```
An exception indicates something unexpected happened. A method can handle an exception
by catching it or declaring it for the caller to deal with. Many exceptions are thrown
by Java libraries. You can throw your own exception with code such as throw new
Exception().
Subclasses of java.lang.Error are exceptions that a programmer should not attempt to
handle. Subclasses of java.lang.RuntimeException are runtime (unchecked) exceptions.
Subclasses of java.lang.Exception, but not java.lang.RuntimeException are checked
exceptions. Java requires checked exceptions to be handled or declared.
If a try statement has multiple catch blocks, at most one catch block can run. Java
looks for an exception that can be caught by each catch block in the order they appear, and
the fi rst match is run. Then execution continues after the try statement. If both catch and
finally throw an exception, the one from finally gets thrown.
Common runtime exceptions include:
■ ArithmeticException
■ ArrayIndexOutOfBoundsException
■ ClassCastException
■ IllegalArgumentException
■ NullPointerException
■ NumberFormatException
IllegalArgumentException and NumberFormatException are typically thrown by the
programmer, whereas the others are typically thrown by the JVM.
Common checked exceptions include:
■ IOException
■ FileNotFoundException
Common errors include:
■ ExceptionInInitializerError
■ StackOverflowError
■ NoClassDefFoundError
When a method overrides a method in a superclass or interface, it is not allowed to add
checked exceptions. It is allowed to declare fewer exceptions or declare a subclass of a
declared exception. Methods declare exceptions with the keyword throws.
```













