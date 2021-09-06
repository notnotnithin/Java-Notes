# Need for Concurrent Collections
* Traditional collection objects like 
  * ArrayList
  * HashSet
  * HashMap 

and so on can be accessed by multiple threads simultaneously and there are chances of data
inconsistency problem which makes them not thread safe.

* There are already some thread-safe collections in Java.
  * Vector
  * HashTable
  * Collections.synchronizedList()
  * Collections.synchronizedSet()
  * Collections.synchronizedMap()

However, the performance of these above mentioned collections are not upto the mark.
* For any operations, be it a read or a write operation, the total collections will be locked by a thread until it finshes its task and thereby increasing the waiting time of threads and decreasing the performance.
* While one thread is iterating a collection, the other threads are not allowed to modify collection object parallely. Trying to do so the iterator will result in **ConcurrentModificationException** This is one of the biggest problem with the traditional collections. Hence, the traditional collections are not suitable for scalable multi-threaded applications. To overcome these problems, Concurrent Collections were introduced as part of **JDK 1.5**.