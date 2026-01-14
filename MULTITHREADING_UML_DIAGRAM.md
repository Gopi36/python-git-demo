# Python Multithreading - Comprehensive UML Diagram
## Single Source of Truth for Students

---

## 📊 Thread Lifecycle State Diagram

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         THREAD LIFECYCLE                                 │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                           │
│   [*] ──────────> (Created) ──────────> (Started) ──────────> (Running) │
│                       │                      │                     │     │
│                       │                      │                     │     │
│                   Thread()              start()              Executing   │
│                   object                called                 code      │
│                   created                                        │       │
│                                                                  │       │
│                                                         ┌────────┴────┐  │
│                                                         │             │  │
│                                                         ▼             │  │
│                                                    (Blocked)          │  │
│                                                   wait/join/         │  │
│                                                   acquire()          │  │
│                                                         │             │  │
│                                                         │             │  │
│                                                    notify/release    │  │
│                                                         │             │  │
│                                                         └─────────────┘  │
│                                                                  │       │
│                                                                  │       │
│   [*] <────────── (Terminated) <────────── (Completed) <────────┘       │
│                                                                           │
│                                              run() method                 │
│                                              completed                    │
└─────────────────────────────────────────────────────────────────────────┘
```

### Thread Lifecycle Timeline (Vertical View)

```
Main Thread              Child Thread 1           Child Thread 2
    │                         │                        │
    │ Creating threads        │                        │
    ├──────────────────────> Created ──────────────> Created
    │                         │                        │
    │ start() called          │                        │
    ├──────────────────────> Started ─────────────> Started
    │                         │                        │
    │ Running                 │                        │
    │ (executing main)       Running                  Running
    │                      (executing)              (executing)
    │                         │                        │
    │                         │                        │
    │ join() - waiting        │                        │
    │     (blocked)           │                        │
    │ ┌─────────────────────> │                        │
    │ │                       │                        │
    │ │                    Completed                Completed
    │ └───────────────────────┘                        │
    │                                                   │
    │ join() - waiting                                  │
    │     (blocked)                                     │
    │ ┌─────────────────────────────────────────────────┘
    │ │
    │ └─────────────────────────────────────────────────┐
    │                                                   │
    │ All children done                              Done
    │                                                   │
    ▼                                                   ▼
  Done                                            [Terminated]
```

---

## 🧩 Class Diagram: Thread Class

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                             Thread                                         ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ PROPERTIES:                                                                ║
║ - name: str                    # Thread name                               ║
║ - ident: int (read-only)       # System-assigned thread ID                ║
║ - daemon: bool                 # Daemon thread flag                        ║
║ - _target: callable            # Function to execute                       ║
║ - _args: tuple                 # Arguments for target                      ║
║ - _kwargs: dict                # Keyword arguments for target              ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ CONSTRUCTOR:                                                               ║
║ + __init__(group=None, target=None, name=None,                            ║
║            args=(), kwargs={}, daemon=None)                                ║
║   Creates a new thread object                                              ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ ESSENTIAL METHODS:                                                         ║
║ + start() : None                                                           ║
║   Begin thread execution (calls run() in separate thread)                 ║
║   Can only be called once - raises RuntimeError if called twice           ║
║                                                                            ║
║ + run() : None                                                             ║
║   Method representing thread activity (override in subclass)               ║
║   Standard implementation invokes target with args/kwargs                  ║
║                                                                            ║
║ + join(timeout=None) : None                                                ║
║   Wait until thread terminates or timeout expires                          ║
║   Blocks calling thread until this thread completes                        ║
║                                                                            ║
║ + is_alive() : bool                                                        ║
║   Return True if thread is alive (started and not yet terminated)          ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ NAMING METHODS:                                                            ║
║ + getName() : str              # Get thread name                           ║
║ + setName(name: str) : None    # Set thread name                           ║
║ + name : str (property)        # Name property (read/write)                ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DAEMON METHODS:                                                            ║
║ + isDaemon() : bool            # Check if daemon thread                    ║
║ + setDaemon(daemonic: bool)    # Set daemon status (before start only)     ║
║ + daemon : bool (property)     # Daemon property (read/write)              ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ GLOBAL/STATIC FUNCTIONS:                                                   ║
║ <<static>> + active_count() : int                                          ║
║   Return number of Thread objects currently alive                          ║
║                                                                            ║
║ <<static>> + enumerate() : list[Thread]                                    ║
║   Return list of all Thread objects currently alive                        ║
║                                                                            ║
║ <<static>> + current_thread() : Thread                                     ║
║   Return Thread object corresponding to caller's thread                    ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLES:                                                                  ║
║                                                                            ║
║ METHOD 1: Function-based Thread                                            ║
║ ┌────────────────────────────────────────────┐                            ║
║ │ def worker():                              │                            ║
║ │     print("Working...")                    │                            ║
║ │                                            │                            ║
║ │ t = threading.Thread(target=worker)        │                            ║
║ │ t.start()                                  │                            ║
║ └────────────────────────────────────────────┘                            ║
║                                                                            ║
║ METHOD 2: Inheritance-based Thread                                         ║
║ ┌────────────────────────────────────────────┐                            ║
║ │ class MyThread(threading.Thread):          │                            ║
║ │     def run(self):                         │                            ║
║ │         print(f"{self.name} working...")   │                            ║
║ │                                            │                            ║
║ │ t = MyThread(name="Worker-1")              │                            ║
║ │ t.start()                                  │                            ║
║ └────────────────────────────────────────────┘                            ║
║                                                                            ║
║ METHOD 3: Composition-based Thread                                         ║
║ ┌────────────────────────────────────────────┐                            ║
║ │ class Task:                                │                            ║
║ │     def execute(self):                     │                            ║
║ │         print("Executing task...")         │                            ║
║ │                                            │                            ║
║ │ task = Task()                              │                            ║
║ │ t = threading.Thread(target=task.execute)  │                            ║
║ │ t.start()                                  │                            ║
║ └────────────────────────────────────────────┘                            ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

---

## 🔒 Synchronization Primitives - Class Diagrams

### 1. Lock - Basic Mutual Exclusion

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                              Lock                                          ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ PROPERTIES:                                                                ║
║ - _locked: bool               # Internal lock state                        ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ METHODS:                                                                   ║
║ + acquire(blocking=True, timeout=-1) : bool                                ║
║   Acquire the lock. Block if already held. Returns True if acquired        ║
║                                                                            ║
║ + release() : None                                                         ║
║   Release the lock. Can be called from any thread                          ║
║   Raises RuntimeError if lock is not held                                  ║
║                                                                            ║
║ + locked() : bool                                                          ║
║   Return True if lock is currently held                                    ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DESCRIPTION:                                                               ║
║ Basic mutex providing mutual exclusion.                                    ║
║ Only ONE thread can acquire lock at a time.                                ║
║ ⚠️ Cannot be acquired recursively (causes deadlock!)                      ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLE: Bank Account Synchronization                                      ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import threading                                       │                ║
║ │                                                        │                ║
║ │ class BankAccount:                                     │                ║
║ │     def __init__(self):                                │                ║
║ │         self.balance = 1000                            │                ║
║ │         self.lock = threading.Lock()                   │                ║
║ │                                                        │                ║
║ │     def withdraw(self, amount):                        │                ║
║ │         self.lock.acquire()  # ← Acquire lock          │                ║
║ │         try:                                           │                ║
║ │             if self.balance >= amount:                 │                ║
║ │                 self.balance -= amount                 │                ║
║ │                 print(f"Withdrew: ${amount}")          │                ║
║ │         finally:                                       │                ║
║ │             self.lock.release()  # ← Always release    │                ║
║ │                                                        │                ║
║ │ account = BankAccount()                                │                ║
║ │ t1 = threading.Thread(target=account.withdraw,         │                ║
║ │                       args=(600,))                     │                ║
║ │ t2 = threading.Thread(target=account.withdraw,         │                ║
║ │                       args=(600,))                     │                ║
║ │ t1.start(); t2.start()                                 │                ║
║ │ t1.join(); t2.join()                                   │                ║
║ └────────────────────────────────────────────────────────┘                ║
║                                                                            ║
║ ⚠️ LIMITATION: Recursive acquisition causes DEADLOCK                      ║
║ ┌────────────────────────────────────────────┐                            ║
║ │ lock = Lock()                              │                            ║
║ │ lock.acquire()  # OK                       │                            ║
║ │ lock.acquire()  # ❌ DEADLOCK!             │                            ║
║ └────────────────────────────────────────────┘                            ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### 2. RLock - Recursive Lock (a > r)

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                             RLock                                          ║
║                    (Reentrant / Recursive Lock)                            ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ PROPERTIES:                                                                ║
║ - _owner: Thread              # Thread that owns the lock                  ║
║ - _count: int                 # Recursion level (acquisition count)        ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ METHODS:                                                                   ║
║ + acquire(blocking=True, timeout=-1) : bool                                ║
║   Acquire the lock. Same thread can acquire multiple times                 ║
║   Increments internal counter                                              ║
║                                                                            ║
║ + release() : None                                                         ║
║   Release the lock. Decrements internal counter                            ║
║   Lock fully released when counter reaches 0                               ║
║   Must be called by thread that acquired the lock                          ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ RELATIONSHIP: a > r (Acquisitions > Releases temporarily)                  ║
║                                                                            ║
║ Same thread can acquire multiple times:                                    ║
║   acquire() → count: 0 → 1                                                 ║
║   acquire() → count: 1 → 2                                                 ║
║   acquire() → count: 2 → 3                                                 ║
║   release() → count: 3 → 2                                                 ║
║   release() → count: 2 → 1                                                 ║
║   release() → count: 1 → 0 (fully released)                                ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DESCRIPTION:                                                               ║
║ Recursive lock that can be acquired multiple times by same thread          ║
║ Prevents self-deadlock in recursive function calls                         ║
║ Must release as many times as acquired                                     ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLE 1: Recursive Function                                              ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import threading                                       │                ║
║ │                                                        │                ║
║ │ rlock = threading.RLock()                              │                ║
║ │                                                        │                ║
║ │ def recursive_function(n):                             │                ║
║ │     if n <= 0:                                         │                ║
║ │         return                                         │                ║
║ │                                                        │                ║
║ │     rlock.acquire()  # ← Acquire (even recursively)   │                ║
║ │     try:                                               │                ║
║ │         print(f"Level {n}")                            │                ║
║ │         recursive_function(n - 1)  # Recursive call    │                ║
║ │     finally:                                           │                ║
║ │         rlock.release()  # ← Release                   │                ║
║ │                                                        │                ║
║ │ recursive_function(3)  # ✅ No deadlock!               │                ║
║ └────────────────────────────────────────────────────────┘                ║
║                                                                            ║
║ EXAMPLE 2: Nested Method Calls                                             ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ class ResourceManager:                                 │                ║
║ │     def __init__(self):                                │                ║
║ │         self.rlock = threading.RLock()                 │                ║
║ │         self.count = 0                                 │                ║
║ │                                                        │                ║
║ │     def increment(self):                               │                ║
║ │         with self.rlock:  # Acquire                    │                ║
║ │             self.count += 1                            │                ║
║ │                                                        │                ║
║ │     def double_increment(self):                        │                ║
║ │         with self.rlock:  # Acquire 1st time           │                ║
║ │             self.increment()  # Acquire 2nd time ✅    │                ║
║ │             self.increment()  # Acquire 3rd time ✅    │                ║
║ │         # All three acquisitions by same thread!       │                ║
║ │                                                        │                ║
║ │ manager = ResourceManager()                            │                ║
║ │ manager.double_increment()  # Works perfectly!         │                ║
║ └────────────────────────────────────────────────────────┘                ║
║                                                                            ║
║ ✅ ADVANTAGE: Prevents self-deadlock in recursion/nesting                 ║
╚═══════════════════════════════════════════════════════════════════════════╝

         Lock                           RLock
          │                              │
          │◄─────────extends with────────┘
          │          recursion support
```

