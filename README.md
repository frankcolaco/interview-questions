## interview-questions

## Collection Types and Iteration
## What are legacy collections?
Legacy collections are the collection classes and interfaces that were part of Java before the introduction of the Java Collections Framework in Java 1.2. These include classes like Vector, Stack, Hashtable, and the interface Enumeration. They were provided in JDK 1.0.
Key characteristics:
- Introduced in Java 1.0: These classes/interfaces existed before the Collections Framework was added in Java 1.2.
- Thread Safety: Most legacy collection classes (like Vector and Hashtable) are synchronized, meaning they are thread-safe by default. This was done to make them safe for use in multi-threaded environments.
- Performance: Because of synchronization, these classes can be slower compared to newer, unsynchronized collections (like ArrayList or HashMap).
- Integration: With Java 1.2, the Collections Framework was introduced, which provided more flexible, efficient, and unsynchronized alternatives. The legacy classes were retrofitted to implement the new collection interfaces where possible (for example, Vector implements List, and Hashtable implements Map).
Examples of legacy collections:
- Vector
- Stack (which extends Vector)
- Hashtable
- Properties (which extends Hashtable)
- Enumeration (interface for iterating over legacy collections)
- Dictionary (abstract class, parent of Hashtable)
Why are they called “legacy”? They are called legacy because they were part of the original Java API and predate the modern Collections Framework. While still available for backward compatibility, they are generally not recommended for new code.
Summary:
Legacy collections are the original collection classes/interfaces from Java 1.0, such as Vector, Stack, Hashtable, and Enumeration. They are typically synchronized (thread-safe), but less efficient than the newer collection classes introduced in Java 1.2 and later.


## What is the collections framework?
**Answer:**
The Collections Framework in Java is a unified architecture for representing and manipulating collections—groups of objects—such as lists, sets, and maps. Introduced in JDK 1.2, it provides a set of interfaces (like List, Set, Map, Queue) and their implementations (such as ArrayList, HashSet, HashMap, LinkedList, etc.), along with algorithms to operate on them (like sorting and searching).
The framework standardizes how collections are handled, making it easier to write efficient, reusable, and maintainable code. It also includes utility classes (like Collections and Arrays) for common operations. By default, most implementations are not thread-safe, which improves performance for single-threaded scenarios, but thread-safe versions can be created if needed.
**Key points:**
- Provides interfaces, implementations, and algorithms for working with collections.
- Supports different types of collections (lists, sets, maps, queues).
- Promotes code reusability and interoperability.
- Replaces older, less flexible collection classes (like Vector, Hashtable, Stack).
- Not thread-safe by default, but thread-safe versions are available if needed.
Why is it important? It simplifies programming by providing ready-to-use data structures and algorithms, reducing the need to write custom collection classes.

**Reasoning**:
- The framework organizes collections into a hierarchy of interfaces and classes.
- It improves performance and flexibility compared to legacy classes.
- It is a core part of Java programming for handling groups of objects.


## What are wrapped collections?
Wrapped collections in Java refer to collections that are wrapped (or decorated) by special utility methods provided by the Collections class. These methods return a new collection object that adds extra behavior to the original collection. The most common types of wrappers are:
- Synchronized wrappers (e.g., Collections.synchronizedList(list))
- Unmodifiable wrappers (e.g., Collections.unmodifiableList(list))
In the context of your question, we’re focusing on the synchronized (thread-safe) wrappers.

## Does wrapping a collection always make it thread-safe?
**Short answer:**
Wrapping a collection with Collections.synchronizedXXX() makes the collection’s individual method calls thread-safe, but it does not make all compound actions automatically thread-safe.
Why?
- The wrapper synchronizes each method call, so two threads can’t simultaneously execute a method on the collection.
- However, if you perform a sequence of operations (like “check-then-act” or iteration), you must manually synchronize on the collection object to ensure thread safety.
Example of a potential problem:
```java
List<Integer> list = Collections.synchronizedList(new ArrayList<>());

// This is NOT thread-safe:
if (!list.contains(1)) {
    list.add(1);
}
```
Between contains and add, another thread could modify the list.
Correct way for compound actions:
```java
synchronized(list) {
    if (!list.contains(1)) {
        list.add(1);
    }
}
```

**Summary**
- Wrapped collections are collections decorated with extra behavior, like synchronization.
- Synchronized wrappers make individual method calls thread-safe.
- Compound actions (like iteration or check-then-act) require additional external synchronization.
- Wrapping does not guarantee complete thread safety for all usage patterns.

**Common misconception:**
Many assume that using a synchronized wrapper makes all operations thread-safe, but you still need to be careful with compound actions.

## What are concurrent collections?
Concurrent collections in Java are special implementations of collection classes (like lists, maps, sets, and queues) that are designed to be safely used by multiple threads at the same time, without requiring external synchronization (like manually using synchronized blocks).
**Key Points:**
- Thread Safety Without Manual Locking:
- Traditional collections (like ArrayList, HashMap) are not thread-safe.
- Synchronized wrappers (like Collections.synchronizedList) provide thread safety but can be inefficient because they lock the entire collection for every operation.
- Concurrent collections handle synchronization internally and more efficiently, often allowing multiple threads to operate without blocking each other.
- Located in java.util.concurrent Package:
- Examples include: ConcurrentHashMap, CopyOnWriteArrayList, ConcurrentLinkedQueue, ConcurrentSkipListMap, etc.
- How They Achieve Thread Safety:
- Segmented Locking: For example, ConcurrentHashMap divides the map into segments, allowing multiple threads to access different segments simultaneously.
- Non-blocking Algorithms: Some collections use techniques like Compare-And-Swap (CAS) to update data without locking.
- Copy-On-Write: Collections like CopyOnWriteArrayList create a new copy of the underlying array on every write, so reads can happen without locking.
- Advantages:
- Higher throughput and scalability compared to synchronized collections.
- No need for client-side locking.
- Designed for concurrent access patterns.
**Example:**
```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
map.put("apple", 1);
map.put("banana", 2);
```
Multiple threads can safely read and write to this map without additional synchronization.

**Summary:**
Concurrent collections are thread-safe collection classes in Java’s java.util.concurrent package, designed to allow efficient and safe access by multiple threads, using advanced concurrency techniques like segmented locking, CAS, and copy-on-write.

The main mechanisms used by concurrent collections to achieve thread safety include:
- Copy on write: This scheme is designed for read-heavy scenarios. The collection updates state by copying the entire underlying array when a write occurs, ensuring existing readers never face modification issues. Examples include CopyOnWriteArrayList.
- Compare-and-Swap (CAS): This optimistic concurrency model reads a variable state, computes a modification, and updates the value only if the initial variable matches the expected state. Examples include ConcurrentLinkedQueue.
- Locking segments: This approach partitions data into discrete regions that can be locked independently to allow parallel operations. Examples include ConcurrentHashMap.


5. How do we create unmodifiable collections in modern Java?
1. Using Collections.unmodifiableXXX() methods (Java 1.2 and above):
- You create a regular (modifiable) collection first, then wrap it using methods like Collections.unmodifiableList(list), Collections.unmodifiableSet(set), or Collections.unmodifiableMap(map).
- Behavior:
- The returned collection is a view of the original. If the original collection is modified, the changes are reflected in the unmodifiable view.
- Attempting to modify the unmodifiable view (e.g., add, remove) throws UnsupportedOperationException.
- These collections do allow null elements if the underlying collection does.

2. Using Factory Methods (List.of(), Set.of(), Map.of()) (Java 9 and above):
- Java 9 introduced convenient static factory methods to create unmodifiable collections directly:
- List.of(...)
- Set.of(...)
- Map.of(...)
- Example:
```java
List<String> unmodifiableList = List.of("a", "b", "c");
  Set<Integer> unmodifiableSet = Set.of(1, 2, 3);
Map<String, Integer> unmodifiableMap = Map.of("a", 1, "b", 2);
```
- Behavior:
- The collections returned are truly unmodifiable—no underlying modifiable collection exists.
- They do not allow null elements or keys. Attempting to include null will throw a NullPointerException.
- They are more concise and efficient for creating small, fixed collections.

Key Differences:
- Null Handling:
- Collections.unmodifiableXXX() allows null if the underlying collection does.
- List.of(), Set.of(), Map.of() do not allow null elements or keys.
- Underlying Collection:
- Collections.unmodifiableXXX() is a wrapper; changes to the original collection are reflected.
- List.of(), etc., create a new, immutable collection.

**Summary:**:
In modern Java (Java 9+), the preferred way to create unmodifiable collections is using List.of(), Set.of(), and Map.of(). For earlier versions or when you need to wrap an existing collection, use Collections.unmodifiableXXX().


## Core Collection Interfaces in Java
The Java Collections Framework (in java.util) is built around several core interfaces that define the main types of collections. Here’s a breakdown:
1. Collection
- The root interface for most collection types (except maps).
- It defines basic methods like add(), remove(), size(), and iterator().
2. Set
- Extends Collection.
- Represents a collection that does not allow duplicate elements.
- Common implementations: HashSet, LinkedHashSet, TreeSet.
3. List
- Extends Collection.
- Represents an ordered collection (sequence) that can contain duplicates.
- Elements can be accessed by their integer index.
- Common implementations: ArrayList, LinkedList, Vector.
4. Queue
- Extends Collection.
- Designed for holding elements prior to processing, typically in FIFO (first-in, first-out) order.
- Common implementations: LinkedList, PriorityQueue, ArrayDeque.
5. Deque
- Extends Queue.
- Stands for double-ended queue.
- Supports element insertion and removal at both ends (can be used as stack or queue).
- Common implementations: ArrayDeque, LinkedList.
6. Map
- Not a subtype of Collection.
- Represents a collection that maps keys to values; no duplicate keys allowed.
- Common implementations: HashMap, TreeMap, LinkedHashMap.

New Interfaces (Java 21+)
- SequencedCollection, SequencedSet, SequencedMap: These interfaces provide a unified way to work with collections that have a defined encounter order, adding methods like getFirst() and getLast().

Why These Matter
- These interfaces define the contracts for how collections behave in Java.
- Implementations provide concrete data structures with different performance and ordering characteristics.

**Common Misconceptions:**
- Map is not a subtype of Collection.
- Deque is a subtype of Queue, which itself is a subtype of Collection.

## The Difference Between Iterator and Iterable in Java
1. Iterable Interface:
- The Iterable interface represents a collection of objects that can be iterated (looped) over.
- It has a single method: iterator(), which returns an Iterator.
- If a class implements Iterable, it can be used in the enhanced for-each loop (for (Type item : collection)).
- Example: All collection classes like ArrayList, HashSet, etc., implement Iterable.
2. Iterator Interface:
- The Iterator interface provides methods to actually traverse (iterate through) the elements of a collection.
- Its main methods are:
- hasNext(): returns true if there are more elements to iterate.
- next(): returns the next element.
- remove(): removes the last element returned by the iterator (optional operation).
- You obtain an Iterator by calling the iterator() method on an Iterable object.

How They Work Together
- Iterable is like a “promise” that a class can provide an Iterator.
- Iterator is the actual “tool” used to step through the elements.
Example:
```java
List<String> list = new ArrayList<>();
for (String s : list) { // uses Iterable
    // ...
}

Iterator<String> it = list.iterator(); // get Iterator from Iterable
while (it.hasNext()) {
    String s = it.next();
    // ...
}
```

Why This Matters
- If you want your class to be usable in a for-each loop, implement Iterable.
- If you want to define how to step through elements, provide an Iterator.

