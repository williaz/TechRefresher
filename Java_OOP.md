- Variable Scope
  - Class scope: anywhere
  - Local scope: declaration location matter
    - function scope
    - block scope
      - var will be destroyed after the block
      - name of vars of inner and outer block must be different
```java
            if (node.left != null) {
                int a = 1;
                int b = 10;
            }
            // a = 4; // CE: undef!
            int a = 3; // Fine
            if (node.right != null) {
                // int a = 2; // CE: already def!
                int b = 20; // Fine.
            }
```


