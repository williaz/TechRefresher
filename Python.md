- REPL(Read Eval Print Loop): The python shell is running a loop, which will read everything that
you type in at this prompt, evaluate what it has read, and then print the
result.
- docstring: documentation within triple quotes, similar to Javadoc. Calling help(a_function) displays this docstring.
- dir() check built-in functions
- print()
- if, elif, while
- is equivalent to Java ==
  - id() is hashCode()
- complex 1000 + 12j
- strings are immutable in Python.
  - wrapped in either single or double quotes
  - raw string denoted by the r prefix.:  an indication to the Python parser to not apply any backslash escaping rules to the string.
```python
>>> print 'c:\temp\dir' ①
c: emp\dir
>>> print 'c:\\temp\dir' ②
c:\temp\dir
>>> print r'c:\temp\dir' ③
c:\temp\dir
```
- tuple is immutable.
- Python allows passing in a negative value for the index, in which case, it **adds the length of the list to the index** and returns the corresponding element.
- The slice syntax—L[start:stop(:step)]—returns a subsequence of the list as a new list.
  - default start: 0
  - default stop: len
  - default step: 1
    - negative step reverses the direction of iteration
```python
>>> numbers = [0, 1, 2, 'three', 4, 5, 6, 7, 8, 9] ①
>>> numbers[0] ②
0
>>> numbers[-1] ③
9

>>> numbers = [10, 20, 30]
>>> for number in numbers:
... print number
...
10
20
30

>>> for index, number in enumerate(numbers):
... print index, number
...
0 10
1 20
2 30
>>> for index in range(len(numbers)):
... print index
...
0
1
2

23
>>> stack = []
>>> stack.append("event") # Push
>>> event = stack.pop() # Pop
>>>
>>> queue = []
>>> queue.append("event") # Push
>>> event = queue.pop(0) # Pop from beginning

```
- List comprehensions: map(fn, iter) as [fn(x) for x in iter].
```python
>>> factorials_of_odds = [math.factorial(num) for num in
range(10) if num % 2 != 0]
```

- If the list/object being iterated over is large (or even unbounded), then a variant of the list comprehension syntax called **generator expressions** can be used.
```python
>>> factorials_of_odds = (math.factorial(num) for num in
xrange(10**10) if num % 2 != 0)
```
#### function

- They are first-class objects
  - can live by themselves without the need to be wrapped inside classes. 
  - can be created at runtime, assigned to variables, 
  - passed as arguments to other functions, and returned as values from other functions.
  - support list of positional parameters, named parameters, varargs, and keyword-based varargs.
  - supports anonymous functions in the form of lambda expressions
  - support for multiple return values!
    - automatic tuple packing: When a function returns multiple comma-separated values, Python automatically wraps them up into a tuple
```python
# automatic tuple unpacking
>>> a, b, c = (1, 2, 3) ②
>>> print a, b, c
1 2 3
```
- In a duck-typed language, the function would take an object of any type, and simply call its walk and quack methods, producing a runtime error if they are not defined.
- *args: The leading single asterisk is Python notation to unpack the tuple values 
- **kwargs: the leading double asterisk unpacks the dict values.
  
```python
def make_function(parity): ①
  """Returns a function that filters out `odd` or `even`
  numbers depending on the provided `parity`.
  """
  if parity == 'even':
    matches_parity = lambda x: x % 2 == 0 ②
  elif parity == 'odd':
    matches_parity = lambda x: x % 2 != 0
  else:
    raise AttributeError("Unknown Parity: " + parity) ③
  def get_by_parity(numbers): ④
    filtered = [num for num in numbers if matches_parity(num)] # closure.
    return filtered
  return get_by_parity ⑤
# return function


>>> get_odds = make_function('odd') ①
>>> print get_odds(range(10)) ②
[1, 3, 5, 7, 9]
```
#### Class
- The first argument of all instance methods is self; java this
- __init__: constructor
- __str__: toString()
- Python does not have the concept of visibility at all! Everything is just public.
- internal implementation detail:  the convention is to prefix the attribute/method name with a single underscore 
- Python supports single inheritance as well as multiple inheritance;
  - the method resolution order is in general, depth-first. 
    - The class attribute __mro__ can be inspected to check the actual method resolution order being used for the class.
- Protocols are like Java interfaces


#### APP
The most basic unit of source code organization in Python is the module,
which is just a .py file with Python code in it. This code could be functions,
classes, statements or any combination thereof. Several modules can
be collected together into a package. Python packages are just like Java
packages, with one difference: the directory corresponding to a package
must contain an initializer file named __init__.py. This file can be empty
or it can optionally contain some bootstrap code that is executed when the
package is first imported.


```python
.
|-- cart.py
|-- db
| |-- __init__.py
| |-- mysql.py
| +-- postgresql.py
+-- model
|-- __init__.py
+-- order.py

import model.order
sell_order = order.SellOrder()

# from <package|module> import <item>:

from model.order import SellOrder
sell_order = SellOrder()

from model.order import TYPES as ORDER_TYPES
```
- It is perfectly fine to create modules with just functions or just classes or a mix of both.

- There is no formal notion of an application entry point in Python
  - when you run python foo.py, execution begins at the start of the file and all statements defined at the top-level are executed in order until we reach the end of the file.
- The first value in the list, sys.argv[0] is the name of the script itself.
- When Python imports a module, it actually executes that module, just as it would have if the module were run as a script!
- During program execution, whenever a module is imported, Python creates a new namespace for it. The name of the module is used as the name of the namespace.
- the entry point of your application (typically, the script that is executed by Python) is assigned the namespace __main__.
- idiomatic Python main() functions
```python
if __name__ == '__main__':
  import sys
  main(sys.argv)
```
