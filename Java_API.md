- [x] Clear StringBuilder
```java
StringBuilder sb = new StringBuilder();
sb.setLength(0); //

// value = new char[capacity];
   public void setLength(int newLength) {
        if (newLength < 0)
            throw new StringIndexOutOfBoundsException(newLength);
        ensureCapacityInternal(newLength);

        if (count < newLength) {
            Arrays.fill(value, count, newLength, '\0');
        }

        count = newLength;
    }
```