Common Misconception:
Some people think Iterator and Iterable are interchangeable, but they serve different purposes:
- Iterable = can be iterated (provides an iterator)
- Iterator = does the actual iterating


## How is it possible to get a ConcurrentModificationException from single-threaded code?
**Answer:**
A ConcurrentModificationException can occur in single-threaded code when you modify a collection directly while iterating over it using a fail-fast iterator.
****Reasoning:**:**
- In Java, many collection classes (like ArrayList, HashSet, etc.) provide iterators that are “fail-fast.”
- When you create an iterator (e.g., via iterator() or in a for-each loop), the iterator keeps track of the collection’s modification count (modCount).
- If you modify the collection structurally (e.g., add or remove elements) directly through the collection itself (not through the iterator’s own remove() method) while iterating, the iterator detects that the collection’s modCount has changed unexpectedly.
- When the iterator notices this during its next operation (like next() or hasNext()), it throws a ConcurrentModificationException.
**Example:**
```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");

for (String s : list) {
    if (s.equals("B")) {
        list.remove(s); // Direct modification during iteration
    }
}
```
- In the above code, even though only one thread is running, removing an element from the list directly while iterating over it causes a ConcurrentModificationException.
Why does this happen?
- The exception is not just about multiple threads. It’s about modifying the collection in an unexpected way during iteration.
- The fail-fast behavior is designed to prevent unpredictable results or subtle bugs that could occur if the collection changes while it’s being iterated.
Key Point:
You can get a ConcurrentModificationException in single-threaded code if you modify a collection directly (not via the iterator) while iterating over it.

Question Recap:
If a memory leak causes a LinkedList to contain three billion elements, what value will its size() method return?

Correct Answer:
The size() method will return 2,147,483,647 (which is Integer.MAX_VALUE in Java).

Step-by-Step Reasoning:
- LinkedList Size Storage:
In Java, the LinkedList class keeps track of its size using an int field. The maximum value an int can hold is 2,147,483,647 (Integer.MAX_VALUE).
## What Happens When Exceeding Integer.MAX_VALUE?
If you try to add more elements than Integer.MAX_VALUE, the size counter will overflow and wrap around to negative numbers due to integer overflow.
- API Design:
The size() method is defined to return an int, not a long. This is a limitation of the Java Collections Framework.
- Practical Outcome:
If a LinkedList somehow contains more than Integer.MAX_VALUE elements (for example, three billion), the size() method cannot represent this correctly.
- It will either return Integer.MAX_VALUE (if the implementation caps it there),
- Or, if not capped, it may wrap around and return a negative number.
However, according to the Java SE documentation, the behavior is undefined if the collection exceeds Integer.MAX_VALUE elements. In practice, most implementations will cap the value at Integer.MAX_VALUE.

**Summary:**:
- The size() method will return 2,147,483,647 (Integer.MAX_VALUE), not the true count if the collection exceeds this limit.

Common Misconception:
Some might think the method would return the actual number of elements (e.g., three billion), but that’s not possible with an int return type.

List, Set, and Map Interfaces

## What defines the List interface and its primary implementations?
Answer:
The List interface in Java defines an ordered collection (also known as a sequence) that allows duplicate elements. It provides precise control over where each element is inserted and allows access to elements by their integer index (position in the list). The List interface includes methods for positional access, searching, iteration, and range-view operations.
Key characteristics of the List interface:
- Maintains the order of insertion.
- Allows duplicate elements.
- Supports positional access and insertion (using indices).
- Provides methods like add(int index, E element), get(int index), set(int index, E element), and remove(int index).
Primary Implementations:
- ArrayList
- Backed by a dynamic array.
- Provides fast random access (O(1) time for get and set).
- Slower for insertions and deletions in the middle of the list (O(n) time).
- Most commonly used implementation for general-purpose lists.
- LinkedList
- Backed by a doubly-linked list.
- Faster insertions and deletions at the beginning or end of the list (O(1) time).
- Slower random access (O(n) time for get and set).
- Uses more memory due to storing node pointers.
Why is this correct?
- The List interface is all about ordered, index-based collections that allow duplicates.
- ArrayList and LinkedList are the two main implementations you’ll encounter in interviews and real-world Java programming.
- Each has different performance characteristics: ArrayList is better for random access, while LinkedList is better for frequent insertions/removals at the ends.
Common misconception:
Some learners think LinkedList is always better for insertions, but this is only true at the ends. For insertions in the middle, both can be slow, but LinkedList avoids array resizing.
Modern Java provides factory methods for creating unmodifiable lists. The following example shows how to initialize a list and access its elements using sequenced collection methods.
```java
import java.util.List;

void main() {
    List<String> frameworkList = List.of("Spring", "Quarkus", "Micronaut");
    
    String firstElement = frameworkList.getFirst();
    System.out.println("First framework: " + firstElement);
}
```
- Line 4: We use the List.of() factory method to create an unmodifiable list of strings.
- Line 6: We retrieve the first element using the getFirst() method, which is part of the modern sequenced collections API.
- Line 7: We print the retrieved element to the standard output stream.

2. How do the primary Set implementations differ in performance and ordering?
In Java, the three primary Set implementations are HashSet, LinkedHashSet, and TreeSet. They differ in both performance characteristics and how they handle element ordering:

1. HashSet
- Ordering: Does not guarantee any order of elements. The order can appear random and may change over time.
- Performance: Offers constant-time (O(1)) performance for basic operations like add, remove, and contains, assuming a good hash function.
- Use case: Best when you only care about uniqueness and performance, not about order.

2. LinkedHashSet
- Ordering: Maintains insertion order. Elements are returned in the order they were added.
- Performance: Slightly slower than HashSet due to the overhead of maintaining a linked list, but still offers constant-time (O(1)) performance for basic operations.
- Use case: Use when you need to maintain the order in which elements were inserted.

3. TreeSet
- Ordering: Maintains elements in sorted order according to their natural ordering (as defined by Comparable) or by a provided Comparator.
- Performance: Basic operations like add, remove, and contains take logarithmic time (O(log n)) because it uses a Red-Black tree.
- Use case: Use when you need a sorted set.

**Summary Table:**
| Implementation | Ordering | Performance (add/contains/remove) |
| --- | --- | --- |
| HashSet | No order | O(1) |
| LinkedHashSet | Insertion order | O(1) |
| TreeSet | Sorted order | O(log n) |

Common Misconceptions
- HashSet does not sort or maintain any order.
- LinkedHashSet is not sorted; it only preserves insertion order.
- TreeSet is the only one that sorts elements, but is slower for basic operations.

Interviewers typically ask us to compare three concrete implementations:
- HashSet stores elements using a hash table. It offers the best performance with  time complexity for basic operations like add, remove, and contains. However, it makes no guarantees regarding the iteration order of the elements.
- TreeSet is backed by a Red-Black tree. It implements the NavigableSet interface and maintains its elements in a sorted, ascending order. This strict sorting degrades performance to  for basic operations.
- LinkedHashSet combines a hash table with a linked list running through it. It maintains a predictable iteration order based on when the elements were inserted, operating slightly slower than a standard HashSet due to the linked structure overhead.
We can observe how sets handle duplicate insertions in the following code snippet:
```java
import java.util.HashSet;
import java.util.Set;

public class Demonstration {
    public static void main(String[] args) {
        Set<Integer> uniqueNumbers = new HashSet<>();

        uniqueNumbers.add(10);
        boolean isAddedAgain = uniqueNumbers.add(10);

        System.out.println("Was duplicate added? " + isAddedAgain);
        System.out.println("Set size: " + uniqueNumbers.size());
    }
}
```

3. Why doesn’t the Map interface extend Collection?
Explanation:
The Map interface in Java does not extend the Collection interface because their data models and intended uses are fundamentally different:
- Collection (and its subinterfaces like List and Set) represents a group of individual elements. It provides methods for adding, removing, and iterating over single elements.
- Map, on the other hand, represents a mapping between unique keys and values (key-value pairs). It does not store elements as single objects, but as pairs, and its operations are centered around keys and values rather than just elements.
If Map extended Collection, it would have to support operations like add(E element), which don’t make sense for key-value pairs. The semantics and contracts of Collection methods do not fit the key-value structure of a Map.
**Summary:**:
A Map is not a true collection of elements, but a collection of mappings (associations between keys and values), so it does not extend Collection.

What are the most common implementations of Map?
The three most common implementations are:
- HashMap
- Stores key-value pairs with no guaranteed order.
- Provides fast O(1) access for basic operations (get, put, remove) on average.
- Allows one null key and multiple null values.
- TreeMap
- Stores entries sorted according to the natural ordering of the keys, or by a specified comparator.
- Provides O(log n) time for basic operations.
- Does not allow null keys (but allows null values).
- LinkedHashMap
- Maintains the insertion order of entries (or access order if configured).
- Slightly slower than HashMap, but predictable iteration order.
- Allows one null key and multiple null values.

Reasoning Recap
- Map is not a subtype of Collection because it manages pairs, not single elements.
- The most common implementations are HashMap, TreeMap, and LinkedHashMap, each with different ordering and performance characteristics.
If you had any misconceptions or partial understanding, I hope this clarifies the distinctions and the reasoning behind the design!
We can initialize a modern unmodifiable map and iterate over its entries using the following syntax:
```java
import java.util.Map;

public class Demonstration {
    public static void main(String[] args) {
        Map<String, Integer> systemPorts = Map.of(
            "HTTP", 80,
            "HTTPS", 443,
            "SSH", 22
        );

        systemPorts.forEach((service, port) -> {
            System.out.println(service + " runs on port " + port);
        });
    }
}
```
- Lines 1–13: The Demonstration class contains the main() method, which creates an immutable map of network services and their default port numbers, then prints each key-value pair.
- Lines 5–9: The Map.of() method creates an immutable Map<String, Integer> containing three service-to-port mappings. Once created, the map cannot be modified by adding, removing, or updating entries.
- Lines 11–13: The forEach() method iterates over every key-value pair in the map. The lambda expression receives the service name (service) and its corresponding port number (port) and prints them.
Understanding the contracts of lists, sets, and maps helps us answer technical interview questions and select appropriate collection types for Java applications.

Sequenced Collections
What problem does the Sequenced Collections API solve?
Answer:
Before Java 21, working with ordered collections (like List, Deque, or LinkedHashSet) was inconsistent when you wanted to access or manipulate elements at the beginning or end of the collection. Each collection type had its own way to do this:
- For a List, you’d use get(0) for the first element and get(size() - 1) for the last.
- For a Deque, you’d use getFirst() and getLast().
- For a LinkedHashSet, you’d have to create an iterator and call next() for the first element, and there was no direct way to get the last element.
This inconsistency made code harder to read, maintain, and generalize across different collection types.
The Sequenced Collections API (introduced in Java 21) solves this problem by:
- Introducing new interfaces (SequencedCollection, SequencedSet, SequencedMap) that define a standard set of methods for all collections with a defined encounter order.
- Providing common methods like getFirst(), getLast(), removeFirst(), removeLast(), and addFirst(), addLast() (where appropriate).
- Making it possible to write code that works with any ordered collection in a uniform way, improving readability and reducing the need for collection-specific workarounds.
In summary:
The Sequenced Collections API unifies and standardizes how you access and manipulate the first and last elements of ordered collections in Java, making code more consistent and easier to maintain.

**Reasoning:**:
Your question asked about the problem solved by the Sequenced Collections API. The main issue was the lack of a unified way to work with the ends of ordered collections, leading to inconsistent and sometimes awkward code. The new API addresses this by providing a common interface and methods for all such collections.

Question:
What are the core interfaces introduced in this API?

