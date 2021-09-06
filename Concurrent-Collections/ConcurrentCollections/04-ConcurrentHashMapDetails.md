# ConcurrentHashMap details
* Underlying data structure is HashTable.
* ConcurrentHashMap allows concurrent read and thread safe update operations.
* To perform read operation, the thread doesn't need any lock. But to perform update operations, the thread requires a lock but it is just a Bucket Level Lock - a lock on the part of Map and not as a whole.
* Instead of locking the whole map, the concurrent update is achieved by internally dividing map into smaller portion which is defined by the concurrency level.
* The default concurrency level is 16. That means ConcurrentHashMap allows 16 read operations and 16 write (update) operations simultaneously.
* The `null` is not allowed for both key and values.
* Using concurrent collections, it is possible to modify the collections while the other thread is interacting with the same collection. Hence concurrent collections will never throw ConcurrentModificationException.

# Constructors
* ```ConcurrentHashMap m = new ConcurrentHashMap();```
  * Creates an empty ConcurrentHashMap with a default initial capacity of 16, with a default fill ratio 0.75, and a default concurrency level of 16.
* ```ConcurrentHashMap m = new ConcurrentHashMap(int initialCapacity)```
* ```ConcurrentHashMap m = new ConcurrentHashMap(int initialCapacity, float fillRatio)```
* ```ConcurrentHashMap m = new ConcurrentHashMap(int initialCapacity, float fillRatio, int concurrencyLevel)```
* ```ConcurrentHashMap m = new ConcurrentHashMap(Map m)```