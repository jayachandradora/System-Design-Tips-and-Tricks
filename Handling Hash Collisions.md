# Handling Hash Collisions in Hash Table Implementations

Hash collisions occur when multiple keys hash to the same index in a hash table. Efficiently managing these collisions is essential for maintaining the performance and reliability of hash table implementations. Here are two common approaches:

## Chaining

Chaining involves using linked lists (or other dynamic structures) to handle collisions:

- **Data Structure**: Each bucket in the hash table contains a linked list (or array) of entries that hash to the same index.
- **Collision Resolution**: New key-value pairs are appended to the linked list or added to the array at the hashed index.
- **Traversal**: To find a specific key, traverse the linked list or array starting from the hashed index until the matching key is found or the end of the list/array is reached.

### Pros:
- Simple implementation.
- Handles any number of collisions effectively.
- Dynamic structure adjusts well to varying numbers of collisions.

### Cons:
- Increased memory usage due to storing additional pointers or array entries.
- Retrieval time can increase with larger linked lists or arrays.

## Probing (Open Addressing)

Probing involves finding alternative positions within the hash table when collisions occur:

- **Collision Resolution**: Calculate alternative hash positions using a probing sequence (e.g., linear probing, quadratic probing, double hashing).
- **Probing Sequences**:
  - **Linear Probing**: Check consecutive slots until an empty slot is found.
  - **Quadratic Probing**: Increase step size quadratically to find the next available slot.
  - **Double Hashing**: Use a second hash function to calculate the step size.

### Pros:
- Saves memory as no additional data structures are needed for collisions.
- Can be faster for small tables or memory-constrained environments.

### Cons:
- Clustering may occur, leading to performance degradation.
- Requires careful tuning of probing sequences for optimal performance.

## Choosing Between Chaining and Probing

- **Usage Scenario**: Chaining is suitable when memory is not a constraint and efficient handling of a large number of collisions is necessary. Probing is preferred in memory-constrained environments or when quick access and minimal memory overhead are priorities.
- **Implementation Complexity**: Chaining is easier to implement with dynamic structures like linked lists. Probing requires careful handling of deletion and selection of efficient probing sequences.
- **Performance Considerations**: Performance varies based on factors such as load factor, collision rate, and hash function efficiency.

In conclusion, both chaining and probing are effective strategies for managing hash collisions in hash table implementations, each offering distinct advantages and considerations based on specific use cases and performance requirements.

### Chaining Implementation in Java

Chaining involves using linked lists or arrays to manage collisions. Here’s how you can implement a hash table with chaining in Java:

```java
import java.util.LinkedList;

// Node class for linked list in each bucket
class Node<K, V> {
    K key;
    V value;
    Node<K, V> next;

    public Node(K key, V value) {
        this.key = key;
        this.value = value;
        this.next = null;
    }
}

// Hash table implementation with chaining
class HashTableChaining<K, V> {
    private LinkedList<Node<K, V>>[] buckets;
    private int capacity;
    private int size;

    public HashTableChaining(int capacity) {
        this.capacity = capacity;
        this.buckets = new LinkedList[capacity];
        this.size = 0;
    }

    // Hash function to get index
    private int hashFunction(K key) {
        return Math.abs(key.hashCode() % capacity);
    }

    // Put key-value pair into hash table
    public void put(K key, V value) {
        int index = hashFunction(key);
        if (buckets[index] == null) {
            buckets[index] = new LinkedList<>();
        }
        LinkedList<Node<K, V>> bucket = buckets[index];
        for (Node<K, V> node : bucket) {
            if (node.key.equals(key)) {
                node.value = value;
                return;
            }
        }
        bucket.add(new Node<>(key, value));
        size++;
    }

    // Get value associated with key from hash table
    public V get(K key) {
        int index = hashFunction(key);
        LinkedList<Node<K, V>> bucket = buckets[index];
        if (bucket != null) {
            for (Node<K, V> node : bucket) {
                if (node.key.equals(key)) {
                    return node.value;
                }
            }
        }
        return null;
    }

    // Remove key-value pair from hash table
    public void remove(K key) {
        int index = hashFunction(key);
        LinkedList<Node<K, V>> bucket = buckets[index];
        if (bucket != null) {
            for (Node<K, V> node : bucket) {
                if (node.key.equals(key)) {
                    bucket.remove(node);
                    size--;
                    return;
                }
            }
        }
    }

    // Check if hash table is empty
    public boolean isEmpty() {
        return size == 0;
    }

    // Get size of hash table
    public int size() {
        return size;
    }
}

// Example usage
public class Main {
    public static void main(String[] args) {
        HashTableChaining<String, Integer> hashTable = new HashTableChaining<>(10);
        hashTable.put("John", 30);
        hashTable.put("Jane", 25);
        hashTable.put("Doe", 40);

        System.out.println("Age of John: " + hashTable.get("John")); // Output: 30

        hashTable.remove("Jane");
        System.out.println("Size of hash table: " + hashTable.size()); // Output: 2
    }
}

```

