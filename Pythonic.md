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







