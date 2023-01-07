```py
python3 -m pip install --upgrade pip
python3 -m pip install pypdf

import sys
arg1 = sys.argv[1]
```

### 1. Version
```sh
python3 --version
```

```py
import sys
print(sys.version)
```
### 2. Style: PEP 8
https://peps.python.org/pep-0008/

### 4. Prefer F-Strings
- with param name and allow expression
```py
>>> key = "myKey"
>>> var = 18
>>> print(f'key: val is {key}:{var}')
key: val is myKey:18
```
### 6. Unpack with Mulitple assignment
```py
>>> food = [('burger', 400), ('hotdog', 200)]
>>> for i, (item, cal) in enumerate(food):
...     print(f'{i} : {item} has {cal} calories')
... 
0 : burger has 400 calories
1 : hotdog has 200 calories
```
### 7. Prefer enumerate Over range
```py
for i, n in enumerate(arr):
    print(f'{i} : {n}')
```
### 8. Use zip to process multiple Iterators in parellel
- zip: silently truncates to the shortest iterator
  - creates a lazy generator that produces tuples => infinitely long inputs.
- itertools.zip_longest: longest, None
```py
>>> l1 = [1, 3, 5, 7]
>>> l2 = [2, 4, 6]
>>> for i1, i2 in zip(l1, l2):
...     print(f'{i1} VS {i2}')
... 
1 VS 2
3 VS 4
5 VS 6
```
### 9. Avoid else with for and while

### 10. prevent repetition with Assignment Expr :=

- scope to remove emphasis
- as switch/case
- do/while


### 11. Slicing Sequences
- dont's supply 0 as start or eln as end

### 12. avoid Striding with slicing
- positive step
```py
a[::2]
```
### 13. prefer Catch-all unpacking over Slicing
- starred expression
  - occur only once
  - in any position 
  - with at least one required part
  - as a list => memory
```py
>>> l = ['Will', 12, 34, 'male']
>>> name, *others, gender = l
>>> print(f'{name} and {gender}')
Will and male
```

### 14. sort with key
- multiple sorts preserve previous relative order
- tuple for multipel ordering
```py
>>> name = ['Will', 'Bob', 'alex', 'Abby', 'Zoe']
>>> name.sort(key=lambda x : x.lower(), reverse=True)
>>> name
['Zoe', 'Will', 'Bob', 'alex', 'Abby']

>>> people = [('Will', 33), ('Bob', 2), ('Zoe', 28), ('alex', 1)]
>>> people.sort(key = lambda x: (x[1], x[0].lower()))
>>> people
[('alex', 1), ('Bob', 2), ('Zoe', 28), ('Will', 33)]
```
### 15. dict Insertion ordering
- starts from 3.7 officially

### 16. Perfer get Over in and KeyError for dict missing key

```py
>>> try:
...     age = mapp['Will']
... except KeyError:
...     age = "Unknown"
... 
>>> age
'Unknown'
>>> mapp.get('Will', 33)
33
```
### 17. defaultdict Over setdefault 

```py
>>> from collections import defaultdict
>>> owner_cars = defaultdict(set)
>>> owner_cars['Will'].add('cra')
```
### 18. use dict's __missing to set default value if key-dependent like func

```py
>>> class Pictures(dict):
...     def __missing__(self, key):
...         value = key % 10
...         self[key] = value
...         return value
... 
>>> p = Pictures()
>>> p[2]
2
```
### 19. Never unpack >3 var for func return values
### 20. perfer Raising Exception to returning None

```py
>>> def div(a: float, b: float) -> float:
...     try:
...         return a / b
...     except ZeroDivisionError as e:
...         raise ValueError('Invalid Input')
... 
>>> div(3.2, 0)
Traceback (most recent call last):
  File "<stdin>", line 3, in div
ZeroDivisionError: float division by zero

During handling of the above exception, another exception occurred:

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 5, in div
ValueError: Invalid Input
>>> try:
...     div(3.2, 0)
... except ValueError as ve:
...     print('something wrong')
... 
something wrong
```

### 21. Closure scope
- closure can refer to variables from any of the scopes in which they were defined
- use nonlocal: when closure can modufy a variable in its enclosing scopes; better use class instead


```py
def sort_with_vip(nums, group):
     found = False
     def helper(x):
         nonlocal found
         if x in group:
             found = True
             return (0, x)
         return (1, x)
     nums.sort(key = helper)
     return found

nums = [0, 1, 2, 3, 4, 5, 6]
group = [2, 4]
print(sort_with_vip(nums, group))
```

### 22, position arguments ```*varargs``` in func
```py
def log(msg, *values):
    print(f'{msg}: {values}')

log("print: ", 1, 2, 3)
nums = [2, 3, 4]
log("list: ", *nums) # pass items from nums as tuple => may OOM error

print: : (1, 2, 3)
list: : (2, 3, 4)
```

### 23 Keyword Argument for optional arg
- clearer, optional, backword compatible when extend
```py
def func(name, age=18, gender="male"):
    print(f'{name} is {gender} at age of {age}')

func('Will')
func(name="Zoe", gender="female")

arg = {'name':"Alex", 'age':1, 'gender':"male"}
func(**arg)
```