### 3. Semaphore - Resource Counting (a < r)

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                           Semaphore                                        ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ PROPERTIES:                                                                ║
║ - _value: int                 # Internal counter (available resources)     ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ CONSTRUCTOR:                                                               ║
║ + __init__(value=1)           # Initialize with resource count             ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ METHODS:                                                                   ║
║ + acquire(blocking=True, timeout=-1) : bool                                ║
║   Decrement counter. Block if counter = 0                                  ║
║                                                                            ║
║ + release() : None                                                         ║
║   Increment counter. Wake up waiting thread                                ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ RELATIONSHIP: a < r (Acquisitions < Resources)                             ║
║                                                                            ║
║ Counter Mechanics:                                                         ║
║   Initial value: N                                                         ║
║   acquire() → value: N → N-1                                               ║
║   acquire() → value: N-1 → N-2                                             ║
║   ...                                                                      ║
║   acquire() → value: 1 → 0                                                 ║
║   acquire() → BLOCKS (no resources available)                              ║
║   release() → value: 0 → 1 (one thread unblocked)                          ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DESCRIPTION:                                                               ║
║ Manages internal counter representing available resources                  ║
║ Allows N threads to acquire simultaneously (N = initial value)             ║
║ Blocks when all resources are in use                                       ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLE: Connection Pool (Max 3 connections)                               ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import threading                                       │                ║
║ │ import time                                            │                ║
║ │                                                        │                ║
║ │ # Create semaphore with 3 resources                    │                ║
║ │ pool = threading.Semaphore(3)                          │                ║
║ │                                                        │                ║
║ │ def worker(thread_id):                                 │                ║
║ │     print(f"Thread-{thread_id}: Waiting...")           │                ║
║ │                                                        │                ║
║ │     pool.acquire()  # Decrement counter                │                ║
║ │     print(f"Thread-{thread_id}: Got resource!")        │                ║
║ │                                                        │                ║
║ │     try:                                               │                ║
║ │         time.sleep(2)  # Use resource                  │                ║
║ │     finally:                                           │                ║
║ │         pool.release()  # Increment counter            │                ║
║ │         print(f"Thread-{thread_id}: Released")         │                ║
║ │                                                        │                ║
║ │ # Create 6 threads competing for 3 resources           │                ║
║ │ threads = []                                           │                ║
║ │ for i in range(6):                                     │                ║
║ │     t = threading.Thread(target=worker, args=(i,))     │                ║
║ │     threads.append(t)                                  │                ║
║ │     t.start()                                          │                ║
║ │                                                        │                ║
║ │ for t in threads:                                      │                ║
║ │     t.join()                                           │                ║
║ └────────────────────────────────────────────────────────┘                ║
║                                                                            ║
║ OUTPUT:                                                                    ║
║ Thread-0: Waiting...                                                       ║
║ Thread-0: Got resource! ← Resource 1/3                                     ║
║ Thread-1: Waiting...                                                       ║
║ Thread-1: Got resource! ← Resource 2/3                                     ║
║ Thread-2: Waiting...                                                       ║
║ Thread-2: Got resource! ← Resource 3/3                                     ║
║ Thread-3: Waiting...     ← BLOCKED (all resources in use)                 ║
║ Thread-4: Waiting...     ← BLOCKED                                         ║
║ Thread-5: Waiting...     ← BLOCKED                                         ║
║ Thread-0: Released       ← Resource freed                                  ║
║ Thread-3: Got resource!  ← UNBLOCKED                                       ║
║ ...                                                                        ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ USE CASES:                                                                 ║
║ • Database connection pools                                                ║
║ • Thread pools with limited workers                                        ║
║ • Rate limiting (max N requests per second)                                ║
║ • Resource allocation (printers, network sockets)                          ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### 4. BoundedSemaphore - Strict Resource Counting (a = r)

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                       BoundedSemaphore                                     ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ PROPERTIES:                                                                ║
║ - _value: int                 # Current counter value                      ║
║ - _initial_value: int         # Initial counter value (bound)              ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ CONSTRUCTOR:                                                               ║
║ + __init__(value=1)           # Initialize with resource count             ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ METHODS:                                                                   ║
║ + acquire(blocking=True, timeout=-1) : bool                                ║
║   Decrement counter. Block if counter = 0                                  ║
║                                                                            ║
║ + release() : None                                                         ║
║   Increment counter. Raises ValueError if exceeds initial value            ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ RELATIONSHIP: a = r (Acquisitions = Releases strictly)                     ║
║                                                                            ║
║ Prevents counter from exceeding initial value:                             ║
║   Initial: 3                                                               ║
║   acquire() → 2                                                            ║
║   acquire() → 1                                                            ║
║   release() → 2                                                            ║
║   release() → 3                                                            ║
║   release() → ❌ ValueError! (cannot exceed initial value of 3)            ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DESCRIPTION:                                                               ║
║ Same as Semaphore but prevents over-releasing                              ║
║ Raises ValueError if release() called too many times                       ║
║ Ensures strict resource accounting                                         ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ COMPARISON: Semaphore vs BoundedSemaphore                                  ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ # Regular Semaphore (allows over-release)              │                ║
║ │ sem = threading.Semaphore(2)                           │                ║
║ │ sem.release()  # ✅ OK - counter becomes 3             │                ║
║ │ sem.release()  # ✅ OK - counter becomes 4             │                ║
║ │ # No error! Counter can exceed initial value           │                ║
║ │                                                        │                ║
║ │ # BoundedSemaphore (prevents over-release)             │                ║
║ │ bsem = threading.BoundedSemaphore(2)                   │                ║
║ │ bsem.acquire()  # Counter: 2 -> 1                      │                ║
║ │ bsem.release()  # Counter: 1 -> 2                      │                ║
║ │ bsem.release()  # ❌ ValueError!                       │                ║
║ │ # Error: Cannot exceed initial value                   │                ║
║ └────────────────────────────────────────────────────────┘                ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLE: Parking Lot (5 spaces)                                            ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import threading                                       │                ║
║ │                                                        │                ║
║ │ class ParkingLot:                                      │                ║
║ │     def __init__(self, spaces):                        │                ║
║ │         self.sem = threading.BoundedSemaphore(spaces)  │                ║
║ │                                                        │                ║
║ │     def park(self, car_id):                            │                ║
║ │         print(f"Car-{car_id}: Trying to park")         │                ║
║ │         self.sem.acquire()  # Enter parking lot        │                ║
║ │         print(f"Car-{car_id}: Parked!")                │                ║
║ │                                                        │                ║
║ │     def leave(self, car_id):                           │                ║
║ │         print(f"Car-{car_id}: Leaving")                │                ║
║ │         self.sem.release()  # Exit parking lot         │                ║
║ │         print(f"Car-{car_id}: Left")                   │                ║
║ │                                                        │                ║
║ │ lot = ParkingLot(5)                                    │                ║
║ │ lot.park(1)   # ✅ OK                                  │                ║
║ │ lot.park(2)   # ✅ OK                                  │                ║
║ │ lot.leave(1)  # ✅ OK                                  │                ║
║ │ lot.leave(2)  # ✅ OK                                  │                ║
║ │ # lot.leave(3)  # ❌ ValueError! No car to leave       │                ║
║ └────────────────────────────────────────────────────────┘                ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ ADVANTAGE: Catches programming errors (over-releasing)                     ║
╚═══════════════════════════════════════════════════════════════════════════╝

        Semaphore                    BoundedSemaphore
            │                              │
            │◄──────extends with───────────┘
            │       bounds checking