### Probing (Open Addressing) Implementation in Java

Probing involves finding an alternative slot within the hash table when a collision occurs. Here's an example implementation using linear probing in Java:

```java
// Hash table implementation with linear probing
class HashTableLinearProbing<K, V> {
    private K[] keys;
    private V[] values;
    private int capacity;
    private int size;

    public HashTableLinearProbing(int capacity) {
        this.capacity = capacity;
        this.keys = (K[]) new Object[capacity];
        this.values = (V[]) new Object[capacity];
        this.size = 0;
    }

    // Hash function to get index
    private int hashFunction(K key) {
        return Math.abs(key.hashCode() % capacity);
    }

    // Put key-value pair into hash table
    public void put(K key, V value) {
        if (size >= capacity) {
            throw new RuntimeException("Hash table is full");
        }
        int index = hashFunction(key);
        while (keys[index] != null) {
            if (keys[index].equals(key)) {
                values[index] = value; // Update existing value
                return;
            }
            index = (index + 1) % capacity; // Linear probing
        }
        keys[index] = key;
        values[index] = value;
        size++;
    }

    // Get value associated with key from hash table
    public V get(K key) {
        int index = hashFunction(key);
        while (keys[index] != null) {
            if (keys[index].equals(key)) {
                return values[index];
            }
            index = (index + 1) % capacity; // Linear probing
        }
        return null;
    }

    // Remove key-value pair from hash table
    public void remove(K key) {
        int index = hashFunction(key);
        while (keys[index] != null) {
            if (keys[index].equals(key)) {
                keys[index] = null;
                values[index] = null;
                size--;
                return;
            }
            index = (index + 1) % capacity; // Linear probing
        }
    }

    // Check if hash table is empty
    public boolean isEmpty() {
        return size == 0;
    }

    // Get size of hash table
    public int size() {
        return size;
    }
}

// Example usage
public class Main {
    public static void main(String[] args) {
        HashTableLinearProbing<String, Integer> hashTable = new HashTableLinearProbing<>(10);
        hashTable.put("John", 30);
        hashTable.put("Jane", 25);
        hashTable.put("Doe", 40);

        System.out.println("Age of John: " + hashTable.get("John")); // Output: 30

        hashTable.remove("Jane");
        System.out.println("Size of hash table: " + hashTable.size()); // Output: 2
    }
}

```

### Explanation

**Chaining:** Uses a hash table where each bucket stores a linked list of key-value pairs. Collisions are handled by appending entries to the linked list at the hashed index.

**Probing (Linear Probing):** Implements a hash table where collisions are resolved by probing linearly through the table to find the next available slot.

These implementations illustrate how Java can be used to create hash tables with chaining and probing strategies for handling hash collisions effectively. Adjustments and optimizations can be made based on specific use cases and requirements.