Answer:
The API introduces three core interfaces in the java.util package to handle ordered elements and entries:
- SequencedCollection
- What it is: This is the root interface for collections that maintain a defined encounter order.
- Extends: Collection
- Key features:
- Provides methods to add, retrieve, and remove elements at both the beginning and end of the collection (e.g., addFirst, addLast, getFirst, getLast, removeFirst, removeLast).
- Offers a method to obtain a reversed view of the collection (reversed()).
- SequencedSet
- What it is: An interface for sets that maintain a defined order.
- Extends: Both SequencedCollection and Set
- Key features:
- Ensures no duplicate elements, like all sets.
- Supports all sequenced operations (adding/removing at both ends, reversed view).
- SequencedMap
- What it is: An interface for maps that maintain a defined order of entries.
- Extends: Map
- Key features:
- Applies the sequenced logic to key-value pairs.
- Provides ordered access to entries, keys, and values.
- Includes methods like firstEntry, lastEntry, pollFirstEntry, pollLastEntry, and reversed().

**Reasoning:**:
- These interfaces were introduced to standardize and unify the way Java collections handle ordering, especially at both ends (head and tail).
- Previously, different collections (like LinkedList, Deque, LinkedHashMap) had their own ways of handling order, but there was no common interface.
- With these interfaces, you can write code that works with any ordered collection or map, not just specific implementations.

**Common Misconceptions:**
- Some might think only SequencedCollection was added, but there are three: SequencedCollection, SequencedSet, and SequencedMap.
- These interfaces are not classes; they define contracts for ordering behavior.

How do we access and manipulate elements using SequencedCollection?
Answer:
The SequencedCollection interface in Java provides a set of intuitive methods to access and manipulate elements at both ends of a collection. These methods include:
- getFirst() – retrieves the first element.
- getLast() – retrieves the last element.
- addFirst(E e) – adds an element to the front.
- addLast(E e) – adds an element to the end.
- removeFirst() – removes and returns the first element.
- removeLast() – removes and returns the last element.
- reversed() – returns a view of the collection in reverse order.
**Reasoning:**:
- The purpose of SequencedCollection is to provide a unified way to work with collections that have a defined order (like lists and deques).
- With these methods, you can easily access or modify elements at the start or end of the collection, which was previously more cumbersome with just the List or Deque interfaces.
- For example, if you have a List<String> list = new ArrayList<>();, since List now implements SequencedCollection, you can call list.getFirst() or list.addLast("hello") directly.
Why this is correct:
- The methods mentioned are part of the SequencedCollection interface, introduced in Java 21.
- These methods are now available on standard list and deque implementations, making element access and manipulation more expressive and consistent.
**Common Misconceptions:**
- Some might think these methods are only for deques, but with SequencedCollection, they are available for lists as well.
- Remember, these methods operate on the logical sequence of the collection, not just physical positions.
We can observe how to use these unified methods on a standard list in the following example:
```java
import java.util.ArrayList;
import java.util.List;

public class Demonstration {
    public static void main(String[] args) {
        List<String> tasks = new ArrayList<>();

        tasks.add("Process Data");

        tasks.addFirst("Initialize");
        tasks.addLast("Cleanup");

        System.out.println("First task: " + tasks.getFirst());
        System.out.println("Reversed view: " + tasks.reversed());
    }
}
```
- Lines 1–14: The Demonstration class contains the main() method, which creates a list of tasks, adds elements at both ends of the list, retrieves the first element, and displays a reversed view of the list.
- Lines 6–10: An ArrayList<String> is created, and elements are added using add(), addFirst(), and addLast(). The addFirst() method inserts an element at the beginning of the list, while addLast() appends an element to the end.
- Line 13: The getFirst() method retrieves the first element of the list without requiring an index, making the code more readable than using get(0).
- Line 14: The reversed() method returns a reverse-order view of the list. It does not create a new list; instead, it provides a view that presents the existing elements in reverse order.

Absolutely! Here’s the complete answer, along with an explanation:

How does a SequencedMap differ from a SequencedCollection?
A SequencedMap is an interface introduced in Java 21 that extends the Map interface and adds sequencing (ordering) capabilities to map entries. In contrast, a SequencedCollection is an interface for collections (like lists or sets) that maintains a defined encounter order for its elements.
Key Differences:
- Type of Elements:
- SequencedCollection deals with individual elements (like in a List or Set).
- SequencedMap deals with key-value pairs (map entries).
- Sequenced Operations:
- SequencedCollection provides methods like getFirst(), getLast(), addFirst(E), addLast(E), removeFirst(), and removeLast() for manipulating elements at the ends of the collection.
- SequencedMap provides similar methods, but for entries: firstEntry(), lastEntry(), pollFirstEntry(), pollLastEntry(). It also provides putFirst(K, V) and putLast(K, V) for maps that support manual repositioning of entries.
- Application:
- Use SequencedCollection when you need an ordered collection of elements.
- Use SequencedMap when you need an ordered mapping of keys to values, and want to perform operations based on the order of entries.

**Summary Table:**:
| Interface | Applies To | Sequenced Methods (Examples) |
| --- | --- | --- |
| SequencedCollection | Elements | getFirst(), getLast(), addFirst(E), … |
| SequencedMap | Map Entries | firstEntry(), lastEntry(), putFirst(), … |
Why is this distinction important?
Because sequencing in a map applies to entries (key-value pairs), not just keys or values individually. The methods in SequencedMap are designed to work with the entire entry, reflecting the order in which entries are stored or manipulated.
We can initialize a sequenced map and retrieve its boundary entries using the following code:
```java
import java.util.LinkedHashMap;
import java.util.SequencedMap;

public class Demonstration {
    public static void main(String[] args) {
        SequencedMap<String, Integer> portConfig = new LinkedHashMap<>();

        portConfig.put("HTTP", 80);
        portConfig.put("HTTPS", 443);
        portConfig.put("SSH", 22);

        System.out.println("First configured port: " + portConfig.firstEntry());
        System.out.println("Last configured port: " + portConfig.lastEntry());
    }
}
```
- Lines 1–2: The program imports the LinkedHashMap and SequencedMap classes from the Java collections framework.
- Lines 4–13: The Demonstration class contains the main() method, which creates a SequencedMap, inserts several key-value pairs, and retrieves the first and last entries.
- Line 6: A LinkedHashMap object is assigned to a SequencedMap reference. As LinkedHashMap preserves insertion order and implements the SequencedMap interface, it supports operations such as firstEntry() and lastEntry().
- Lines 8–10: Three key-value pairs are inserted into the map. The insertion order is preserved as HTTP, HTTPS, and SSH.
- Lines 12–13: The firstEntry() method returns the first key-value pair inserted into the map, while lastEntry() returns the most recently inserted key-value pair.

HashMap Internals

How does a HashMap store data internally?
A HashMap in Java stores data using an array of buckets, where each bucket is essentially a linked list (or, in Java 8 and above, sometimes a balanced tree if there are many collisions).
Step-by-Step Process:
- Hashing the Key:
- When you put a key-value pair into the HashMap, the map first computes the hash code of the key using the key’s hashCode() method.
- Calculating the Bucket Index:
- The hash code is then processed (often with some additional bit manipulation to reduce collisions) and mapped to an index in the internal array.
- The index is calculated using a bitwise AND operation: (n - 1) & hash, where n is the current capacity of the array. This is faster than using the modulo operator and ensures the index is within the array bounds.
- Storing the Entry:
- Each element in the array is called a “bucket.” If the bucket at the computed index is empty, the key-value pair is stored there as a new node.
- If the bucket already contains one or more entries (because different keys produced the same index), the new entry is added to the linked list (or tree) at that bucket.
- Handling Collisions:
- If multiple keys map to the same bucket index (a collision), the HashMap stores all entries in that bucket using a linked list (or a tree if the list gets too long, starting from Java 8).
- Retrieving Values:
- To retrieve a value, the HashMap computes the hash and index in the same way, then traverses the linked list (or tree) at that bucket to find the entry with the matching key.
Key Points:
- Uses an array of buckets (Node[] table).
- Hashes the key to determine the bucket index.
- Handles collisions with linked lists or trees.
- Efficient lookup, insertion, and deletion (on average O(1) time complexity).

Why is this important? Understanding this helps you reason about HashMap’s performance, why good hashCode implementations matter, and how collisions are managed.

Great! Let’s break down each part of how a HashMap stores data internally, step by step, and dive deeper into the details.

1. Hashing the Key
- Purpose: The goal is to convert the key (which can be any object) into an integer value (the hash code).
- How: Java calls the key’s hashCode() method. For example, "apple".hashCode() returns an integer.
- Why: This integer is used to help distribute keys evenly across the array, reducing the chance of collisions.
Question for you:
Why do you think it’s important for different keys to have different hash codes as much as possible?

2. Calculating the Bucket Index
- Purpose: The hash code could be any integer (positive or negative), but the array has a fixed size (say, 16 buckets).
- How: HashMap uses a bitwise AND operation: (n - 1) & hash, where n is the array length. This quickly maps the hash code to a valid array index.
- Example: If the array length is 16, and the hash code is 123456, then the index is 123456 & 15 (which is 0 to 15).
- Why: This is faster than using the modulo operator (hash % n) and works efficiently when the array length is a power of two.
Question for you:
Why do you think HashMap’s internal array size is always a power of two?

3. Storing the Entry
- Purpose: Place the key-value pair in the appropriate bucket.
- How: Each bucket is a reference to a linked list (or, in Java 8+, a tree if there are many entries).
- If Empty: If the bucket is empty, a new node is created and placed there.
- If Not Empty: If the bucket already has nodes, the new node is appended to the list (or inserted into the tree).
Question for you:
What do you think happens if you insert a key that already exists in the map?

4. Handling Collisions
- Purpose: Collisions happen when two different keys end up in the same bucket.
- How: HashMap uses a linked list to store multiple entries in the same bucket. In Java 8+, if a bucket’s list gets too long (more than 8 entries), it’s converted to a balanced tree (like a red-black tree) for faster lookups.
- Why: This prevents performance from degrading if many keys collide.
Question for you:
Why might a poor hashCode() implementation cause performance problems in a HashMap?

5. Retrieving Values
- Purpose: Find the value associated with a given key.
- How: HashMap hashes the key, calculates the bucket index, and then traverses the linked list (or tree) in that bucket to find the node with the matching key (using equals()).
- Why: This allows for fast average-case lookups.

What happens when two keys generate the same array index in a Java HashMap?
When two different keys produce the same array index (also known as a hash collision), the HashMap must handle this situation to ensure that both key-value pairs can coexist.
How does HashMap handle collisions?
- Separate Chaining (Linked List or Tree):
- In Java’s HashMap, each bucket (array index) can store multiple entries.
- When a collision occurs, the HashMap stores the entries at that index in a linked list (or, in Java 8 and above, a balanced tree if the list becomes too long).
- Each node in the list (or tree) contains a key-value pair.
- Insertion:
- If a new key hashes to an index already occupied, the new entry is added to the linked list (or tree) at that bucket.
- Retrieval:
- When retrieving a value, HashMap first computes the index using the key’s hash code.
- It then traverses the linked list (or tree) at that index, comparing each key using the equals() method until it finds the correct one.
Example: Suppose keys “cat” and “dog” both hash to index 5. The bucket at index 5 will contain a linked list (or tree) with both entries. When you look up “cat”, HashMap will check each entry at index 5 and use equals() to find the right key.
**Summary:**:
- Collisions are handled by storing multiple entries in a single bucket using a linked list or tree.
- Retrieval involves searching through these entries to find the correct key.
**Common Misconceptions:**
- Colliding keys do NOT overwrite each other unless they are considered equal by equals().
- The array index is not unique to each key; collisions are expected and handled internally.