```

### Synchronization Relationship Summary

```
┌─────────────────────────────────────────────────────────────────────┐
│                    SYNCHRONIZATION RELATIONSHIPS                     │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  a > r  (RLock)           Acquisitions > Releases (temporarily)      │
│  ══════                   Same thread can acquire multiple times     │
│                           before releasing all                       │
│                                                                      │
│  Example:                                                            │
│    rlock.acquire()  # Count: 0 → 1                                  │
│    rlock.acquire()  # Count: 1 → 2  (a=2, r=0, so 2>0 ✓)            │
│    rlock.acquire()  # Count: 2 → 3  (a=3, r=0, so 3>0 ✓)            │
│    rlock.release()  # Count: 3 → 2  (a=3, r=1, so 3>1 ✓)            │
│                                                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  a < r  (Semaphore)       Acquisitions < Resources available         │
│  ══════                   Number of threads that can acquire         │
│                           is limited by resource count               │
│                                                                      │
│  Example (Semaphore(3)):                                             │
│    Initial resources: 3                                              │
│    Thread 1 acquires → 2 left  (1 < 3 ✓)                            │
│    Thread 2 acquires → 1 left  (2 < 3 ✓)                            │
│    Thread 3 acquires → 0 left  (3 < 3... borderline)                │
│    Thread 4 acquires → BLOCKS! (4 < 3 ✗)                            │
│                                                                      │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  a = r  (BoundedSemaphore) Acquisitions = Releases (strictly)        │
│  ══════                    Counter must stay within [0, initial]     │
│                            Prevents over-releasing                   │
│                                                                      │
│  Example (BoundedSemaphore(3)):                                      │
│    acquire()  # Count: 3 → 2  (acquired 1, released 0)              │
│    acquire()  # Count: 2 → 1  (acquired 2, released 0)              │
│    release()  # Count: 1 → 2  (acquired 2, released 1)              │
│    release()  # Count: 2 → 3  (acquired 2, released 2) ✓            │
│    release()  # ❌ ValueError! (would exceed initial value)          │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 💬 Inter-Thread Communication - Class Diagrams

