
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
- block: declare **seperate Object lock** for synchronized block instead of using the actual object in case Java optimize the vars
### thread pool
- ExecutorService: sumbit task, shutdown(), awaitTermincation() 

### CountDownLatch
- latch.wait() for count reach

### Producer-Consumer
- BlockingQueue: put(), take() waiting for valid size

### wait & notify
- wait: only in syn, lose lock, 
- notify: only in syn, wake up all waiting threads; better release lock after notify quickly
- Producer-Consumer: combine with syn block on a shared collection

### ReenrantLock
- Lock: lock(), unlock() in try-finally block
- Condition: await(), singal() in between lock()/unlock() 


### Deadlock
- 2 lock, diff order
- nested synchronized block
- reentrantLock1.tryLock() && reentrantLock2.tryLock()

```java
// resolve deadlock

private void acquireLocks(Lock lock1, Lock2) {
    while (true) {
        // acq locks
        boolean gotLock1 = false;
        boolean gotLock2 = false;
        try {
            gotLock1 = lock1.tryLock();
            gotLock2 = lock2.tryLock();
        } finally {
            if (gotLock1 && gotLock2) {
                return;
            }
            if (gotLock1) {
                lock1.unlock();
            }
            if (gotLock2) {
                lock2.unlock();
            }
        
        }
        // didn't acquire lock
        Thread.sleep(1);
    }
}
```
### Semaphore
- maintain a number as the permits to control resource access, DB connection?
- fair - true if this semaphore will guarantee first-in first-out granting of permits under contention, else false
- release(): +1, acquire(): -1, or wait till get a permit

### Callable & Future
- Callable for return value
- future to get return or catch exception
```java
Future<Integer> future = Executor.submit(new Callable<Integer>{...});

future.get();

Future<?> future = Executor.submit(new Callable<Void>{...});
```
### interrupt
- t1.interrupt()
- Thread.currentThread().isInterrupted()