How does a HashMap prevent performance degradation during extreme collisions?
Complete Answer
In Java, if many keys in a HashMap end up in the same bucket (due to hash collisions), the default structure for storing these entries is a linked list. Normally, lookup time in a HashMap is O(1), but if a bucket contains many entries (i.e., a long linked list), lookup time degrades to O(n) for that bucket.
To prevent this performance degradation, Java 8 and later versions introduced a mechanism where, if the number of entries in a single bucket exceeds a certain threshold (specifically, 8 entries, known as TREEIFY_THRESHOLD), and the overall map capacity is at least 64, the linked list in that bucket is converted into a balanced Red-Black Tree. This process is called “treeification.”
A Red-Black Tree provides O(log n) lookup time, even in the worst case, which is a significant improvement over the O(n) time of a linked list. This ensures that even if an attacker tries to cause many hash collisions (for example, in a denial-of-service attack), the performance of the HashMap remains acceptable.
In summary:
When a bucket in a HashMap becomes too crowded, Java converts the linked list of entries in that bucket into a Red-Black Tree, improving the worst-case lookup time from O(n) to O(log n) and protecting against performance degradation due to extreme collisions.

Reasoning Process
- Problem: Many keys colliding in the same bucket cause long linked lists, degrading performance.
- Solution: Java detects when a bucket is too crowded (≥8 entries and map capacity ≥64).
- Action: The linked list in that bucket is converted to a Red-Black Tree.
- Result: Lookup time improves from O(n) to O(log n) in that bucket, maintaining good performance even under heavy collisions.

Common Misconceptions
- Some believe resizing the map is the only way to handle collisions, but resizing only spreads entries across more buckets; it doesn’t address the structure within a single bucket.
- The treeification only happens if the map’s capacity is at least 64; otherwise, resizing is preferred.

What is the load factor, and how does it trigger resizing?
Answer:
In Java’s HashMap, the load factor is a measure that determines how full the hash table is allowed to get before its capacity is automatically increased (resized). It is defined as the ratio: load factor = (number of entries) / (number of buckets)
By default, the initial capacity of a HashMap is 16, and the default load factor is 0.75.
How does it trigger resizing?
When the number of entries in the map exceeds the product of the current capacity and the load factor (for example, 16 * 0.75 = 12), the HashMap will resize itself. This resizing process involves:
- Creating a new array of buckets with double the previous capacity.
- Rehashing all existing entries and placing them into the new buckets according to their hash codes.
This process helps maintain efficient lookup and insertion times by minimizing the number of collisions (where multiple keys map to the same bucket).
Why is this important?
- If the load factor is too high, the hash table will have many collisions, which degrades performance.
- If the load factor is too low, the hash table will use more memory than necessary.
**Summary:**:
- The load factor controls when resizing happens.
- When the number of entries exceeds (capacity × load factor), the map resizes (doubles its capacity) and rehashes all entries.

**Common Misconceptions:**
- Some think resizing happens every time a new entry is added, but it only happens when the threshold (capacity × load factor) is exceeded.
- The load factor is not a fixed number of entries; it’s a ratio.

We can observe how the hashCode() and equals() methods control bucket placement and retrieval by implementing a custom key object. Let’s define a class that forces a collision and see how the map handles it.

```java
import java.util.HashMap;
import java.util.Map;
import java.util.Objects;

public class Demonstration {
    public static void main(String[] args) {
        Map<CustomKey, String> dataMap = new HashMap<>();

        CustomKey keyOne = new CustomKey(2, "Alpha");
        CustomKey keyTwo = new CustomKey(4, "Beta");

        dataMap.put(keyOne, "First Value");
        dataMap.put(keyTwo, "Second Value");

        System.out.println("Value for keyOne: " + dataMap.get(keyOne));
        System.out.println("Map size: " + dataMap.size());
    }
}

class CustomKey {
    private final int id;
    private final String name;

    public CustomKey(int id, String name) {
        this.id = id;
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;

        CustomKey that = (CustomKey) o;
        return id == that.id && Objects.equals(name, that.name);
    }

    @Override
    public int hashCode() {
        return id % 2;
    }
}
```
- Lines 1–4: The program imports the classes required for the HashMap, the Map interface, and the Objects utility class.
- Lines 6–17: The Demonstration class creates a HashMap with CustomKey objects as keys, inserts two key-value pairs, retrieves a value using one of the keys, and prints the map size.
- Lines 12–16: When dataMap.get(keyOne) is executed, the HashMap first uses the hash code to locate the appropriate bucket and then uses the equals() method to identify the correct key within that bucket.
- Lines 20–26: The CustomKey class defines two immutable fields, id and name, and initializes them through its constructor. These fields determine object equality.
- Lines 29–35: The equals() method compares two CustomKey objects. Two keys are considered equal only if both their id and name fields are equal.
- Lines 39–40: The hashCode() method returns id % 2, intentionally producing many hash collisions. Both keyOne (id = 2) and keyTwo (id = 4) return the same hash code (0), causing them to be placed in the same hash bucket.
Understanding HashMap internals helps us design keys with consistent hashCode() and equals() behavior, reduce unnecessary collisions, and avoid related performance issues at scale.

Memory Areas
Explore how the JVM organizes its memory into heap, non-heap, and native regions. Understand the causes of common memory errors like StackOverflowError and OutOfMemoryError through practical examples. This lesson helps you grasp key memory concepts essential for modern Java development and interviews.
Before reviewing specific interview questions, we need to understand how the JVM organizes memory. At a high level, the heap stores most objects and arrays created by the application. The JVM also uses non-heap and native memory for class metadata, compiled code, thread stacks, and internal runtime structures.

What kinds of memory does the JVM manage?
Answer:
The Java Virtual Machine (JVM) manages two main types of memory:
- Heap Memory
- This is where all Java objects and their instance variables are stored.
- The heap is managed by the garbage collector, which automatically frees memory that is no longer referenced by the application.
- Most of the memory used by a typical Java application is in the heap.
- Non-Heap Memory
- This includes all other memory areas used by the JVM for its own internal processes.
- Key components of non-heap memory are:
- Metaspace (since Java 8): Stores class metadata (information about classes and methods).
- Method Area (before Java 8): Previously used for class metadata.
- Code Cache: Stores compiled native code generated by the Just-In-Time (JIT) compiler.
- Thread Stacks: Each Java thread has its own stack, which stores method frames, local variables, and partial results.
- Native Memory: Used for JVM internal structures and sometimes for direct memory access (e.g., NIO buffers).
**Reasoning:**:
- The heap is for application-level objects and is managed by the garbage collector.
- The non-heap area is for JVM internals, such as class definitions, compiled code, and thread stacks.
- Both heap and non-heap memory are created and managed by the JVM at startup, and together they make up the JVM’s memory footprint.
**Common Misconceptions:**
- Some people think only the heap matters, but the JVM’s own memory (non-heap) is just as important for performance and stability.
- The term “PermGen” was used before Java 8 for class metadata, but it was replaced by “Metaspace” in Java 8 and later.
**Summary Table:**:
| Memory Type | Purpose | Managed by GC? |
| --- | --- | --- |
| Heap | Java objects, instance variables | Yes |
| Non-Heap | Class metadata, code cache, thread stacks | No |
What are the Java runtime data areas?
According to the Java Virtual Machine (JVM) Specification, when a Java program runs, the JVM creates several runtime data areas. These areas are used to store data and manage execution. They are:
1. Method Area
- Purpose: Stores class-level information such as class structures, method data, field data, and runtime constant pool.
- Lifetime: Created when the JVM starts, shared among all threads.
- Implementation Note: In modern JVMs (Java 8+), this is implemented as “Metaspace” in native memory.
2. Heap
- Purpose: Stores all Java objects and arrays.
- Lifetime: Created at JVM startup, shared among all threads, and exists until the JVM shuts down.
- Garbage Collection: Objects in the heap are managed by the garbage collector.
3. Java Stack (JVM Stack)
- Purpose: Each thread has its own stack, which stores frames. Each frame contains local variables, operand stack, and references to the runtime constant pool.
- Lifetime: Created when a thread is created, destroyed when the thread ends.
- Note: Local variables and method call information are stored here.
4. Program Counter (PC) Register
- Purpose: Each thread has its own PC register, which holds the address of the current instruction being executed.
- Lifetime: Exists per thread, for the duration of the thread.
5. Native Method Stack
- Purpose: Used for native (non-Java) method calls, such as those using JNI (Java Native Interface).
- Lifetime: Each thread has its own native method stack.

**Summary Table:**:
| Data Area | Thread-Shared or Per-Thread | Purpose |
| --- | --- | --- |
| Method Area | Shared | Class metadata, static fields, constants |
| Heap | Shared | All objects and arrays |
| Java Stack | Per-thread | Local variables, method calls |
| PC Register | Per-thread | Current instruction address |
| Native Method Stack | Per-thread | Native method calls |

Why is this important?
Understanding these areas helps you reason about memory management, thread safety, and performance in Java applications.
Let’s take a deep dive into each of the Java runtime data areas. I’ll break down each one, explain its purpose, how it works, and why it matters.
1. Method Area
## What is it?
The Method Area is a memory space shared by all threads. It stores class-level data, such as:
- Class structure (fields, methods)
- Static variables
- Method and field metadata
- Runtime constant pool (literals, references)
## When is it created?
At JVM startup, and it exists as long as the JVM runs.
- Modern implementation:
In Java 8 and later, the Method Area is implemented as “Metaspace,” which uses native memory instead of the old “PermGen” space.
## Why is it important?
It holds the blueprint for all classes loaded by the JVM. If you load too many classes (e.g., in an application server), you can run out of Metaspace.
2. Heap
## What is it?
The Heap is the main area for dynamic memory allocation. All Java objects and arrays are created here.
## Who uses it?
All threads share the heap.
- Garbage Collection:
The JVM automatically reclaims memory in the heap that is no longer referenced (garbage collection).
## Why is it important?
Memory leaks or excessive object creation can cause the heap to fill up, leading to OutOfMemoryError.
3. Java Stack (JVM Stack)
## What is it?
Each thread has its own stack. The stack stores frames, and each frame contains:
- Local variables (including method parameters)
- Operand stack (for intermediate calculations)
- Reference to the runtime constant pool
- Lifespan:
Created when a thread starts, destroyed when the thread ends.
## Why is it important?
Stack size is limited. Deep recursion or large local variables can cause StackOverflowError. Also, primitives and references to objects are stored here, but the actual objects are on the heap.
4. Program Counter (PC) Register
## What is it?
Each thread has its own PC register. It holds the address of the current instruction being executed.
## Why is it needed?
Since Java is multithreaded, each thread needs to know where it is in its execution. The PC register helps the JVM resume execution correctly after thread switches.
5. Native Method Stack
## What is it?
Used for native methods (methods written in languages like C/C++ and called from Java using JNI).
## Who uses it?
Each thread has its own native method stack.
## Why is it important?
If your Java program interacts with native code, this stack is used to manage those calls.

Let’s check your understanding:
## Can you explain why the heap is shared among all threads, but the stack is not?
## What might happen if the method area (Metaspace) runs out of memory?
## Why does each thread need its own PC register?