### 1. Event - Simple Signaling

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                             Event                                          ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ PROPERTIES:                                                                ║
║ - _flag: bool                 # Internal boolean flag                      ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ METHODS:                                                                   ║
║ + set() : None                                                             ║
║   Set internal flag to True (GREEN signal 🟢)                              ║
║   All threads waiting on this event are awakened                           ║
║                                                                            ║
║ + clear() : None                                                           ║
║   Reset internal flag to False (RED signal 🔴)                             ║
║   Future wait() calls will block until set() is called                     ║
║                                                                            ║
║ + wait(timeout=None) : bool                                                ║
║   Block until internal flag is True                                        ║
║   Returns immediately if flag is already True                              ║
║   Returns True if event set, False if timeout occurred                     ║
║                                                                            ║
║ + is_set() : bool                                                          ║
║   Return True if flag is True, False otherwise                             ║
║   Non-blocking check of event status                                       ║
║                                                                            ║
║ + isSet() : bool                                                           ║
║   Deprecated alias for is_set()                                            ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DESCRIPTION:                                                               ║
║ Simple synchronization primitive with boolean flag                         ║
║ Think of it as a traffic light: 🟢 Green (go) / 🔴 Red (stop)             ║
║ One thread signals, multiple threads can wait                              ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLE: Traffic Signal System                                             ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import threading                                       │                ║
║ │ import time                                            │                ║
║ │                                                        │                ║
║ │ # Create event (initially False/RED)                   │                ║
║ │ traffic_light = threading.Event()                      │                ║
║ │                                                        │                ║
║ │ def traffic_controller():                              │                ║
║ │     print("🔴 RED light - cars must wait")             │                ║
║ │     time.sleep(3)                                      │                ║
║ │                                                        │                ║
║ │     print("🟢 GREEN light - cars can go!")             │                ║
║ │     traffic_light.set()  # Set flag to True            │                ║
║ │     time.sleep(2)                                      │                ║
║ │                                                        │                ║
║ │     print("🔴 RED light again")                        │                ║
║ │     traffic_light.clear()  # Reset flag to False       │                ║
║ │                                                        │                ║
║ │ def car(car_id):                                       │                ║
║ │     print(f"Car-{car_id}: Waiting at intersection")    │                ║
║ │     traffic_light.wait()  # Block until green          │                ║
║ │     print(f"Car-{car_id}: ✅ Crossing!")               │                ║
║ │                                                        │                ║
║ │ # Start controller                                     │                ║
║ │ controller = threading.Thread(                         │                ║
║ │     target=traffic_controller)                         │                ║
║ │ controller.start()                                     │                ║
║ │                                                        │                ║
║ │ # Start cars                                           │                ║
║ │ for i in range(5):                                     │                ║
║ │     t = threading.Thread(target=car, args=(i,))        │                ║
║ │     t.start()                                          │                ║
║ └────────────────────────────────────────────────────────┘                ║
║                                                                            ║
║ OUTPUT:                                                                    ║
║ 🔴 RED light - cars must wait                                              ║
║ Car-0: Waiting at intersection                                             ║
║ Car-1: Waiting at intersection                                             ║
║ Car-2: Waiting at intersection                                             ║
║ Car-3: Waiting at intersection                                             ║
║ Car-4: Waiting at intersection                                             ║
║ 🟢 GREEN light - cars can go!                                              ║
║ Car-0: ✅ Crossing!                                                        ║
║ Car-1: ✅ Crossing!                                                        ║
║ Car-2: ✅ Crossing!                                                        ║
║ Car-3: ✅ Crossing!                                                        ║
║ Car-4: ✅ Crossing!                                                        ║
║ 🔴 RED light again                                                         ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ STATE DIAGRAM:                                                             ║
║                                                                            ║
║    ┌─────────────────┐       set()        ┌─────────────────┐            ║
║    │   Flag = False  │ ─────────────────> │   Flag = True   │            ║
║    │   (RED 🔴)      │                     │   (GREEN 🟢)    │            ║
║    │                 │ <───────────────── │                 │            ║
║    │ wait() BLOCKS   │      clear()       │  wait() RETURNS │            ║
║    └─────────────────┘                    └─────────────────┘            ║
║                                                                            ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ USE CASES:                                                                 ║
║ • Start/stop signals                                                       ║
║ • Initialization complete notification                                     ║
║ • Shutdown signals                                                         ║
║ • Simple producer-consumer signaling                                       ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### 2. Condition - Advanced Wait/Notify

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                           Condition                                        ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ PROPERTIES:                                                                ║
║ - _lock: RLock                # Underlying RLock                           ║
║ - _waiters: list              # List of waiting threads                    ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ CONSTRUCTOR:                                                               ║
║ + __init__(lock=None)         # Create with optional lock                  ║
║                               # Uses RLock by default                      ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ METHODS:                                                                   ║
║ + acquire(*args) : None                                                    ║
║   Acquire the underlying lock                                              ║
║   Must be called before wait() or notify()                                 ║
║                                                                            ║
║ + release() : None                                                         ║
║   Release the underlying lock                                              ║
║   Should be called after wait() or notify()                                ║
║                                                                            ║
║ + wait(timeout=None) : bool                                                ║
║   Release lock and wait until notified or timeout                          ║
║   Automatically re-acquires lock before returning                          ║
║   Must be called with lock held                                            ║
║                                                                            ║
║ + notify(n=1) : None                                                       ║
║   Wake up at most n threads waiting on this condition                      ║
║   Default n=1 wakes up one thread                                          ║
║   Must be called with lock held                                            ║
║                                                                            ║
║ + notify_all() : None                                                      ║
║   Wake up all threads waiting on this condition                            ║
║   Must be called with lock held                                            ║
║                                                                            ║
║ + notifyAll() : None                                                       ║
║   Deprecated alias for notify_all()                                        ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DESCRIPTION:                                                               ║
║ More sophisticated version of Event                                        ║
║ Allows complex wait/notify patterns                                        ║
║ Internally uses RLock for synchronization                                  ║
║ Supports selective notification (notify vs notify_all)                     ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLE: Producer-Consumer Pattern                                         ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import threading                                       │                ║
║ │ import time                                            │                ║
║ │                                                        │                ║
║ │ class SharedBuffer:                                    │                ║
║ │     def __init__(self):                                │                ║
║ │         self.buffer = []                               │                ║
║ │         self.condition = threading.Condition()         │                ║
║ │         self.MAX_SIZE = 5                              │                ║
║ │                                                        │                ║
║ │     def produce(self, item, name):                     │                ║
║ │         self.condition.acquire()  # ← Acquire lock     │                ║
║ │                                                        │                ║
║ │         # Wait if buffer is full                       │                ║
║ │         while len(self.buffer) >= self.MAX_SIZE:       │                ║
║ │             print(f"{name}: Buffer full, waiting...")  │                ║
║ │             self.condition.wait()  # ← Wait            │                ║
║ │                                                        │                ║
║ │         # Produce item                                 │                ║
║ │         self.buffer.append(item)                       │                ║
║ │         print(f"{name}: Produced {item}")              │                ║
║ │                                                        │                ║
║ │         # Notify consumers                             │                ║
║ │         self.condition.notify()  # ← Wake 1 consumer   │                ║
║ │                                                        │                ║
║ │         self.condition.release()  # ← Release lock     │                ║
║ │                                                        │                ║
║ │     def consume(self, name):                           │                ║
║ │         self.condition.acquire()  # ← Acquire lock     │                ║
║ │                                                        │                ║
║ │         # Wait if buffer is empty                      │                ║
║ │         while len(self.buffer) == 0:                   │                ║
║ │             print(f"{name}: Buffer empty, waiting...")  │                ║
║ │             self.condition.wait()  # ← Wait            │                ║
║ │                                                        │                ║
║ │         # Consume item                                 │                ║
║ │         item = self.buffer.pop(0)                      │                ║
║ │         print(f"{name}: Consumed {item}")              │                ║
║ │                                                        │                ║
║ │         # Notify producers                             │                ║
║ │         self.condition.notify()  # ← Wake 1 producer   │                ║
║ │                                                        │                ║
║ │         self.condition.release()  # ← Release lock     │                ║
║ │         return item                                    │                ║
║ │                                                        │                ║
║ │ # Usage                                                │                ║
║ │ buffer = SharedBuffer()                                │                ║
║ │                                                        │                ║
║ │ def producer(name):                                    │                ║
║ │     for i in range(10):                                │                ║
║ │         buffer.produce(f"Item-{i}", name)              │                ║
║ │         time.sleep(0.1)                                │                ║
║ │                                                        │                ║
║ │ def consumer(name):                                    │                ║
║ │     for i in range(5):                                 │                ║
║ │         buffer.consume(name)                           │                ║
║ │         time.sleep(0.2)                                │                ║
║ │                                                        │                ║
║ │ p = threading.Thread(target=producer,                  │                ║
║ │                      args=("Producer",))               │                ║
║ │ c1 = threading.Thread(target=consumer,                 │                ║
║ │                       args=("Consumer-1",))            │                ║
║ │ c2 = threading.Thread(target=consumer,                 │                ║
║ │                       args=("Consumer-2",))            │                ║
║ │                                                        │                ║
║ │ p.start(); c1.start(); c2.start()                      │                ║
║ │ p.join(); c1.join(); c2.join()                         │                ║
║ └────────────────────────────────────────────────────────┘                ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ CONDITION vs EVENT:                                                        ║
║                                                                            ║
║ EVENT:                                      CONDITION:                     ║
║ • Simple binary flag                        • Complex conditions           ║
║ • set() wakes ALL waiters                   • notify(n) wakes n waiters    ║
║ • No lock management needed                 • Requires lock management     ║
║ • Best for: Go/Stop signals                 • Best for: Producer-consumer  ║
║                                                                            ║
║         Event                      Condition                               ║
║           │                           │                                    ║
║           │◄────more sophisticated────┘                                    ║
║                                                                            ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ INTERNAL STRUCTURE:                                                        ║
║                                                                            ║
║    Condition                                                               ║
║       │                                                                    ║
║       ├──> uses ──> RLock (for mutual exclusion)                           ║
║       │                                                                    ║
║       └──> manages ──> Waiter list (threads waiting)                       ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### 3. Queue Module - Thread-Safe Data Exchange

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                      <<module>> import queue                               ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                         Queue Class Hierarchy                              ║
║                                                                            ║
║                            QueueBase                                       ║
║                                │                                           ║
║                    ┌───────────┼───────────┐                              ║
║                    │           │           │                               ║
║                  Queue    LifoQueue  PriorityQueue                         ║
║                  (FIFO)     (LIFO)     (Priority)                          ║
╚═══════════════════════════════════════════════════════════════════════════╝

