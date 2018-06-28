# Queue

## Model Design

### What is a queue?

A stack is a collection that is based on _**first-in-first-out**_**\(FIFO\)** policy.

![Abstract data structure example: Queue, a collection FIFO principle](../.gitbook/assets/image%20%2854%29.png)

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

![Queue structure example: represented by doubly  LinkedList](../.gitbook/assets/image%20%2856%29.png)

We will enqueue at the end of LinkedList and dequeue from the front of LinkedList.

#### Code implementation

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

_Refer to_ [_here_](stack.md#code-implementation) _for Iterator implementation detail._

#### Time complexity

Every operation of LinkedList based Queue including iterator will take **constant** time.

| LinkedList based | Best | Worst | Amortized |
| --- | --- | --- | --- | --- |
| Construct | 1  | 1 | 1 |
| enqueue | 1 | 1 | 1 |
| dequeue1 | 1 | 1 |  |
| size | 1  | 1 | 1   |

#### Space complexity

Using same 40 bytes per node and additional 8 bytes for last node reference in total.

### Array based



