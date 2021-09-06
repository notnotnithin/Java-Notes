ConcurrentMap (I) - All concurrent collections are present inside `java.util.concurrent` package.

Map (I) <- ConcurrentMap (I) <- ConcurrentHashMap (C)

ConcurrentMap defines 3 methods:
* ```Object putIfAbsent(Object key, Object value)```

To add entry to a map if the specified key is not available already.

```
Object putIfAbsent(Object key, Object value) {
  if (!map.containsKey(key)) {
    map.put(key, value);
  } else {
    return map.get(key);
  }
}
```

| put()                         | putIfAbsent()          |
|-------------------------------|------------------------|
| If the key is already available, old value will be replaced with the new value and the old value is returned.      | If the key is already available, then entry won't be added instead it would return the old associated value. If the key is not available then the entry would be added. |

* ```boolean remove(Object key, Object value)```

Removes the entry only if the key is associated with the specified value.

```
if (map.containsKey(key) && map.get(key).equals(value)) {
  map.remove(key);
  return true;
} else {
  return false;
}
```

* ```boolean replace(Object key, Object oldValue, Object newValue)```

Replaces the old value with the new value only if the key values matches.

```
if (map.containsKey(key) && map.get(key).equals(oldValue)) {
  map.replace(key, oldValue, newValue);
  return true;
} else {
  return false;
}
```

