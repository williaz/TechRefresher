### Thread
- Tuning: Thread, SQL, JVM

- Goal: speed up
- one core CPU: a lot IO waiting in thread like Web, can improve
- when give away CPU: blocked: wait, await; sleep, yield, finished
- Thread: execution flow of code, pass to CPU
1. Runnable => task
2. Thread => thread
3. Callable's call() in run()
- CPU: execute code
- how many?
1. create, destroy time
2. space for trhead: JVM 1M for a thread
3. conect switching reduce performance
- thread pool:
1. blocking threads
2. task repo: queue => BlockingQueue: block or timeout
3. thread pick up task

- determine thread num
1. computing: CPU num * 1 -2
2. IO: time of IO blocking, tomcat default max=200


```java
public  class ThreadPool {
    private BlockingQueue<Runnable> que;
    private List<Thread> workers;
    
    public ThreadPool (int size, int taskNum) {
       this.que = new LinkedBlockingQueue<>(taskNum);
       this.workers = Collections.synchronizedList(new ArrayList<>());
       for (int i = 0; i < size; i++) {
           Worker w = new Worker(this);
           workers.add(w);
           w.start(); ///
       }
    }
    
    public bolean submit(Runnable r) {
        if (!isWorking) {
           return false;
        }
        this.que.offer(r);
    }
    
    private volatile isWorking = true;
    public void close() {
        // no more new task
        // finsih task
        // no blocking thread
        // current blocking thread <= interrupt
        isWorking = false;
        for (Thread t : workers) {
            if (t.getState().equals(Thread.state.BLOCKED)) {
                t.interrupt();
            }
        }
        
    }

    
    public static class Worker extends Thread {
        private ThreadPool pool;
        public Worker(ThreadPool pool) {
           this.pool = pool; ///
        }
        public void run() {
            que.take();
            while (this.isWorking || !que.isEmpty()) { ///
                Runnable task = null;
                try {
                    if (isWorking)
                        task = this.pool.que.take(); ///
                    else {
                        task = this.pool.que.poll(); ///
                    }
                } catch (InterruptedException e) {
                }
                if (task != null) {
                    task.run();
                }
            }
        }
    }
}

```
### Thread safe
- variable
1. global
2. local
- data shared by thread: globale var
- can a thread see the upated var by another thread?
1. synchronized
2. volatile

- JMM: memory model <= logical 
1. shared var in main memory(heap)
2. Thread can only operate own memory(stack)
3. if need operation shared var, need copy from main memory and then operate and copy back
- JMM sync contract: 8 atomic opt
1. lock
2. unlock
3. read: main m -> temp/register
4. load: temp -> own m
5. use: -> thread opt
6. assign
7. store: own -> temp 
8. write: temp -> main
* read&load, store&write: require ordered, not need contigous

- ensure var visualizable
1. final
2. synchronized: just empty work memory before entering; must sync to main m before unlocking VS before use and after
3. volatile: 
must load from main before use, read and load must be contigous; 
after modifed, must sync to main immediately, store and write be contiguous


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









