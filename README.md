
# Parallel-Computer-Architecture Assignment 

The objective of this project is to build a machine learning model that predicts gold prices by leveraging parallel computing techniques. The project aims to highlight the efficiency gains and pitfalls of parallel execution by demonstrating concepts like race conditions, synchronization, and performance optimization.

## Race Condition Analysis
The focus is on identifying variables that may lead to race conditions during concurrent execution.

### Variables Prone to Race Conditions:
- **gold_price** – Simultaneously modified by `increment_price` and `decrement_price` functions.  
- **gold_volume** – Modified concurrently by `increment_volume` and `decrement_volume` threads.  

### Cause of Race Conditions:
These variables are shared resources accessed by multiple threads without synchronization. Since the operations (`+=` and `-=`) are not atomic, they result in overlapping reads and writes, leading to unpredictable final values.

```python
gold_price = 1500
gold_volume = 100

def increment_price():
    global gold_price
    for _ in range(100000):
        gold_price += 1

def decrement_price():
    global gold_price
    for _ in range(100000):
       gold_price -= 1 
```

This block demonstrates how simultaneous access to `gold_price` by two functions creates a race condition.

---

## Execution of Code with Race Conditions:
### Outcome:
The unhandled race condition led to discrepancies in the predicted gold prices and volume. Observed values fluctuated due to concurrent updates by different threads, resulting in unpredictable behavior.

### Execution Time:
The initial code (without synchronization) executed in approximately **0.2503 seconds**. Despite faster performance, the results were inconsistent, which confirmed the existence of race conditions.

---

## Synchronization and Performance Testing:
### Solution Approaches:
1. **Without Handling (Fastest but Inconsistent):**  
   - **Execution time:** ~**0.0886 seconds**  
   - Race conditions remained, leading to inconsistent results.

2. **Using `threading.Lock` (Critical Section):**  
   - **Execution time:** ~**0.1500 seconds**  
   - Locks ensured that only one thread modified the shared resource at a time, ensuring data integrity but with slight performance degradation.

3. **Atomic Operations (Potential Future Implementation):**  
   Could offer fast, thread-safe updates by ensuring atomicity without blocking threads.  

4. **Reduction (Thread Independence):**  
   Each thread works independently, and results are aggregated at the end. This method minimizes contention and scales well across multiple cores.

---

## Performance Analysis with Different Cores:
### Results on 1, 2, 4, 8 Cores:
- **No Lock:** Fastest execution on all cores but led to data corruption.  
- **Lock (Critical Section):** Consistent results but limited scalability as the lock creates bottlenecks with increased cores.  
- **Reduction (Hypothetical):** Most scalable, with significant improvements as the number of cores increases.  
- **Atomic Operations:** Expected to strike a balance between speed and consistency for larger setups.  

---

In the end, the choice of synchronization mechanism is context-dependent. Locks work well for smaller core counts, while reduction and atomic operations are better suited for larger, multi-core environments.