The Different Parts of the Heap in Java
In Java, the heap is the area of memory used for dynamic memory allocation—where all class instances and arrays are allocated. Modern Java Virtual Machines (JVMs) use a generational garbage collection strategy, so the heap is divided into several logical parts:
1. Young Generation
- Eden Space:
- Most new objects are allocated here.
- It’s the first area where objects are created.
- Survivor Spaces (S0 and S1):
- There are two survivor spaces, often called S0 and S1 (or From and To).
- When a minor garbage collection occurs, objects that survive are copied from Eden to one of the survivor spaces.
- After each minor GC, the survivor spaces swap roles.
2. Old (Tenured) Generation
- Objects that have survived several rounds of minor garbage collection in the young generation are promoted to the old generation.
- This area holds longer-lived objects.
- Garbage collection here (major/full GC) is less frequent but more expensive.
3. (Optional) Permanent Generation / Metaspace
- In older JVMs (before Java 8), there was a “PermGen” (Permanent Generation) for storing class metadata, method information, and interned strings.
- In Java 8 and later, this was replaced by “Metaspace,” which is not part of the heap but serves a similar purpose.

Why is the Heap Divided This Way?
- Generational Hypothesis: Most objects die young (become unreachable soon after allocation).
- By separating young and old objects, the JVM can collect garbage more efficiently:
- Minor GCs (in the young generation) are fast and frequent.
- Major GCs (in the old generation) are slower but less frequent.

Alternative Heap Layouts
- Region-based collectors (like G1, ZGC, Shenandoah):
- Instead of fixed contiguous generations, the heap is divided into many regions.
- G1, for example, still logically separates young and old regions, but the physical layout is more flexible.

**Summary Table:**
| Part | Purpose | Typical Contents |
| --- | --- | --- |
| Eden Space | New object allocation | Newly created objects |
| Survivor Spaces | Hold objects surviving minor GCs | Young objects that survived |
| Old Generation | Long-lived objects | Mature objects |
| PermGen/Metaspace | Class metadata, method info, etc. | Class definitions, metadata |

Common Misconceptions
- PermGen/Metaspace is not for regular objects, only for class metadata.
- The heap is not the same as the stack (the stack is for method frames and local variables).
- In modern JVMs, the 4GB heap limit is no longer relevant due to 64-bit addressing.
Deep dive into all parts of the Java heap
1. Eden Space (Young Generation)
- Purpose:
This is where almost all new objects are allocated.
- Behavior:
- When you create a new object (new keyword), it usually goes into Eden.
- Eden fills up quickly because most objects are short-lived.
- Garbage Collection:
- When Eden is full, a “minor GC” occurs.
- Most objects in Eden are unreachable and are collected.
- Surviving objects are moved to a Survivor space.
Question for you:
Why do you think most objects in Eden are short-lived? What kinds of objects might die quickly?

2. Survivor Spaces (S0 and S1, Young Generation)
- Purpose:
To hold objects that have survived at least one minor GC.
- Behavior:
- There are two survivor spaces: S0 and S1.
- After a minor GC, surviving objects from Eden are copied into one survivor space (say, S0).
- On the next minor GC, survivors are copied from S0 to S1 (and vice versa).
- Each time an object survives a GC, it ages (its “age” counter increases).
- Promotion:
- When an object’s age reaches a certain threshold, it is promoted to the Old Generation.
Question for you:
Why do you think the JVM uses two survivor spaces instead of just one?

3. Old (Tenured) Generation
- Purpose:
To store objects that have survived multiple minor GCs and are likely to live longer.
- Behavior:
- Objects promoted here are considered long-lived.
- This area is larger and is collected less frequently.
- Garbage Collection:
- When the Old Generation fills up, a “major GC” or “full GC” occurs.
- This is more expensive and can cause application pauses.
Question for you:
What might be examples of objects that end up in the Old Generation?

4. Permanent Generation / Metaspace
- Purpose:
To store class metadata, method information, and interned strings.
- PermGen (Java 7 and earlier):
- Fixed size, could cause OutOfMemoryError if full.
- Metaspace (Java 8+):
- Grows dynamically, not part of the heap.
- Stores similar information as PermGen.
Question for you:
Why do you think class metadata is stored separately from regular objects?

5. Region-Based Collectors (G1, ZGC, Shenandoah)
- Purpose:
To improve scalability and reduce pause times.
- Behavior:
- Heap is divided into many regions (not just contiguous generations).
- Regions can be assigned as Eden, Survivor, or Old as needed.
- Allows for more flexible and efficient garbage collection.

PermGen space (short for “Permanent Generation”) was a special area of memory in the Java Virtual Machine (JVM), specifically in the HotSpot implementation before Java 8. Its main purpose was to store metadata about classes, such as:
- Class definitions (bytecode)
- Method and field information
- Static variables
- Interned Strings
- Runtime constant pool
Why was PermGen problematic?
- Fixed size: PermGen had a maximum size set at JVM startup (using flags like -XX:PermSize and -XX:MaxPermSize). If your application loaded many classes (e.g., via frameworks or redeployments), it could run out of PermGen space, causing java.lang.OutOfMemoryError: PermGen space.
- Hard to tune: It was difficult to predict the right size, especially for dynamic applications like web servers.
- GC inefficiency: PermGen was collected less frequently and could lead to memory leaks if classes were not unloaded properly.
What happened to PermGen?
- Removed in Java 8: Starting with Java 8, PermGen was replaced by a new memory area called Metaspace.
- Metaspace improvements: Metaspace stores class metadata in native memory (outside the Java heap) and can grow automatically by default, reducing the risk of running out of space.
**Summary:**:
PermGen was a JVM memory area for class metadata, present before Java 8. It was removed and replaced by Metaspace in Java 8 to solve tuning and memory issues.

**Reasoning:**:
- You needed to know what PermGen was, what it stored, why it was problematic, and what replaced it.
- The answer covers all these points and explains the transition to Metaspace.

What is Metaspace?
Metaspace is a memory area introduced in Java 8 to store class metadata in the Java Virtual Machine (JVM). Before Java 8, this metadata was stored in a memory area called PermGen (Permanent Generation). However, PermGen had several limitations, such as being difficult to size and prone to memory leaks, which could lead to java.lang.OutOfMemoryError: PermGen space.
Key points about Metaspace:
- Purpose:
Metaspace holds class metadata, which includes information about classes, methods, fields, and other reflective data needed by the JVM at runtime.
- Location:
Unlike PermGen, which was part of the Java heap, Metaspace is allocated in native memory (outside the Java heap). This means it uses the host system’s memory directly.
- Sizing:
By default, Metaspace can grow automatically as needed, limited only by the amount of available system memory. You can control its maximum size with the JVM option -XX:MaxMetaspaceSize.
- Benefits over PermGen:
- No more OutOfMemoryError: PermGen space due to fixed-size limitations.
- Easier memory management for class metadata.
- Reduces the need for manual tuning in most cases.
- OutOfMemoryError:
If the Metaspace runs out of native memory (for example, due to excessive class loading or classloader leaks), the JVM will throw java.lang.OutOfMemoryError: Metaspace.
Summary:
Metaspace is the memory space where the JVM stores class metadata since Java 8, replacing PermGen. It resides in native memory, grows automatically by default, and can be tuned with JVM options.
Why is this important?
Understanding Metaspace helps you troubleshoot memory issues related to class loading, especially in applications that dynamically load many classes (like application servers or frameworks that use a lot of reflection or dynamic proxies).

PermGen vs. Metaspace in Java
1. What is PermGen?
- PermGen (Permanent Generation) was a special area of the Java heap prior to Java 8.
- It stored metadata about classes, methods, and other reflective data.
- Its size was fixed at JVM startup (using -XX:PermSize and -XX:MaxPermSize).
- If you loaded too many classes or used a lot of reflection, you could get java.lang.OutOfMemoryError: PermGen space.
2. What is Metaspace?
- Starting with Java 8, PermGen was removed and replaced by Metaspace.
- Metaspace stores the same kind of class metadata, but it is allocated in native memory (outside the Java heap).
- By default, Metaspace can grow automatically as needed, limited only by the available system memory (unless you set -XX:MaxMetaspaceSize).
- If Metaspace runs out of native memory, you get java.lang.OutOfMemoryError: Metaspace.
3. Key Differences:
- Memory Location:
- PermGen: Part of the Java heap.
- Metaspace: Uses native (OS) memory, not the heap.
- Sizing:
- PermGen: Fixed maximum size, set at JVM startup.
- Metaspace: Grows automatically by default, only limited by system memory or a set max.
- Configuration:
- PermGen: Controlled by -XX:PermSize and -XX:MaxPermSize.
- Metaspace: Controlled by -XX:MetaspaceSize and -XX:MaxMetaspaceSize.
- Error Message:
- PermGen: OutOfMemoryError: PermGen space
- Metaspace: OutOfMemoryError: Metaspace
**Summary Table:**:
| Aspect | PermGen (Java 7 and below) | Metaspace (Java 8+) |
| --- | --- | --- |
| Memory Area | Java Heap | Native Memory |
| Sizing | Fixed, must be set | Grows automatically |
| Config Flags | `-XX:PermSize, -XX:MaxPermSize` | `-XX:MetaspaceSize, -XX:MaxMetaspaceSize` |
| OOM Error | PermGen space | Metaspace |
Why the change?
- PermGen was inflexible and often led to memory issues in dynamic class-loading scenarios (like application servers).
- Metaspace is more flexible and reduces the likelihood of running out of space for class metadata.
Common misconception:
Some think Metaspace is just a bigger PermGen, but the key is that it’s outside the heap and managed differently.

What is native (off-heap) memory?
Answer:
Native (or off-heap) memory refers to memory that is allocated outside of the Java heap. While the Java heap is managed by the Java Virtual Machine (JVM) and its garbage collector, native memory is part of the process’s address space but is not managed by the JVM’s garbage collector.
Key points:
## Where is it used?
- The JVM itself uses native memory for internal structures such as:
- Metaspace (where class metadata is stored)
- Thread stacks
- JIT-compiled code cache
- Garbage collector data structures
- Java applications can also allocate native memory directly, for example:
- Using JNI (Java Native Interface) and calling native code (e.g., C/C++ libraries)
- Using NIO (New I/O) direct byte buffers (e.g., ByteBuffer.allocateDirect())
- Using the Foreign Function & Memory API (standardized in Java 22)
## How is it managed?
- Native memory is not managed by the JVM garbage collector.
- If your application allocates native memory, you are responsible for releasing it when it’s no longer needed. Failing to do so can cause memory leaks.
## Why is it important?
- The total memory footprint of a Java process is the sum of heap memory and native (off-heap) memory.
- Monitoring only the Java heap may not give you the full picture of your application’s memory usage.
Summary:
Native (off-heap) memory is memory used by the JVM and Java applications that is outside the Java heap and not managed by the garbage collector. It’s used for JVM internals and can be allocated directly by applications via JNI, NIO direct buffers, or the Foreign Function & Memory API. Proper management is required to avoid memory leaks.

**Reasoning:**:
You asked for the full answer, so I provided a clear definition, examples of usage, management considerations, and why it matters. If you have any follow-up questions or want to explore related topics (like how to monitor native memory usage), feel free to ask!

