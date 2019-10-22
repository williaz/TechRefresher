
### start thread
- 1. extends Thread, overriding run() and use start() trigger
- 2. implements Runnable, overriding run(), pass the class as arg in Thread constructor, start() thread; can use anonymous class
- 3. ExecutorService to create a thread-pool

### volatile
- as shared data(like state) maybe been cached by thread
- volatile: prevent thread caching variable

### synchronized
- join(): wait thread finished
- synchronized: uses intrinsic lock to prevent interleaving between operation steps used by mt

```java

```