╔═══════════════════════════════════════════════════════════════════════════╗
║                       <<abstract class>>                                   ║
║                          QueueBase                                         ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ PROPERTIES:                                                                ║
║ # maxsize: int                # Maximum size (0 = unlimited)               ║
║ # _queue: deque               # Internal data structure                    ║
║ # _mutex: Lock                # Lock for synchronization                   ║
║ # _not_empty: Condition       # Condition for consumers                    ║
║ # _not_full: Condition        # Condition for producers                    ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ COMMON METHODS (All Queue Types):                                          ║
║                                                                            ║
║ + put(item, block=True, timeout=None) : None                               ║
║   Put an item into the queue                                               ║
║   If full and block=True, wait until space available                       ║
║   Raises queue.Full if timeout expires                                     ║
║                                                                            ║
║ + get(block=True, timeout=None) : Any                                      ║
║   Remove and return an item from the queue                                 ║
║   If empty and block=True, wait until item available                       ║
║   Raises queue.Empty if timeout expires                                    ║
║                                                                            ║
║ + empty() : bool                                                           ║
║   Return True if queue is empty                                            ║
║   ⚠️ Not reliable for synchronization (race condition)                    ║
║                                                                            ║
║ + full() : bool                                                            ║
║   Return True if queue is full                                             ║
║   ⚠️ Not reliable for synchronization (race condition)                    ║
║                                                                            ║
║ + qsize() : int                                                            ║
║   Return approximate size of queue                                         ║
║   ⚠️ May not be accurate due to concurrent access                         ║
║                                                                            ║
║ + task_done() : None                                                       ║
║   Indicate that a formerly enqueued task is complete                       ║
║   Used by consumer threads                                                 ║
║                                                                            ║
║ + join() : None                                                            ║
║   Block until all items have been processed                                ║
║   Waits for all task_done() calls                                          ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ INTERNAL SYNCHRONIZATION:                                                  ║
║                                                                            ║
║   Queue uses internally:                                                   ║
║      • 1 Lock (_mutex)                                                     ║
║      • 2 Condition variables (_not_empty, _not_full)                       ║
║                                                                            ║
║   This makes all queue operations thread-safe automatically!               ║
╚═══════════════════════════════════════════════════════════════════════════╝

╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                        queue.Queue                                         ║
║                      (FIFO - First In First Out)                           ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ CONSTRUCTOR:                                                               ║
║ + __init__(maxsize=0)         # 0 = unlimited size                         ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DESCRIPTION:                                                               ║
║ FIFO queue - First item added is first item retrieved                      ║
║ Like a line at a store - first person in line is served first              ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ BEHAVIOR:                                                                  ║
║                                                                            ║
║   put("A") ─┐                                                              ║
║   put("B") ─┤───> [A, B, C] ───> get() returns "A" (first in)             ║
║   put("C") ─┘                                                              ║
║                                                                            ║
║   Order: A → B → C → get() → A (FIFO)                                     ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLE: Task Distribution System                                          ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import queue                                           │                ║
║ │ import threading                                       │                ║
║ │ import time                                            │                ║
║ │                                                        │                ║
║ │ # Create FIFO queue                                    │                ║
║ │ task_queue = queue.Queue(maxsize=10)                   │                ║
║ │                                                        │                ║
║ │ def producer(name):                                    │                ║
║ │     for i in range(5):                                 │                ║
║ │         task = f"Task-{i}"                             │                ║
║ │         task_queue.put(task)  # Add to queue           │                ║
║ │         print(f"{name}: Added {task}")                 │                ║
║ │         time.sleep(0.2)                                │                ║
║ │                                                        │                ║
║ │ def consumer(name):                                    │                ║
║ │     while True:                                        │                ║
║ │         try:                                           │                ║
║ │             task = task_queue.get(timeout=2)           │                ║
║ │             print(f"{name}: Processing {task}")        │                ║
║ │             time.sleep(0.5)                            │                ║
║ │             task_queue.task_done()                     │                ║
║ │         except queue.Empty:                            │                ║
║ │             break                                      │                ║
║ │                                                        │                ║
║ │ p = threading.Thread(target=producer,                  │                ║
║ │                      args=("Producer",))               │                ║
║ │ c1 = threading.Thread(target=consumer,                 │                ║
║ │                       args=("Consumer-1",))            │                ║
║ │ c2 = threading.Thread(target=consumer,                 │                ║
║ │                       args=("Consumer-2",))            │                ║
║ │                                                        │                ║
║ │ p.start(); c1.start(); c2.start()                      │                ║
║ │ p.join(); c1.join(); c2.join()                         │                ║
║ └────────────────────────────────────────────────────────┘                ║
║                                                                            ║
║ OUTPUT (FIFO order maintained):                                            ║
║ Producer: Added Task-0                                                     ║
║ Consumer-1: Processing Task-0  ← First in                                  ║
║ Producer: Added Task-1                                                     ║
║ Consumer-2: Processing Task-1  ← Second in                                 ║
║ Producer: Added Task-2                                                     ║
║ Consumer-1: Processing Task-2  ← Third in                                  ║
║ ...                                                                        ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ USE CASES:                                                                 ║
║ • Task queues / Job processing                                             ║
║ • Message passing between threads                                          ║
║ • Request handling (web servers)                                           ║
║ • Event processing                                                         ║
╚═══════════════════════════════════════════════════════════════════════════╝

╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                       queue.LifoQueue                                      ║
║                      (LIFO - Last In First Out / Stack)                    ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ CONSTRUCTOR:                                                               ║
║ + __init__(maxsize=0)         # 0 = unlimited size                         ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DESCRIPTION:                                                               ║
║ LIFO queue (Stack) - Last item added is first item retrieved               ║
║ Like a stack of plates - last plate added is first taken                   ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ BEHAVIOR:                                                                  ║
║                                                                            ║
║   put("A") ─┐                                                              ║
║   put("B") ─┤───> [A, B, C] ───> get() returns "C" (last in)              ║
║   put("C") ─┘                                                              ║
║                                                                            ║
║   Order: A → B → C → get() → C (LIFO)                                     ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLE: Stack Operations                                                  ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import queue                                           │                ║
║ │                                                        │                ║
║ │ # Create LIFO queue (Stack)                            │                ║
║ │ stack = queue.LifoQueue()                              │                ║
║ │                                                        │                ║
║ │ # Push items                                           │                ║
║ │ stack.put("First")                                     │                ║
║ │ stack.put("Second")                                    │                ║
║ │ stack.put("Third")                                     │                ║
║ │                                                        │                ║
║ │ # Pop items (LIFO order)                               │                ║
║ │ print(stack.get())  # Output: "Third"  ← Last pushed   │                ║
║ │ print(stack.get())  # Output: "Second"                 │                ║
║ │ print(stack.get())  # Output: "First"  ← First pushed  │                ║
║ └────────────────────────────────────────────────────────┘                ║
║                                                                            ║
║ VISUALIZATION:                                                             ║
║                                                                            ║
║   Stack growth:                                                            ║
║                                                                            ║
║   put("A")     put("B")     put("C")        get()                          ║
║   ───────      ───────      ───────         ─────                          ║
║                   ┌───┐       ┌───┐                                        ║
║                   │ B │       │ C │ ← Top   Returns "C"                    ║
║                   ├───┤       ├───┤                                        ║
║   ┌───┐       ┌───┐       ┌───┐                                           ║
║   │ A │       │ A │       │ B │                                            ║
║   └───┘       ├───┤       ├───┤                                            ║
║               │ A │       │ A │                                            ║
║               └───┘       └───┘                                            ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ USE CASES:                                                                 ║
║ • Undo/Redo operations                                                     ║
║ • Backtracking algorithms                                                  ║
║ • Depth-first traversal                                                    ║
║ • Function call stack simulation                                           ║
╚═══════════════════════════════════════════════════════════════════════════╝

╔═══════════════════════════════════════════════════════════════════════════╗
║                            <<class>>                                       ║
║                     queue.PriorityQueue                                    ║
║                    (Priority-Based Ordering)                               ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ CONSTRUCTOR:                                                               ║
║ + __init__(maxsize=0)         # 0 = unlimited size                         ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ DESCRIPTION:                                                               ║
║ Items retrieved based on priority (lowest value first)                     ║
║ Items should be tuples: (priority, data)                                   ║
║ Uses heap queue algorithm (heapq) internally                               ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ BEHAVIOR:                                                                  ║
║                                                                            ║
║   put((3, "Low"))  ─┐                                                      ║
║   put((1, "High")) ─┤───> Sorted by priority                               ║
║   put((2, "Med"))  ─┘                                                      ║
║                                                                            ║
║   get() returns (1, "High")   ← Lowest priority number = Highest priority ║
║   get() returns (2, "Med")                                                 ║
║   get() returns (3, "Low")                                                 ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ EXAMPLE: Task Scheduling by Priority                                       ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import queue                                           │                ║
║ │ import threading                                       │                ║
║ │                                                        │                ║
║ │ # Create priority queue                                │                ║
║ │ pq = queue.PriorityQueue()                             │                ║
║ │                                                        │                ║
║ │ # Add tasks with priorities                            │                ║
║ │ # (lower number = higher priority)                     │                ║
║ │ pq.put((3, "Task: Database backup"))                   │                ║
║ │ pq.put((1, "Task: Critical security patch"))           │                ║
║ │ pq.put((2, "Task: Update user profile"))               │                ║
║ │ pq.put((1, "Task: Fix production bug"))                │                ║
║ │ pq.put((5, "Task: Clean temp files"))                  │                ║
║ │                                                        │                ║
║ │ print("Processing tasks by priority:")                 │                ║
║ │ while not pq.empty():                                  │                ║
║ │     priority, task = pq.get()                          │                ║
║ │     print(f"Priority {priority}: {task}")              │                ║
║ └────────────────────────────────────────────────────────┘                ║
║                                                                            ║
║ OUTPUT (Sorted by priority):                                               ║
║ Processing tasks by priority:                                              ║
║ Priority 1: Task: Critical security patch  ← Highest priority              ║
║ Priority 1: Task: Fix production bug                                       ║
║ Priority 2: Task: Update user profile                                      ║
║ Priority 3: Task: Database backup                                          ║
║ Priority 5: Task: Clean temp files         ← Lowest priority               ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ ADVANCED EXAMPLE: Complex Priority Items                                   ║
║ ┌────────────────────────────────────────────────────────┐                ║
║ │ import queue                                           │                ║
║ │ from dataclasses import dataclass, field               │                ║
║ │ from typing import Any                                 │                ║
║ │                                                        │                ║
║ │ @dataclass(order=True)                                 │                ║
║ │ class Task:                                            │                ║
║ │     priority: int                                      │                ║
║ │     item: Any = field(compare=False)                   │                ║
║ │                                                        │                ║
║ │ pq = queue.PriorityQueue()                             │                ║
║ │ pq.put(Task(3, {"name": "backup", "size": 100}))       │                ║
║ │ pq.put(Task(1, {"name": "critical", "size": 10}))      │                ║
║ │                                                        │                ║
║ │ task = pq.get()  # Gets Task with priority 1           │                ║
║ └────────────────────────────────────────────────────────┘                ║
╠═══════════════════════════════════════════════════════════════════════════╣
║ USE CASES:                                                                 ║
║ • Task scheduling (high-priority tasks first)                              ║
║ • Event handling (critical events first)                                   ║
║ • Resource allocation (urgent requests first)                              ║
║ • Job scheduling systems                                                   ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