What is the code cache in HotSpot?
The code cache in HotSpot is a special area of native (non-heap) memory where the Java Virtual Machine (JVM) stores the native machine code generated by the Just-In-Time (JIT) compiler. When Java methods are executed frequently (“hot” methods), the JIT compiler translates their bytecode into optimized native instructions for better performance. These compiled instructions are then stored in the code cache.
Key Points:
- Purpose: The code cache allows the JVM to reuse already-compiled native code for methods, avoiding repeated compilation and improving execution speed.
- Location: It is separate from the Java heap and is managed by the JVM natively.
- Size: The size of the code cache can be configured (e.g., with the -XX:ReservedCodeCacheSize JVM option).
- Behavior when full: If the code cache becomes full, the JVM may stop compiling new methods, which can degrade performance because methods will run in interpreted mode instead of optimized native code.
- Segmentation (Java 9+): Since Java 9, the code cache is divided into segments (non-method, profiled, non-profiled) to improve efficiency and management.
Why is it important?
- Efficient use of the code cache is crucial for JVM performance, especially for long-running applications.
- Monitoring and tuning the code cache can help prevent performance issues related to JIT compilation.
Summary:
The code cache is where HotSpot stores the native code produced by the JIT compiler, enabling fast execution of frequently used Java methods.
1. Purpose of the Code Cache
What does it do?
- The code cache stores native machine code generated by the JIT compiler.
- When a Java method is called many times, the JVM considers it “hot” and compiles it from bytecode (which is interpreted) into native code (which runs directly on the CPU).
- Storing this native code in the code cache means the JVM can quickly execute these methods in the future without recompiling them.
Why is this important?
- Interpreted bytecode is slower than native code.
- By caching compiled code, the JVM avoids repeated compilation and speeds up method execution.

2. Location: Native (Non-Heap) Memory
Where is the code cache?
- The code cache is not part of the Java heap (where objects live).
- It is a separate area of memory managed by the JVM itself, outside of the garbage-collected heap.
Why does this matter?
- It means garbage collection does not affect the code cache.
- The JVM manages its size and contents independently.

3. Size and Configuration
How big is the code cache?
- The size is configurable using JVM options.
- The most common option is -XX:ReservedCodeCacheSize, which sets the maximum size (e.g., -XX:ReservedCodeCacheSize=256m).
Why would you tune this?
- If your application has many hot methods, a larger code cache can prevent it from filling up.
- If the code cache is too small, the JVM may stop compiling new methods, causing performance drops.

4. Behavior When Full
What happens if the code cache fills up?
- The JVM stops compiling new methods (compilation is disabled).
- Existing compiled code remains, but new hot methods will run in interpreted mode, which is slower.
- You might see warnings in the logs like:
CodeCache is full. Compiler has been disabled.
- Performance can degrade, especially for applications that rely on JIT optimizations.

5. Segmentation (Java 9 and Later)
What changed in Java 9?
- The code cache was split into segments:
- Non-method code: For things like JVM stubs and runtime helpers.
- Profiled code: For methods compiled with profiling information (used for further optimization).
- Non-profiled code: For methods compiled without profiling.
- This segmentation helps manage memory more efficiently and allows for better tuning and garbage collection of code cache segments.

6. Monitoring and Tuning
How can you monitor the code cache?
- JVM tools like jstat or JMX can show code cache usage.
- Example:
```bash
jstat -codecache <pid>
```
What should you look for?
- If the code cache is consistently near full, consider increasing its size.
- If you see frequent warnings about the code cache being full, it’s a sign to tune your JVM options.

7. **Summary Table:**
| Aspect | Details |
| --- | --- |
| Purpose | Stores native code from JIT-compiled methods |
| Location | Native memory, outside Java heap |
| Size | Configurable with -XX:ReservedCodeCacheSize |
| Full Behavior | Stops compiling new methods, performance may drop |
| Segmentation | Since Java 9: split into non-method, profiled, and non-profiled segments |
| Monitoring | Use jstat, JMX, or JVM logs |

What is String interning?
Answer:
String interning in Java is a method of storing only one copy of each distinct string value, which must be immutable. The Java String Pool (also called the intern pool) is a special memory region where the JVM stores string literals.
How it works:
- When you create a string literal, like String s = "hello";, Java automatically places “hello” in the String Pool.
- If another string literal with the same value is created, Java will point it to the same object in the pool, rather than creating a new one.
- If you create a new String object at runtime, like String s2 = new String("hello");, it will not automatically use the pool. However, you can call s2.intern(), which will either return the existing pooled instance or add it to the pool if it’s not already there.
Why is this useful?
- It saves memory, since identical strings share the same object.
- It allows for fast equality checks using == because interned strings with the same value will reference the same object.
Technical details:
- The String Pool is implemented as a hash table inside the JVM.
- The size of the pool can be tuned with JVM options (e.g., -XX:StringTableSize).
- Since Java 7, the String Pool is stored in the heap, not in the PermGen space.
- Java 9 introduced compact strings, optimizing storage for Latin-1 characters.
- G1 garbage collector can deduplicate backing arrays for further memory savings.
Example:
```java
String a = "test";
String b = "test";
String c = new String("test");
String d = c.intern();

System.out.println(a == b); // true, both point to the pool
System.out.println(a == c); // false, c is a new object
System.out.println(a == d); // true, d is interned, points to the pool
```
Summary:
String interning ensures that all identical string literals and explicitly interned strings share the same memory reference, optimizing memory usage and enabling fast reference equality checks.

Demonstrating memory limits
To demonstrate these concepts, we can trigger stack exhaustion and heap exhaustion. The following code compares exhausting a thread’s JVM stack with exhausting the shared heap.
Here’s the MemoryLimits.java file demonstrating these two exhaustion scenarios:

```java
class MemoryLimits {
    public static void main(String[] args) {
        try {
            triggerStackOverflow();
        } catch (Error e) {
            System.out.println("Caught: " + e.getClass().getName());
        }

        try {
            triggerOutOfMemory();
        } catch (Error e) {
            System.out.println("Caught: " + e.getClass().getName());
        }
    }

    static void triggerStackOverflow() {
        triggerStackOverflow();
    }

    static void triggerOutOfMemory() {
        Object[] array = new Object[Integer.MAX_VALUE];
    }
}
```
- Lines 3–7: We call triggerStackOverflow(). We catch the Error so the program does not crash immediately, allowing us to proceed to the next test.
- Lines 9–13: We call triggerOutOfMemory() and catch the resulting Error.
- Lines 16–18: We define a method that calls itself infinitely. Each method call adds a new frame to the thread's JVM stack. Eventually, the stack space is exhausted, throwing a StackOverflowError.
- Lines 20–22: We attempt to allocate an array on the heap using the maximum integer size. Because the JVM cannot find enough contiguous space on the heap for this massive object, it throws an OutOfMemoryError.
Pro tip: In modern Java, interviewers may ask if “all objects” are created on the heap. The answer is technically no. Through a process called escape analysis, the JIT compiler can determine if an object never escapes the method it was created in. If so, it can perform scalar replacement, breaking the object down and allocating its primitives directly on the stack to save heap space and reduce garbage collection overhead.
Understanding JVM memory organization helps diagnose memory and performance issues. Distinguishing among heap, non-heap, and native memory also provides context for garbage collection and reference types in the next lessons

## Reference Strengths
Explore how different Java reference strengths—strong, soft, weak, and phantom—impact garbage collection and memory use. Understand how to manage object lifecycles for efficient resource handling and to avoid memory leaks, enabling you to write memory-conscious Java applications.
Java usually manages memory automatically. However, we can influence when objects become eligible for garbage collection by using different reference strengths. Ordinary Java references are strong references by default. Soft references may be cleared in response to memory pressure, weak references may be cleared when an object is only weakly reachable, and phantom references support post-mortem cleanup tracking.
The Four Reference Types in Java
Java provides four types of references, which determine how the garbage collector treats referenced objects. They are, in order of decreasing “strength”:
- Strong Reference
- What it is: This is the default type of reference in Java. Any normal object reference (e.g., String s = new String("hello");) is a strong reference.
- Garbage Collection: As long as a strong reference exists to an object, the object will not be garbage collected.
- Usage: Most objects you create and use in Java are accessed via strong references.
- Soft Reference
- What it is: Created using the SoftReference class in java.lang.ref.
- Garbage Collection: Objects referenced only by soft references are collected only when the JVM absolutely needs memory (i.e., before throwing an OutOfMemoryError).
- Usage: Useful for implementing memory-sensitive caches.
- Weak Reference
- What it is: Created using the WeakReference class in java.lang.ref.
- Garbage Collection: Objects referenced only by weak references are collected as soon as there are no strong or soft references to them.
- Usage: Commonly used in data structures like WeakHashMap, where you want entries to be removed automatically when their keys are no longer in ordinary use.
- Phantom Reference
- What it is: Created using the PhantomReference class in java.lang.ref.
- Garbage Collection: Phantom references do not prevent their referents from being collected. After the object is finalized and ready for collection, the phantom reference is enqueued in a reference queue.
- Usage: Used for more advanced resource cleanup, such as managing off-heap memory or other resources that need to be released after an object is truly gone.
Why This Matters
- Strong references keep objects alive.
- Soft and weak references allow objects to be collected under different conditions, which is useful for memory-sensitive applications.
- Phantom references are for advanced cleanup scenarios.
Common Misconceptions
- All references except strong references are found in the java.lang.ref package.
- Soft and weak references are not the same: soft references are more “persistent” than weak references.
- Phantom references cannot be used to access the referent object (their get() method always returns null).

What is a strong reference?
A strong reference in Java is the default type of reference created when you assign an object to a variable, like this:

```java
Object obj = new Object();
```

As long as there is at least one strong reference to an object, the Java Garbage Collector will never reclaim (delete) that object from memory. In other words, the object is considered “strongly reachable” and will not be garbage collected.
Key Points:
- Most references you use in Java are strong references.
- An object remains in memory as long as it is accessible through a chain of strong references from a “GC root” (like local variables on the stack, static fields, etc.).
- Only when there are no strong references to an object does it become eligible for garbage collection.
Why is this important? Understanding strong references helps you manage memory effectively and avoid memory leaks. If you keep strong references to objects you no longer need, they will not be garbage collected, which can lead to increased memory usage.
Contrast with other reference types: Java also has weak, soft, and phantom references, which allow objects to be collected under different circumstances, but strong references are the default and most common.

What are weak references in Java?
A weak reference in Java is a type of reference object provided by the java.lang.ref package, specifically the WeakReference class. Unlike a strong reference (the default in Java), a weak reference does not prevent its referent (the object it points to) from being reclaimed by the garbage collector.
How does it work?
- If an object is only referenced by weak references (meaning there are no strong or soft references to it), the garbage collector is free to reclaim the object’s memory at the next collection cycle.
- After the object is collected, the weak reference will return null when you call its get() method.
Why are weak references useful?
- They allow you to associate data with objects without preventing those objects from being garbage collected.
- A common use case is in data structures like WeakHashMap, where the keys are held using weak references. If a key is no longer in use elsewhere, it can be garbage collected, and its entry is automatically removed from the map.
Example:
```java
WeakReference<MyObject> weakRef = new WeakReference<>(new MyObject());
// If there are no strong references to MyObject, it can be collected.
```

Summary:
- Weak references allow referenced objects to be garbage collected.
- Useful for memory-sensitive caches or mappings (like WeakHashMap).
- They help avoid memory leaks by not unnecessarily prolonging the life of objects.
Common misconception:
Some think weak references delay garbage collection, but actually, they allow collection as soon as there are no strong or soft references left.
To see how a weak reference behaves when the strong reference is removed, consider the following example.

```java
import java.lang.ref.WeakReference;

class Demonstration {
    public static void main(String args[]) {
        String str = new String("Educative.io"); 
        WeakReference<String> myString = new WeakReference<>(str);
        
        str = null; 
        
        System.gc();

        if (myString.get() != null) {
            System.out.println(myString.get());
        } else {
            System.out.println("String object has been cleared by the Garbage Collector.");
        }
    }
}
```
- Line 5: We create a new String object. This is our strong reference. (Note: We explicitly use new String() here instead of a string literal. String literals are stored in the String Pool and are not easily garbage collected.)
- Line 6: We wrap the string in a WeakReference.
- Line 8: We nullify the strong reference, making the object only weakly reachable.
- Line 10: We suggest that the GC run using System.gc().
- Lines 12–16: We call myString.get() to retrieve the referent. If the GC ran and collected the object, this will return null.

