
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

