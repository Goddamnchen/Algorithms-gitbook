# Queue Application

This application is derived from a assignment of Algorithms - Princeton on Coursera.   
Assignment specification refers to: [Deques and Randomized Queues](http://coursera.cs.princeton.edu/algs4/assignments/queues.html)

## Deques and Randomized Queues

This application aims to write a generic data type for a deque and a randomized queue using arrays and linked lists, and introduce to generics and iterator.

## Model Design

> **Deque:** A _double-ended queue_ or _deque_ \(pronounced “deck”\) is a generalization of a stack and a queue that supports adding and removing items from either the front or the back of the data structure. Create a generic data type `Deque` that implements the following API:

{% code-tabs %}
{% code-tabs-item title="Deque java API" %}
```java
public class Deque<Item> implements Iterable<Item> {
   public Deque()                           // construct an empty deque
   public boolean isEmpty()                 // is the deque empty?
   public int size()                        // return the number of items on the deque
   public void addFirst(Item item)          // add the item to the front
   public void addLast(Item item)           // add the item to the end
   public Item removeFirst()                // remove and return the item from the front
   public Item removeLast()                 // remove and return the item from the end
   public Iterator<Item> iterator()         // return an iterator over items in order from front to end
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

> **Randomized queue:** A _randomized queue_ is similar to a stack or queue, except that the item removed is chosen uniformly at random from items in the data structure. Create a generic data type `RandomizedQueue` that implements the following API:

```java
public class RandomizedQueue<Item> implements Iterable<Item> {
   public RandomizedQueue()                 // construct an empty randomized queue
   public boolean isEmpty()                 // is the randomized queue empty?
   public int size()                        // return the number of items on the randomized queue
   public void enqueue(Item item)           // add the item
   public Item dequeue()                    // remove and return a random item
   public Item sample()                     // return a random item (but do not remove it)
   public Iterator<Item> iterator()         // return an independent iterator over items in random order
}

```

> _Iterator_.  Each iterator must return the items in uniformly random order. The order of two or more iterators to the same randomized queue must be _mutually independent_; each iterator must maintain its own random order.

Before designing these data structures, we firstly come out with serval helping questions:

### **How to maintain each iterator will return a queue with uniformly random order ?** 

The thing is "each" iterator. Each iterator is built mutually independent and could have their own instance variable. Therefore, it is possible that we initialize a instance variable of newly shuffled queue for each iterator object when constructing them by using `StdRandom.shuffle()`—it implements the Knuth shuffle and runs in linear time. 

### How to remove items uniformly at random from current queue.

There is a efficient approach to uniformly and randomly remove a item from **array-based** queue if we dont care about the order of queue. To achieve this, we could simply choice a random index and exchange the item with the item waiting to be dequeued soon, and then set the item on the enqueue-side to NULL. If we do not care the order of items in previous queue, this would perfectly work.

However, uniformly and randomly removing a item from **LinkedList-based** queue can hardly be efficient for the reason that we have no direct access to the item we want to remove.

### Choose Array or LinkedList based?

Choice about which medium to use to implement deque or randomized queue is subjected to the performance requirement which will discuss in [next section](queue-application.md#algorithm-analysis). We will select array-based or linkedlist based implement according to time and space complexity requirement.

## Algorithm Analysis

![Performance requirement.](../.gitbook/assets/image%20%2856%29.png)

Before design deque and randomized queue, we must analyze this performance requirement to make a choice between using linkedlist-based or array-based implementation.

### Deque

The non-iterator operations that take constant worst-case time for deque directly offer us the best choice, which is to use deque based on **doubly linked list** . The doubly linkedlist-based deque could  manipulate on both side and costs constant worst-case time for all operations, and also satisfies the other requirements to implement a deque API.

#### Code implementation

{% code-tabs %}
{% code-tabs-item title="Deque implementation based on doubly linked list" %}
```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Deque<Item> implements Iterable<Item> {
    private Node sentinel;
    private int size;

    private class Node {
        private Node prev;
        private Item item;
        private Node next;

        public Node() {
            this.item = null;
            this.item = null;
            this.next = null;
        }
        public Node(Item item, Node prev, Node next) {
            this.item = item;
            this.prev = prev;
            this.next = next;
        }
    }
    
    public Deque() {
        this.sentinel = new Node();
        this.sentinel.prev = this.sentinel;
        this.sentinel.next = this.sentinel;
        this.size = 0;
    }
    
    public void addFirst(Item item) {
        if (item == null) throw new IllegalArgumentException("can not add null");
        this.sentinel.next = new Node(item, this.sentinel, this.sentinel.next);
        this.sentinel.next.next.prev = this.sentinel.next;
        this.size += 1;
    }
    
    public void addLast(Item item) {
        if (item == null) throw new IllegalArgumentException("can not add null");
        this.sentinel.prev = new Node(item, this.sentinel.prev, this.sentinel);
        this.sentinel.prev.prev.next = this.sentinel.prev;
        this.size += 1;
    }
    
    public Item removeFirst() {
        if (isEmpty()) throw new NoSuchElementException("The Deque is empty, can not remove!");
        Node prevFirst = this.sentinel.next;
        this.sentinel.next = this.sentinel.next.next;
        this.sentinel.next.prev = this.sentinel;
        this.size -= 1;
        return prevFirst.item;
    }
    
    public Item removeLast() {
        if (isEmpty()) throw new NoSuchElementException("The Deque is empty, can not remove!");
        Node prevLast = this.sentinel.prev;
        this.sentinel.prev = this.sentinel.prev.prev;
        this.sentinel.prev.next = this.sentinel;
        this.size -= 1;
        return prevLast.item;
    }
    
    public boolean isEmpty()          { return this.size == 0; }
    public int size()                 { return size; }
    public Iterator<Item> iterator()  { return new DequeIterator(); }
    
    private class DequeIterator implements Iterator<Item> {
        private Node p = sentinel;
        private int index = 0;

        /**
         * @return true when index belongs to Deque
         * @return false when index out of bound
         */
        @Override
        public boolean hasNext() {
            if (index < size() ) return true;
            else return false;
        }
        @Override
        public Item next() {
            if (size() == 0 || !hasNext()) throw new NoSuchElementException("DequeIterator has no more items to iterate");
            Item item = p.next.item;
            p = p.next;
            index += 1;
            return item;
        }
        @Override
        public void remove(){
            throw new UnsupportedOperationException("DequeIterator does not support remove() method");
        }
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Randomized Queue

"_The constant amortized time"_  for non-iterator operations reminds __us that it is basically a **array-based** case. Plus, considering the "_linear in current \# of items"_ on both "_Memory per iterator"_ and _"Iterator constructor",_ we are likely to build a extra array for each iterator for the aim of shuffling, leading to a queue with uniformly random items for each iterator.

#### Code implementation

```java
import edu.princeton.cs.algs4.StdRandom;
import java.util.Iterator;
import java.util.NoSuchElementException;

public class RandomizedQueue<Item> implements Iterable<Item> {
    private Item[] queue;
    private int size;       //invariant for both enqueuing and dequeuing in this case

    public RandomizedQueue(){
        this.queue = (Item[]) new Object[4];
        this.size = 0;
    }
    
    public boolean isEmpty()  { return this.size == 0; }
    public int size()         { return size; }
    
    public void enqueue(Item item) {
        validate(item);
        resize();
        this.queue[this.size] = item;
        this.size += 1;
    }
    
    /**
     * 1.find a random item uniformly
     * 2.switch it with the item at the dequeue side (dont care about queue order)
     * 3.Set item to be dequeued to null, avoiding loitering
     * 4.shrink the queue and pop off item to be dequeued
     * @return item to be dequeued
     */
    public Item dequeue() {
        validate();
        resize();
        int index = findIndex();
        Item itemToDequeue = this.queue[index];
        Item itemAtDequeueSide = this.queue[this.size - 1];
        this.queue[index] = itemAtDequeueSide;
        this.queue[this.size - 1] = null;      //switch the item at dequeue side with item to be dequeued
        this.size -= 1;
        return itemToDequeue;
    }

    public Item sample() {
        validate();
        int randomIndex = findIndex();
        return queue[randomIndex];
    }

    /* find a index of NOT NULL Item randomly */
    private int findIndex() {
        int index = StdRandom.uniform(0, this.size);
        return index;
    }

    private void resize() {
        Item[] newArray;
        if (this.size == this.queue.length) {
            newArray = (Item[]) new Object[2 * queue.length];
            System.arraycopy(queue, 0, newArray, 0, this.size);
            this.queue = newArray;

        } else if (this.queue.length > 4  && this.size <= queue.length / 4) {
            newArray = (Item[]) new Object[queue.length / 2];
            System.arraycopy(queue, 0, newArray, 0, this.size);
            this.queue = newArray;
        }
    }

    private void validate(Item item) {
        if (item == null) throw new IllegalArgumentException("can not add null");
    }
    private void validate() {
        if (isEmpty()) throw new NoSuchElementException("The Queue is empty!");
    }
    
    public boolean isEmpty()          { return this.size == 0; }
    public int size()                 { return size; }
    public Iterator<Item> iterator()  { return new RQIterator(); }
    
    private class RQIterator implements Iterator<Item>{
        private Item[] a;
        private int index;

        public RQIterator() {
            a = (Item[]) new Object[size()];
            this.index = size() - 1;
            for (int i = 0; i < size(); i++) {
                a[i] = queue[i];
            }
            StdRandom.shuffle(a);
        }

        @Override
        public boolean hasNext() {
            if (this.index >= 0) {
                return true;
            } else {
                return false;
            }
        }
        @Override
        public Item next() {
            if (!hasNext() || size() == 0) throw new NoSuchElementException("Queue has no more items to iterate");
            Item item = a[index];
            index -= 1;
            return item;
        }
    }
}
```

 In this implementation, we do NOT need `head` and `tail` reference index because we maintain the rule of **exchange** when removing.

> It is by uniformly and randomly choosing a item in current queue to remove and then switch it with the enqueue-side item that we indirectly removing items always from the enqueue side.

In this way, we keep the randomized queue always starting from a fixed position that is `queue[0]` and never need a pair of head & tail to regulate the queue.

