## Java Coding Best Practice:

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
