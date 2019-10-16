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







