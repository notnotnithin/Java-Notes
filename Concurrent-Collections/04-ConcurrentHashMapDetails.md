# ConcurrentHashMap details
* Underlying data structure is a HashTable.
* ConcurrentHashMap allows concurrent (any number of threads) read but a thread safe for update operations.
* To perform read operation, the thread doesn't need any lock. But to perform update operations, the thread requires a lock but it is just a Bucket Level Lock - a lock on the part of Map and not as a whole.
ConcurrentHashMap by default has 16 initial capacity and each of the 16 parts is treated as a bucket and for every write operation only the required buckets are locked unlike in a HashTable where a lock is present on the total HashTable. Other name of Bucket Level Locking is Segment Locking.
* **Bucket Level Locking/Segment Locking** - The total map will be divided into 16 parts by default. This number is nothing but the concurrency level. The ConcurrentHashMap will be divided into 16 buckets (initial capacity). Most of the times the number of buckets and concurrency level are the same.
* The default concurrency level is 16. That means ConcurrentHashMap allows 16 read operations and 16 write (update) operations simultaneously.
* A `null` is not allowed for both key and values.
* Using concurrent collections, it is possible to modify the collections while the other thread is interacting with the same collection. Hence concurrent collections will never throw ConcurrentModificationException.

# Constructors
* ```ConcurrentHashMap m = new ConcurrentHashMap();```
  * Creates an empty ConcurrentHashMap with a default initial capacity of 16, with a default fill ratio 0.75, and a default concurrency level of 16.
* ```ConcurrentHashMap m = new ConcurrentHashMap(int initialCapacity)```
* ```ConcurrentHashMap m = new ConcurrentHashMap(int initialCapacity, float fillRatio)```
* ```ConcurrentHashMap m = new ConcurrentHashMap(int initialCapacity, float fillRatio, int concurrencyLevel)```
* ```ConcurrentHashMap m = new ConcurrentHashMap(Map m)```

# Difference between a HashMap and ConcurrenctHashMap
| HashMap | ConcurrenctHashMap |
|---------|--------------------|
| It is not thread safe. | It is thread safe. |
| Relatively the performance is high because threads are not required to wait to operate on a HashMap. | Relatively the performance is low because sometimes threads are required to wait to operate on a ConcurrentHashMap. |
| Using a HashMap, it is not possible to modify the collections while the other thread is interacting with the same collection. We will be thrown with a ConcurrentModificationException. | Using concurrent collections, it is possible to modify the collections while the other thread is interacting with the same collection. Hence concurrent collections will never throw ConcurrentModificationException. |
| Iterator of HashMap is Fail-Fast and it throws a ConcurrentModificationException. | Iterator of a ConcurrentHashMap is Fail-Safe and it won't throw a ConcurrentModificationException. |
| A `null` is allowed for both keys and values | A `null` is not allowed for both key and values. |
| Introduced in JDK 1.2 | Introduced in JDK 1.5 |