What are soft references?
In Java, a soft reference is a type of reference defined in the java.lang.ref package, specifically with the class SoftReference<T>. Unlike strong references (the default in Java), soft references allow the garbage collector (GC) to reclaim the referenced object only when the JVM is running low on memory.
Key points:
- As long as there is enough memory available, objects referenced only by soft references will not be collected.
- When memory becomes scarce, the GC may clear soft references to free up space, but only before throwing an OutOfMemoryError.
- This makes soft references useful for implementing memory-sensitive caches: cached objects remain available while memory is plentiful, but can be reclaimed if needed.
- Example usage: SoftReference<MyObject> ref = new SoftReference<>(myObject);
Why not always use soft references for caching?
- While soft references seem ideal for caches, their behavior can be unpredictable across different JVM implementations and versions.
- Modern cache libraries (like Caffeine or Guava) often provide better performance and more predictable eviction policies than relying solely on soft references.
Summary:
A soft reference keeps its referent alive until the JVM is under memory pressure, at which point the GC may clear it. This makes them suitable for memory-sensitive caches, as entries are kept as long as memory is available and are reclaimed before an OutOfMemoryError is thrown.

**Reasoning:**:
- Soft references are weaker than strong references but stronger than weak references.
- They are specifically designed to help with memory-sensitive caching.
- The JVM guarantees that all softly reachable objects are cleared before running out of memory.

## Difference between Weak and Soft Reference:
In Java, both weak and soft references are types of references that allow objects to be garbage collected, but they differ in how and when the garbage collector reclaims the referenced objects.
- Weak Reference:
A weak reference does not prevent its referent (the object it points to) from being reclaimed by the garbage collector. As soon as the only references to an object are weak references, the next garbage collection cycle can reclaim that object immediately. Weak references are typically used for things like canonicalizing maps, where you don’t want the reference to extend the object’s lifetime.
- Soft Reference:
A soft reference is a little stronger than a weak reference. The garbage collector will only reclaim softly-referenced objects when the JVM is running low on memory. This means that as long as there is enough memory, the object will be kept alive. Soft references are commonly used for implementing memory-sensitive caches, where you want to keep objects around as long as possible, but allow them to be collected if memory is needed elsewhere.
Key Differences:
- Timing of Collection:
- Weak references: Collected eagerly, as soon as the object is only weakly reachable.
- Soft references: Collected lazily, only when memory is low.
- Use Cases:
- Weak references: Good for canonicalizing maps or registries.
- Soft references: Good for caches that should be cleared under memory pressure.
Why?
- Weak references are for objects you don’t want to keep alive unnecessarily.
- Soft references are for objects you’d like to keep alive if possible, but are willing to let go if memory is tight.
**Common Misconceptions:**
- Some think both are the same, but the difference is in how aggressively the garbage collector reclaims the objects.
- Neither type of reference guarantees immediate collection; it’s always up to the garbage collector, but weak references are much more likely to be cleared quickly.

ReferenceQueue in Java:
A ReferenceQueue is used in conjunction with reference objects (like WeakReference, SoftReference, and PhantomReference) to allow your program to be notified when the garbage collector determines that an object is no longer reachable and is ready to be reclaimed.
How it works:
- When you create a reference object (for example, a WeakReference), you can associate it with a ReferenceQueue.
- When the garbage collector detects that the referent (the object being referenced) is no longer strongly reachable, it will:
- Clear the reference (for WeakReference and SoftReference), or
- Enqueue the reference object onto the associated ReferenceQueue.
- Your program can poll or remove references from the queue. This allows you to perform cleanup actions, such as removing entries from a cache or releasing resources associated with the object.
Why is this useful?
- It gives you a way to know exactly when an object has been collected, so you can take further action (like cleaning up related resources).
- This is especially important for PhantomReference, which is only useful when used with a ReferenceQueue.
Modern Java:
- Since Java 9, the Cleaner API provides a safer and easier way to register cleanup actions for objects, internally using phantom references and reference queues.
Summary: A ReferenceQueue lets your program be notified when the garbage collector is about to reclaim an object referenced by a reference object, enabling you to perform post-GC cleanup.

Why is this the answer?
- It covers the purpose of ReferenceQueue.
- It explains how it works with reference objects.
- It mentions practical use cases.
- It connects to modern Java best practices.

Here’s how we register a cleanup action using the modern Cleaner API.

```java
import java.lang.ref.Cleaner;

class ResourceCleanup implements Runnable {
    public void run() {
        System.out.println("Cleaning up resource.");
    }
}

class Demonstration {
    public static void main(String[] args) {
        Cleaner cleaner = Cleaner.create();
        Object myObject = new Object();
        
        cleaner.register(myObject, new ResourceCleanup());
        
        myObject = null;
        System.gc();
    }
}
```

- Lines 3–7: We define a Runnable that holds our cleanup logic. This class must not hold a strong reference to the object being cleaned up, or the object will never be collected.
- Line 11: We create a Cleaner instance.
- Line 14: We register our target object and the cleanup task. Once myObject becomes phantom reachable, the Cleaner thread will automatically invoke the run() method.

## What are phantom references?
A phantom reference in Java is a type of reference provided by the java.lang.ref.PhantomReference class. It is the weakest among all reference types (strong, soft, weak, and phantom). Phantom references are used to determine exactly when an object has been removed from memory, allowing you to perform cleanup actions after garbage collection.
Key Points:
- Behavior:
- The get() method of a PhantomReference always returns null. This means you cannot retrieve the referent object through the reference.
- When the garbage collector determines that an object is phantom reachable (i.e., it is no longer strongly, softly, or weakly reachable), it will enqueue the phantom reference onto a ReferenceQueue (if provided).
- Purpose:
- Phantom references are mainly used for scheduling post-mortem cleanup actions, such as releasing native resources, after the object has been finalized and is about to be reclaimed by the garbage collector.
- They are safer and more flexible than using the deprecated finalize() method.
- Java 9 Change:
- Since Java 9, the referent of a phantom reference is cleared before the reference is enqueued. This prevents any possibility of resurrecting the object.
- Usage:
- Direct use of PhantomReference is rare. Instead, Java provides the java.lang.ref.Cleaner API, which is built on top of phantom references and is the recommended way to perform cleanup actions.
**Summary Table of Reference Types**:
| Reference Type | Cleared by GC? | get() returns referent? | Enqueued before/after GC? | Use Case |
| --- | --- | --- | --- | --- |
| Strong Reference | No | Yes | N/A | Normal object usage |
| Soft Reference | Sometimes | Yes (if not GC’d) | After GC | Caching, memory-sensitive caches |
| Weak Reference | Yes | Yes (if not GC’d) | After GC | Weak maps, canonicalizing caches |
| Phantom Reference | Yes | Always null | After GC | Post-mortem cleanup |
**Common Misconceptions:**
- You cannot use a phantom reference to access the object after it becomes phantom reachable.
- Phantom references are not for preventing garbage collection, but for being notified after collection.

Why is this correct?
- It covers the definition, behavior, purpose, and changes in recent Java versions.
- It clarifies how phantom references differ from other reference types.
- It addresses common misconceptions and best practices.
Given a custom PhantomReference subclass that keeps a strong field referencing its own referent, what happens when we poll its ReferenceQueue?

Correct Answer
If your custom PhantomReference subclass holds a strong reference to its referent (for example, via a field like private Object strongRef;), then the referent will never become eligible for garbage collection. As a result, the phantom reference will never be enqueued onto the ReferenceQueue.
If you call ReferenceQueue.remove() (which blocks until a reference is enqueued), your program will block indefinitely (or until a timeout, if you use remove(long timeout)). If you call ReferenceQueue.poll(), it will always return null.

Step-by-Step Reasoning
- PhantomReference Basics:
- A PhantomReference is enqueued after the garbage collector determines that the referent is phantom reachable (i.e., no strong, soft, or weak references exist).
- Only then does the JVM enqueue the phantom reference onto its ReferenceQueue.
- Strong Reference in Subclass:
- If your subclass keeps a strong reference to the referent (e.g., this.strongRef = referent;), then as long as the PhantomReference object itself is reachable, so is the referent.
- This means the referent is never eligible for garbage collection.
- Effect on ReferenceQueue:
- Since the referent is never collected, the phantom reference is never enqueued.
- Therefore, ReferenceQueue.remove() will block forever, and ReferenceQueue.poll() will always return null.

Why This Happens
- The purpose of reference objects (like PhantomReference) is to allow the referent to be collected when no strong references exist.
- By holding a strong reference inside the reference object itself, you defeat this purpose: the referent is always reachable as long as the reference object is.
- This is a common pitfall and is why the Java documentation warns against storing strong references to the referent inside reference objects.

Key Takeaway
Never store a strong reference to the referent inside a reference object (like a PhantomReference). Doing so prevents garbage collection and breaks the intended behavior of reference queues.
To visualize the problem described in above question look at this “gotcha” example.

```java
import java.lang.ref.PhantomReference;
import java.lang.ref.ReferenceQueue;

class CustomReference<T> extends PhantomReference<T> {
    T referent; // Anti-pattern: this strong reference defeats the purpose

    public CustomReference(T referent, ReferenceQueue<T> q) {
        super(referent, q);
        this.referent = referent;
    }
}
```

- Line 4: We declare a strong reference field named referent.
- Line 9: We store the passed object in the strong reference field. Because the CustomReference itself is still alive in our application, this strong reference prevents the garbage collector from reclaiming the T object, completely breaking the phantom reference mechanism.
Understanding how to use these reference types allows us to build memory-efficient applications and safely release resources without relying on the unpredictable and deprecated finalization mechanism.


## Garbage Collection

In languages like C or C++, developers must explicitly allocate and free memory. Java takes a different approach. The Java Virtual Machine (JVM) automatically manages memory through a process called garbage collection.
While this relieves us from manual memory management, understanding how the garbage collector operates is essential for tuning application performance, reducing latency, and succeeding in technical interviews.

What is a garbage collector?
A garbage collector (GC) in Java is a part of the Java Virtual Machine (JVM) that automatically manages memory. Its main job is to identify and remove objects from memory (the heap) that are no longer reachable or needed by the application. This process frees up memory space, preventing memory leaks and reducing the need for manual memory management by the programmer.
How does it work?
- The garbage collector tracks all objects in memory.
- It determines which objects are still “reachable” (i.e., can be accessed through references from active threads, static fields, or other reachable objects).
- Objects that are no longer reachable from any “GC root” (such as local variables on the stack, static fields, etc.) are considered “garbage.”
- The GC reclaims the memory used by these unreachable objects, making it available for new objects.
Why is it important?
- It simplifies memory management for developers, reducing bugs related to manual allocation and deallocation.
- It helps prevent memory leaks and other memory-related issues.
Additional details:
- The JVM provides several garbage collection algorithms (such as Serial, Parallel, CMS, and G1), each with different trade-offs in terms of performance and pause times.
- Since Java 9, the default garbage collector is G1.
Common misconception: Some people think the garbage collector immediately removes objects as soon as they become unreachable, but in reality, collection happens periodically according to the GC’s own schedule.