### Queue Comparison Summary

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        QUEUE TYPES COMPARISON                            │
├────────────┬──────────────┬─────────────────────┬──────────────────────┤
│ Queue Type │ Order        │ Retrieval Behavior   │ Use Case             │
├────────────┼──────────────┼─────────────────────┼──────────────────────┤
│            │              │                     │                      │
│  Queue     │ FIFO         │ First added,        │ • Job queues         │
│            │ First In     │ first retrieved     │ • Message passing    │
│            │ First Out    │                     │ • Request handling   │
│            │              │  [A,B,C] → get()→A  │ • Event processing   │
│            │              │                     │                      │
├────────────┼──────────────┼─────────────────────┼──────────────────────┤
│            │              │                     │                      │
│ LifoQueue  │ LIFO         │ Last added,         │ • Undo operations    │
│  (Stack)   │ Last In      │ first retrieved     │ • Backtracking       │
│            │ First Out    │                     │ • DFS traversal      │
│            │              │  [A,B,C] → get()→C  │ • Call stack sim     │
│            │              │                     │                      │
├────────────┼──────────────┼─────────────────────┼──────────────────────┤
│            │              │                     │                      │
│ Priority   │ Priority     │ Lowest priority     │ • Task scheduling    │
│  Queue     │ Based        │ number retrieved    │ • Event handling     │
│            │              │ first               │ • Resource alloc     │
│            │              │  [(3,X),(1,Y),(2,Z)]│ • Job scheduling     │
│            │              │  → get() → (1,Y)    │                      │
│            │              │                     │                      │
└────────────┴──────────────┴─────────────────────┴──────────────────────┘

All three queue types are THREAD-SAFE (no manual locking needed)!
Internal synchronization handled automatically using Lock + Condition.
```

---

## 📊 Comprehensive Method Summary Table

```
╔═══════════════════════════════════════════════════════════════════════════╗
║            COMPLETE PYTHON MULTITHREADING METHOD REFERENCE                 ║
╠══════════════╦════════════════════╦═══════════════════════════════════════╣
║  Component   ║  Method/Property   ║  Description                          ║
╠══════════════╬════════════════════╬═══════════════════════════════════════╣
║              ║                    ║                                       ║
║              ║ __init__()         ║ Create thread object                  ║
║              ║ start()            ║ Begin thread execution                ║
║              ║ run()              ║ Method executed by thread             ║
║  Thread      ║ join()             ║ Wait for thread completion            ║
║  Class       ║ is_alive()         ║ Check if thread is running            ║
║              ║ getName() / name   ║ Get thread name                       ║
║              ║ setName() / name   ║ Set thread name                       ║
║              ║ ident              ║ Thread ID (read-only)                 ║
║              ║ daemon/setDaemon() ║ Daemon thread flag                    ║
║              ║                    ║                                       ║
╠══════════════╬════════════════════╬═══════════════════════════════════════╣
║              ║                    ║                                       ║
║  Global      ║ active_count()     ║ Count active threads                  ║
║  Functions   ║ enumerate()        ║ List all active threads               ║
║              ║ current_thread()   ║ Get current thread object             ║
║              ║                    ║                                       ║
╠══════════════╬════════════════════╬═══════════════════════════════════════╣
║              ║                    ║                                       ║
║  Lock        ║ acquire()          ║ Acquire lock (blocking)               ║
║              ║ release()          ║ Release lock                          ║
║              ║ locked()           ║ Check if locked                       ║
║              ║                    ║                                       ║
╠══════════════╬════════════════════╬═══════════════════════════════════════╣
║              ║                    ║                                       ║
║  RLock       ║ acquire()          ║ Acquire lock (recursive)              ║
║  (a > r)     ║ release()          ║ Release lock (recursive)              ║
║              ║                    ║ Same thread can acquire multiple      ║
║              ║                    ║                                       ║
╠══════════════╬════════════════════╬═══════════════════════════════════════╣
║              ║                    ║                                       ║
║  Semaphore   ║ acquire()          ║ Decrement counter                     ║
║  (a < r)     ║ release()          ║ Increment counter                     ║
║              ║                    ║ Allows N concurrent threads           ║
║              ║                    ║                                       ║
╠══════════════╬════════════════════╬═══════════════════════════════════════╣
║              ║                    ║                                       ║
║ Bounded      ║ acquire()          ║ Decrement counter (bounded)           ║
║ Semaphore    ║ release()          ║ Increment counter (bounded)           ║
║  (a = r)     ║                    ║ Prevents over-releasing               ║
║              ║                    ║                                       ║
╠══════════════╬════════════════════╬═══════════════════════════════════════╣
║              ║                    ║                                       ║
║  Event       ║ set()              ║ Set flag to True (GREEN)              ║
║              ║ clear()            ║ Set flag to False (RED)               ║
║              ║ wait()             ║ Block until flag is True              ║
║              ║ is_set()           ║ Check flag status                     ║
║              ║                    ║                                       ║
╠══════════════╬════════════════════╬═══════════════════════════════════════╣
║              ║                    ║                                       ║
║  Condition   ║ acquire()          ║ Acquire underlying lock               ║
║              ║ release()          ║ Release underlying lock               ║
║              ║ wait()             ║ Release lock and wait                 ║
║              ║ notify(n)          ║ Wake up n waiting threads             ║
║              ║ notify_all()       ║ Wake up all waiting threads           ║
║              ║                    ║                                       ║
╠══════════════╬════════════════════╬═══════════════════════════════════════╣
║              ║                    ║                                       ║
║              ║ Queue()            ║ Create FIFO queue                     ║
║              ║ LifoQueue()        ║ Create LIFO queue (Stack)             ║
║  Queue       ║ PriorityQueue()    ║ Create priority queue                 ║
║  Module      ║ put()              ║ Add item to queue                     ║
║              ║ get()              ║ Remove item from queue                ║
║              ║ empty()            ║ Check if empty                        ║
║              ║ qsize()            ║ Get approximate size                  ║
║              ║                    ║                                       ║
╚══════════════╩════════════════════╩═══════════════════════════════════════╝
```

---

## ⚡ Quick Reference - When to Use What

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         DECISION GUIDE                                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  SCENARIO                                    SOLUTION                    │
│  ────────────────────────────────────────────────────────────────────── │
│                                                                          │
│  One thread at a time (simple)               Lock                       │
│                                                                          │
│  Recursive function calls                    RLock                      │
│                                                                          │
│  Limited resource pool (N items)             Semaphore(N)               │
│                                                                          │
│  Strict resource tracking                    BoundedSemaphore(N)        │
│                                                                          │
│  Simple go/stop signal                       Event                      │
│                                                                          │
│  Producer-consumer (complex)                 Condition or Queue         │
│                                                                          │
│  Task distribution (FIFO)                    Queue                      │
│                                                                          │
│  Undo operations (LIFO)                      LifoQueue                  │
│                                                                          │
│  Priority-based processing                   PriorityQueue              │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## ⚠️ Common Pitfalls & Best Practices

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                          COMMON PITFALLS                                   ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║ 1. DEADLOCK with Regular Lock                                              ║
║    ❌ WRONG:                          ✅ CORRECT:                          ║
║    lock = Lock()                     rlock = RLock()                       ║
║    lock.acquire()                    rlock.acquire()                       ║
║    lock.acquire() # DEADLOCK!        rlock.acquire() # OK!                 ║
║                                                                            ║
║ 2. Forgetting to Release                                                   ║
║    ❌ WRONG:                          ✅ CORRECT:                          ║
║    lock.acquire()                    lock.acquire()                        ║
║    # work                            try:                                  ║
║    # forgot release!                     # work                            ║
║                                      finally:                              ║
║                                          lock.release()                    ║
║                                                                            ║
║ 3. Daemon Thread Data Loss                                                 ║
║    ❌ WRONG:                          ✅ CORRECT:                          ║
║    t = Thread(target=save_data,      t = Thread(target=save_data)         ║
║                daemon=True)          t.start()                             ║
║    t.start()                         t.join() # Wait for completion        ║
║    # Main exits, data lost!          # Data safely saved                   ║
║                                                                            ║
║ 4. Race Condition with locked()                                            ║
║    ❌ WRONG:                          ✅ CORRECT:                          ║
║    if not lock.locked():             lock.acquire() # Just acquire         ║
║        lock.acquire() # Race!        try:                                  ║
║                                          # work                            ║
║                                      finally:                              ║
║                                          lock.release()                    ║
║                                                                            ║
║ 5. Queue empty() Race Condition                                            ║
║    ❌ WRONG:                          ✅ CORRECT:                          ║
║    if not q.empty():                 try:                                  ║
║        item = q.get() # Race!            item = q.get(timeout=1)           ║
║                                      except queue.Empty:                   ║
║                                          # handle                          ║
║                                                                            ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                          BEST PRACTICES                                    ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║ ✓ Always use try-finally for lock release                                 ║
║ ✓ Use context managers (with lock:) when possible                          ║
║ ✓ Use RLock for recursive scenarios                                        ║
║ ✓ Use Queue for producer-consumer (simplest and safest)                    ║
║ ✓ Use join() to wait for important threads                                 ║
║ ✓ Avoid daemon threads for I/O operations                                  ║
║ ✓ Always acquire locks in consistent order (prevent deadlock)              ║
║ ✓ Use timeout parameters to prevent indefinite blocking                    ║
║ ✓ Name threads for easier debugging                                        ║
║ ✓ Keep critical sections short                                             ║
║                                                                            ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

---

## 🎓 Complete Example: Multi-Component System

```python
"""
Complete example demonstrating all threading concepts:
- Thread creation and lifecycle
- Synchronization with Lock and Semaphore
- Communication with Queue
- Proper cleanup and error handling
"""

