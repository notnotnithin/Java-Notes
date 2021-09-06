# Difference between Traditional and Concurrent Collections
| Traditional Collections | Concurrent Collections |
|-------------------------|------------------------|
| Traditional collections are not thread-safe. | Concurrent collections are always thread-safe. |
| The complete collection will be locked for any type of operation (read/write)| When compared with traditional thread-safe collections, the performance of concurrent collections is high because of different locking mechanisms (segment lock/bucket lock). |
| By mistake if we try to manipulate an underlying collection, we will be thrown with ConcurrentModificationException | Using concurrent collections, it is possible to modify the collections while the other thread is interacting with the same collection. Hence concurrent collections will never throw ConcurrentModificationException. |
| Not suitable for multi-threaded applications. | Suitable for scalable multi-threaded applications. |

Important concurrent collection classes are: 
  * ConcurrentHashMap
  * CopyOnWriteArrayList
  * CopyOnWriteArraySet