**Summary:**
The garbage collector is an automatic memory management system in Java that frees up memory by removing objects that are no longer in use, so developers don’t have to do it manually.

Garbage Collection Process in Java
Garbage collection (GC) in Java is the process by which the Java Virtual Machine (JVM) automatically identifies and removes objects from memory that are no longer needed by the application, freeing up resources and preventing memory leaks.
1. Heap Structure
The JVM heap is typically divided into several regions:
- Young Generation: Where new objects are allocated. It consists of:
- Eden Space: Most new objects are created here.
- Survivor Spaces (S0 and S1): Objects that survive a garbage collection in Eden are moved here.
- Old (Tenured) Generation: Objects that have survived several garbage collection cycles in the young generation are promoted here.
- (Optional) Permanent Generation / Metaspace: Stores class metadata (in older JVMs).
2. Generational Hypothesis
Java’s garbage collectors are based on the weak generational hypothesis: most objects die young. This means most objects become unreachable quickly after allocation.
3. Garbage Collection Types
- Minor GC: Occurs when the Eden space fills up. The JVM pauses the application (a “stop-the-world” event), identifies live objects in Eden, and moves them to a survivor space. Dead objects are reclaimed.
- Promotion: Objects that survive several minor GCs are promoted to the old generation.
- Major (Full) GC: Occurs when the old generation fills up. This is more expensive, as it scans and collects the entire old generation (and sometimes the young generation too).
4. Modern Collectors
- Parallel GC: Uses multiple threads for minor and major collections.
- G1, ZGC, Shenandoah: These collectors divide the heap into regions and perform much of the collection work concurrently with the application, reducing pause times.
5. Key Points
- Automatic: Developers do not need to manually free memory.
- Stop-the-world: Some phases of GC pause the application.
- Tuning: JVM provides options to tune GC behavior for performance.

**Summary:**
Java’s garbage collector automatically reclaims memory by identifying unreachable objects. It uses a generational approach, with frequent, fast collections in the young generation and less frequent, more expensive collections in the old generation. Modern collectors aim to minimize pause times by doing more work concurrently.

**Common Misconceptions:**
- Developers cannot force garbage collection; System.gc() is only a suggestion.
- Not all objects are immediately collected after becoming unreachable; collection timing is up to the JVM.
Modern Java Garbage Collectors
Java provides several garbage collectors (GCs) that you can choose from, depending on your application’s needs. Here are the main ones available in modern Java (as of Java 21):
1. Serial Garbage Collector
- Flag: -XX:+UseSerialGC
- Description: Uses a single thread for all garbage collection work. It stops all application threads during GC (“stop-the-world”). Best suited for small applications or single-CPU environments.
2. Parallel Garbage Collector (Throughput Collector)
- Flag: -XX:+UseParallelGC
- Description: Uses multiple threads for minor and major GC events. Focuses on maximizing overall throughput. Was the default in Java 8.
3. G1 Garbage Collector
- Flag: -XX:+UseG1GC
- Description: Splits the heap into regions and performs both concurrent and parallel phases. Designed to provide predictable pause times and good throughput. Default since Java 9.
4. Z Garbage Collector (ZGC)
- Flag: -XX:+UseZGC
- Description: A scalable, low-latency collector that performs most work concurrently with the application. Pause times are typically less than 10ms, even with very large heaps. Generational by default since Java 21.
5. Shenandoah Garbage Collector
- Flag: -XX:+UseShenandoahGC
- Description: Another low-pause-time collector, similar to ZGC, developed by Red Hat. Also performs most GC work concurrently.
6. Epsilon Garbage Collector
- Flag: -XX:+UseEpsilonGC
- Description: A “no-op” collector that does not reclaim memory at all. Used for performance testing or debugging memory allocation.

Deprecated/Removed Collectors
- Concurrent Mark Sweep (CMS): Deprecated in Java 9, removed in Java 14. For low-latency needs, use G1, ZGC, or Shenandoah instead.

**Summary Table:**
| Collector | Flag | Key Feature | Use Case |
| --- | --- | --- | --- |
| Serial | `-XX:+UseSerialGC` | Single-threaded, simple | Small heaps, single CPU |
| Parallel | `-XX:+UseParallelGC` | Multi-threaded, throughput | Large heaps, throughput focus |
| G1 | `-XX:+UseG1GC` | Region-based, predictable | Balanced, default since Java 9 |
| ZGC | `-XX:+UseZGC` | Low-latency, scalable | Large heaps, low pause |
| Shenandoah | `-XX:+UseShenandoahGC` | Low-latency, concurrent | Low pause, large heaps |
| Epsilon | `-XX:+UseEpsilonGC` | No-op, no memory reclamation | Testing, benchmarking |

Why This Is Correct
- These are the main collectors available in modern Java (Java 11+ and Java 17/21 LTS).
- Each collector is suited for different scenarios (throughput, latency, heap size).
- Deprecated/removed collectors (like CMS) are noted for historical context.

What are GC roots?
Answer:
In Java, GC roots are special objects that act as starting points for the garbage collector when determining which objects in memory are still reachable (and thus should not be collected). The garbage collector begins its “mark” phase from these roots and traces all objects that can be reached directly or indirectly from them. Any object not reachable from a GC root is considered garbage and eligible for collection.
Typical examples of GC roots include:
- Local variables and parameters in active methods (i.e., references on the stack of live threads).
- Active Java threads themselves.
- Static fields of loaded classes (since these are referenced from the class itself, which is always reachable as long as the class is loaded).
- JNI references (objects referenced from native code outside the JVM).
**Reasoning:**:
- The garbage collector needs a way to determine which objects are still in use. It does this by starting from GC roots and following references.
- Anything that can be reached from a GC root is considered “alive.”
- Anything that cannot be reached from any GC root is considered “dead” and can be safely collected.
**Common Misconceptions:**
- Not all objects in memory are GC roots—only those with a special relationship to the JVM runtime (like stack variables, static fields, etc.).
- GC roots are not themselves garbage collected; they anchor the object graph.
Summary:
GC roots are the foundation of Java’s garbage collection reachability analysis. They are special references from which the JVM starts tracing to find all live objects.
To visualize how an object disconnects from a GC root and becomes eligible for garbage collection, consider the following example:
```java
class GCRootDemonstration {
    public static void main(String[] args) {
        byte[] data = new byte[1024 * 1024];

        System.out.println("Object is strongly reachable from the 'data' root.");

        data = null;

        System.gc();
    }
}
```
- Line 3: We allocate a large byte array on the heap. The local variable data sits on the main thread’s stack. This local variable acts as a GC root, keeping the byte array alive.
- Line 7: We set the local variable to null. The byte array is no longer connected to the GC root. It is now unreachable and eligible for garbage collection.
- Line 9: We request a garbage collection run, which will likely sweep up the unreachable byte array.

## What is the mark-and-sweep algorithm?
The mark-and-sweep algorithm is a classic garbage collection technique used in Java to automatically manage memory.
**Step-by-step explanation**:
- Mark Phase:
- The garbage collector starts from a set of known “root” references (like local variables on the stack, static fields, etc.).
- It traverses the object graph, following references from these roots.
- Every object that can be reached is “marked” as alive.
- Sweep Phase:
- After marking, the collector scans through all objects in the heap.
- Any object that was not marked in the previous phase is considered unreachable (i.e., garbage).
- These unmarked objects are then reclaimed, freeing up memory.
Key Points:
- The algorithm works in two distinct phases: marking and sweeping.
- It can cause “stop-the-world” pauses, where application threads are paused during collection.
- It does not compact memory by default, so over time, memory fragmentation can occur.
- Many modern Java collectors build upon or improve this basic approach (e.g., by adding compaction or concurrent marking).
**Common Misconceptions:**
- Mark-and-sweep does not immediately reclaim memory as soon as an object becomes unreachable; it waits until the next collection cycle.
- It does not move objects in memory (unless combined with a compaction phase).
Why is this important in Java?
- Java developers do not need to manually free memory; the garbage collector (using algorithms like mark-and-sweep) handles it automatically.
- Understanding this process helps explain why memory leaks can still occur (e.g., if objects are still referenced) and why pauses might happen during program execution.

Can we force the garbage collector to run?
Answer: No, we cannot force the Java garbage collector to run. In Java, you can request garbage collection by calling System.gc() or Runtime.getRuntime().gc(). However, these methods only suggest to the JVM that it might be a good time to run the garbage collector—they do not guarantee that garbage collection will actually happen immediately, or at all.
**Reasoning:**:
- The Java Virtual Machine (JVM) manages memory and garbage collection internally.
- When you call System.gc(), it acts as a hint to the JVM, not a command.
- The JVM is free to ignore this request. In fact, some JVMs or configurations (like using the -XX:+DisableExplicitGC flag) will completely ignore explicit GC requests.
- The timing and execution of garbage collection are determined by the JVM’s garbage collector algorithms, which are optimized for overall performance, not for immediate memory reclamation.
Why is this the case?
- Forcing garbage collection could negatively impact performance, so the JVM designers chose to keep control over when GC actually happens.
- Relying on explicit garbage collection is considered a bad practice (“anti-pattern”) in Java programming.
Common misconception: Some developers believe that calling System.gc() will immediately free up memory, but this is not guaranteed.
Summary: You can request, but not force, garbage collection in Java. The JVM decides when (or if) to actually perform garbage collection.

What are minor GC, major GC, and full GC?
1. Minor GC
- What it is: A minor GC (Garbage Collection) refers to the process where the JVM collects and removes objects from the young generation of the heap.
- Young Generation: This area is divided into Eden space and two Survivor spaces (S0 and S1).
- When it happens: When the Eden space fills up, a minor GC is triggered to reclaim memory by removing unreachable objects from the young generation.
- Performance: Minor GCs are usually fast and happen frequently.
2. Major GC
- What it is: A major GC generally refers to a garbage collection event that involves the old (tenured) generation of the heap.
- Old Generation: This is where long-lived objects are stored after surviving several minor GCs.
- When it happens: When the old generation fills up, a major GC is triggered to reclaim space.
- Performance: Major GCs are more expensive than minor GCs because they process more data and can cause longer application pauses.
3. Full GC
- What it is: A full GC is a garbage collection event that collects the entire heap, including both the young and old generations, and sometimes the Metaspace (where class metadata is stored in Java 8+).
- When it happens: Full GCs are triggered when the JVM needs to reclaim as much memory as possible, often when both young and old generations are full, or when certain system calls (like System.gc()) are made.
- Performance: Full GCs are the most expensive and can cause significant application pauses.

Additional Notes
- Terminology: These terms (minor, major, full GC) are not strictly defined in the JVM specification. Their exact meaning can vary between different garbage collectors and JVM implementations.
- Interview Focus: In interviews, it’s important to know that:
- Minor GC = young generation only (fast, frequent)
- Major GC = old generation (slower, less frequent)
- Full GC = entire heap (slowest, should be minimized)

Common Misconceptions
- Sometimes, “major GC” and “full GC” are used interchangeably, but technically, a full GC always includes the entire heap, while a major GC may only include the old generation.
- Minor GCs do not collect objects from the old generation.
Understanding garbage collection concepts and collector behavior helps us evaluate JVM memory settings and collector options. In the next lesson, we will examine memory-tuning techniques and methods for detecting memory leaks.
Clients et prospects d'American Express: Pour plus d'informations sur la façon dont nous protégeons votre vie privée, veuillez visiter www.americanexpress.com/privacy. Si vous êtes situé à l'extérieur des États-Unis, veuillez sélectionner votre emplacement à l'adresse www.americanexpress.com/change-country/ et accéder au lien de confidentialité en bas de la page.
