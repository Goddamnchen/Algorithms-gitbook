# Queue

## Model Design

### What is a queue?

A stack is a collection that is based on **first-in-first-out\(FIFO\)** policy.

![Abstract data structure example: Queue, a collection FIFO principle](../.gitbook/assets/image%20%2857%29.png)

As we can see from the picture above, queue is different with stack in terms of manipulating both insert\(enqueue\) and remove\(dequeue\) operation on the opposite side INSTEAD OF the same side, complying with first-in-first-out.

Iterating through queue will process items in the order they were added to the queue.

### API definition

{% code-tabs %}
{% code-tabs-item title="API of Queue in java implementation" %}
```java
public class Queue<Item> implements Iterable<Item> {
         Queue();
    void enqueue(Item item);
    Item dequeue();
    Item peak();        //Returns (but does not remove) the item least recently added to queue.
 boolean isEmpty();
     int size();
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Algorithm Analysis

Designing a algorithm of queue can also be represented by using both Array and LinkedList. 

### LinkedList based

Due to the reason that queues need operations on the both side of collection, the raw recursive LinkedList can hardly be used efficiently. It is more convenient to use a LinkedList which maintains pointer pointing to the first and last node in LinkedList.

![Queue structure example: represented by doubly  LinkedList](../.gitbook/assets/image%20%2859%29.png)

We will enqueue at the end of LinkedList and dequeue from the front of LinkedList.

#### Code implementation

{% code-tabs %}
{% code-tabs-item title="LinkedList based queue implementation" %}
```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Queue<Item> implements Iterable<Item> {
    private Node<Item> first;    // beginning of queue
    private Node<Item> last;     // end of queue
    private int n;               // number of elements on queue

    // helper linked list class
    private static class Node<Item> {
        private Item item;
        private Node<Item> next;
    }

    public Queue() {
        first = null;
        last  = null;
        n = 0;
    }
    
    public boolean isEmpty() { return first == null; }
    public int size()        { return n; }

    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Queue underflow");
        return first.item;
    }
    
    public void enqueue(Item item) {
        Node<Item> oldlast = last;
        last = new Node<Item>();
        last.item = item;
        last.next = null;
        if (isEmpty()) first = last;
        else           oldlast.next = last;
        n++;
    }

    /* Removes and returns the item on this queue that was least recently added.*/
    public Item dequeue() {
        if (isEmpty()) throw new NoSuchElementException("Queue underflow");
        Item item = first.item;
        first = first.next;
        n--;
        if (isEmpty()) last = null;   // to avoid loitering
        return item;
    }

    /* Returns an iterator that iterates over the items in this queue in FIFO order.*/
    public Iterator<Item> iterator()  { /* same as previous tack iterator */}  

    private class ListIterator<Item> implements Iterator<Item> {
    /* same as previous stack iterator */
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

_Refer to_ [_here_](stack.md#iterator-implementation) _for Iterator implementation detail._

#### Time&Space complexity

{% tabs %}
{% tab title="Time" %}
Every operation of LinkedList based Queue including iterator will take **constant** time.

| LinkedList based | Best | Worst | Amortized |
| --- | --- | --- | --- | --- |
| construct | 1  | 1 | 1 |
| enqueue | 1 | 1 | 1 |
| dequeue | 1 | 1 | 1 |
| size | 1  | 1 | 1   |
{% endtab %}

{% tab title="Space" %}
Using same 40 bytes per node and additional 8 bytes for reference of tail node in total.
{% endtab %}
{% endtabs %}

### Array based

We will not only need these [_definitions_](stack.md#array-based) from array-based stack but also complement it to implement the queue data structure and manipulate items from both side.

* Still, use array `q[]` to store `N` items in queue
* enqueue\(\): add new item at `q[tail]`.
* dequeue\(\): remove item from `q[head]`.
* Update head and tail modulo the capacity.
* Add resizing array.

![Queue structure example: represented by array](../.gitbook/assets/image%20%2853%29.png)

The implementation of  array-based Queue is a little bit complicated due to the additional `head` and `tail`  reference indexes. In order to resize the full or inefficient used array, we need to pay attention to the positions of both head and tail reference indexes, then resetting them to a proper position.

Maintaining the `head` reference index to the start index of array `q[0]` is very clear when resizing happens. We will see implementation details below.

#### Code implementation

The implementation considers:

1. Maintaining correct index movement and index position of array
2. The case when dequeue to empty queue and then enqueue
3. Different positions case of `head` and `tail` when resizing

{% hint style="info" %}
The array-based implementation does NOT support adding a item of **NULL** !
{% endhint %}

{% code-tabs %}
{% code-tabs-item title="Array-based queue implementation" %}
```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Queue<Item> implements Iterable<Item> {
    private Item[] q; 
    private int n;             // size of the queue
    private head, tail;        

    public Queue(int capacity) {
        q = (Item[]) new Object[capacity];
        n = 0;
        head = 0;
        tail = 0;
    }

    public boolean isEmpty()  { return n == 0; }
    public int size()         { return n; }
    
    public void enqueue(Item item) {
        if (item == null) throw new IllegalArgumentException("can not add null");
        if (n > 0 && n == q.length) resize(2 * q.length);
        if (n != 0) tail = toIndex(tail + 1);
        q[tail] = item;
        n++;
    }

    public Item dequeue() {
        if (isEmpty()) throw new NoSuchElementException("Queue underflow");
        if (n > 0 && n == q.length / 4) resize(q.length / 2);
        Item item = q[head];             // save item to return
        q[head] = null;                  // delete item and avoid loitering
        n--;
        if (n != 0) head = toIndex(head + 1);
        return item;                      // return the saved item
    }

    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Queue underflow");
        return q[head];
    }  

    private void resize(int capacity) {
        newArray = (Item[]) new Object[capacity];
        if (tail < head) {
            System.arraycopy(q, head, newArray, 0, q.length - head);
            System.arraycopy(q, 0, newArray, q.length - head, tail + 1 );
        } else {
            System.arraycopy(q, head, newArray, 0, tail - head + 1);
        }
        q = newArray;
        head = 0;
        tail = n - 1;
    }
   
    private int toIndex(int index){
        int newIndex;
        if (index < 0) {
            newIndex = q.length - 1;
        } else if (index >= q.length) {
            newIndex = 0;
        } else {
            newIndex = index;
        }
        return newIndex;
    }
    
    /* Returns an iterator to this queue that iterates through the items in FIFO order.*/
    public Iterator<Item> iterator() { /* see array-based queue iterator below */ }

    private class ArrayIterator<Item> implements Iterator<Item> {
    /* see array-based queue iterator below */
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Iterator implementation

{% code-tabs %}
{% code-tabs-item title="Array-based queue iterator" %}
```java
public Iterator<Item> iterator() {
    return new ArrayIterator<Item>(head);
}

private class ArrayIterator<Item> implements Iterator<Item> {
    private int p;
    private int count = 0;
    
    public ArrayIterator(int first) { 
        p = first; 
    }
    public boolean hasNext()   { return count < n; }
    public void remove()       { throw new UnsupportedOperationException();}

    public Item next() {
        if (!hasNext()) throw new NoSuchElementException();
        Item item = q[p];
        p = toIndex(p + 1);
        count += 1;
        return item;
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Time&Space complexity

{% tabs %}
{% tab title="Time" %}
| Array-based | Best | Worst | Amortized |
| --- | --- | --- | --- | --- |
| construct | 1  | 1 | 1 |
| enqueue | 1 | **N** | 1 |
| dequeue | 1 | **N** | 1 |
| size | 1  | 1 | 1   |
{% endtab %}

{% tab title="Space" %}
* Full array: `8N + ～`
* One - quarter full array: `32N + ～`
{% endtab %}
{% endtabs %}

### Delegation

It is a little complicated to directly code a queue using primitive array. Therefore, using delegation of Deque API could achieve  a implementation of queue easier.

{% code-tabs %}
{% code-tabs-item title="Delegation based queue implementation" %}
```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Stack<Item> implements Iterable<Item> {
    private Deque<Item> queue;

    public Stack() {
        queue = new ArrayDeque<>();
    }
    public void enqueue(Item item) {
        if (item == null) throw new IllegalArgumentException("can not add null");
        queue.addLast(item);
    }
    public Item dequeue() {
        if (isEmpty()) throw new NoSuchElementException("Queue underflow");
        Item returnValue = queue.removeLast();
        return returnValue;
    }
    public int size() {
        return queue.size();
    }
    public boolean isEmpty() {
        return size() == 0;
    }
    public Iterator<Item> iterator() {
        return queue.Iterator();
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Experiment

{% page-ref page="queue-application.md" %}