import threading
import queue
import time
import random

# ============================================
# SHARED RESOURCES
# ============================================

# Semaphore for limiting concurrent workers
worker_pool = threading.Semaphore(3)  # Max 3 concurrent workers

# Lock for protecting shared counter
counter_lock = threading.Lock()
completed_tasks = 0

# Queue for task distribution
task_queue = queue.Queue()

# Event for shutdown signal
shutdown_event = threading.Event()

# ============================================
# WORKER THREADS
# ============================================

def worker(worker_id):
    """Worker thread that processes tasks from queue"""
    print(f"Worker-{worker_id}: Started (ID: {threading.current_thread().ident})")

    while not shutdown_event.is_set():
        try:
            # Get task from queue (with timeout)
            task = task_queue.get(timeout=1)

            # Acquire semaphore (limit concurrent workers)
            worker_pool.acquire()
            print(f"Worker-{worker_id}: Acquired resource, processing {task}")

            try:
                # Simulate work
                time.sleep(random.uniform(0.5, 1.5))
                print(f"Worker-{worker_id}: Completed {task}")

                # Update shared counter (with lock)
                global completed_tasks
                counter_lock.acquire()
                try:
                    completed_tasks += 1
                finally:
                    counter_lock.release()

            finally:
                # Always release semaphore
                worker_pool.release()
                task_queue.task_done()

        except queue.Empty:
            # Queue empty, continue checking for shutdown
            continue

    print(f"Worker-{worker_id}: Shutting down")

# ============================================
# PRODUCER THREAD
# ============================================

def producer(num_tasks):
    """Producer thread that adds tasks to queue"""
    print("Producer: Started")

    for i in range(num_tasks):
        task = f"Task-{i+1}"
        task_queue.put(task)
        print(f"Producer: Added {task}")
        time.sleep(0.2)

    print("Producer: All tasks added")

# ============================================
# MAIN PROGRAM
# ============================================

def main():
    print("=== Multi-threaded Task Processing System ===\n")
    print(f"Main thread: {threading.current_thread().name}")
    print(f"Initial active threads: {threading.active_count()}\n")

    # Create worker threads
    num_workers = 5
    workers = []
    for i in range(num_workers):
        t = threading.Thread(target=worker, args=(i+1,), name=f"Worker-{i+1}")
        t.start()
        workers.append(t)

    # Create producer thread
    num_tasks = 15
    producer_thread = threading.Thread(target=producer, args=(num_tasks,))
    producer_thread.start()

    # Wait for producer to finish
    producer_thread.join()
    print("\nProducer finished. Waiting for workers to complete tasks...")

    # Wait for all tasks to be processed
    task_queue.join()
    print("All tasks completed!")

    # Signal shutdown to workers
    shutdown_event.set()
    print("Shutdown signal sent to workers")

    # Wait for all workers to finish
    for t in workers:
        t.join()

    # Print final statistics
    print(f"\n=== Final Statistics ===")
    print(f"Total tasks completed: {completed_tasks}")
    print(f"Active threads: {threading.active_count()}")
    print(f"All threads: {[t.name for t in threading.enumerate()]}")

if __name__ == "__main__":
    main()
```

---

## 📚 Diagram Legend & Notation

```
╔═══════════════════════════════════════════════════════════════════════════╗
║                          UML NOTATION GUIDE                                ║
╠═══════════════════════════════════════════════════════════════════════════╣
║                                                                            ║
║  <<class>>              Class or interface                                 ║
║  <<abstract class>>     Abstract base class                                ║
║  <<module>>             Python module                                      ║
║                                                                            ║
║  + method()             Public method                                      ║
║  - property             Private property                                   ║
║  # property             Protected property                                 ║
║  <<static>> method()    Static method                                      ║
║                                                                            ║
║  ────>                  Inheritance (extends)                              ║
║  ····>                  Uses/Dependency                                    ║
║  ◆────>                 Composition (has-a)                                ║
║  *────>                 Aggregation                                        ║
║                                                                            ║
║  <i>text</i>            Italicized explanation                             ║
║  <b>text</b>            Bold emphasis                                      ║
║  ┌──────┐               Code example box                                   ║
║  │ code │               (dotted border)                                    ║
║  └──────┘                                                                  ║
║                                                                            ║
║  a > r                  Acquisitions > Releases (RLock)                    ║
║  a < r                  Acquisitions < Resources (Semaphore)               ║
║  a = r                  Acquisitions = Resources (BoundedSemaphore)        ║
║                                                                            ║
║  🟢                     Green signal (set/go)                              ║
║  🔴                     Red signal (clear/stop)                            ║
║  ✅                     Correct/works                                      ║
║  ❌                     Wrong/error                                        ║
║  ⚠️                     Warning/caution                                    ║
║                                                                            ║
╚═══════════════════════════════════════════════════════════════════════════╝
```

---

## 🎯 Summary

This comprehensive UML diagram covers **every aspect** of Python multithreading:

✓ **Thread Lifecycle** - Complete state transitions
✓ **Thread Class** - All methods, properties, and creation patterns
✓ **Global Functions** - active_count(), enumerate(), current_thread()
✓ **Synchronization** - Lock, RLock, Semaphore, BoundedSemaphore
✓ **Relationships** - a>r, a<r, a=r explained with examples
✓ **Communication** - Event, Condition, Queue (FIFO/LIFO/Priority)
✓ **Practical Examples** - Real-world code for every concept
✓ **Best Practices** - Common pitfalls and solutions

**This is your single source of truth for teaching Python multithreading!**

---

*Created for educational purposes - Use as reference for Python threading concepts*
