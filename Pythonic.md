```py
python3 -m pip install --upgrade pip
python3 -m pip install pypdf

import sys
arg1 = sys.argv[1]
```

### 1 Version
```sh
python3 --version
```

```py
import sys
print(sys.version)
```
### 2 Style: PEP 8
https://peps.python.org/pep-0008/

### 4 Prefer F-Strings
- with param name and allow expression
```py
>>> key = "myKey"
>>> var = 18
>>> print(f'key: val is {key}:{var}')
key: val is myKey:18
```
### 6 Unpack with Mulitple assignment
```py
>>> food = [('burger', 400), ('hotdog', 200)]
>>> for i, (item, cal) in enumerate(food):
...     print(f'{i} : {item} has {cal} calories')
... 
0 : burger has 400 calories
1 : hotdog has 200 calories
```
### 7 Prefer enumerate Over range




