# Stack

## Model Design

### What is a stack?

A stack is a collection that is based on _last-in-first-out_\(LIFO\) policy.

![Abstract data structure example: Stack, a collection LIFO principle](../.gitbook/assets/image%20%2814%29.png)

As we can see from the picture above, stack is different with bag in terms of supporting both add\(push\) and remove\(pop\) operation, iterating through all items in stack, and existing specific order of items that in & out of stack.

> _last-in-first-out_\(LIFO\):  Both push and pop operations will manipulate items in/out the same side of collection.

{% hint style="info" %}
As to clients, the order of iterating through stack matters and should be the **reverse** of order where items were added, presenting from Stack Top --&gt; Stack Bottom.
{% endhint %}

### API definition

{% code-tabs %}
{% code-tabs-item title="API of Stack in java implementation" %}
```java
public class Stack<Item> implements Iterable<Item> {
         Stack();
    void push(Item item);
    Item pop();
    Item peak();        //Returns (but does not remove) the item most recently added to stack.
 boolean isEmpty();
     int size();
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Algorithm Analysis

The Algorithm design of stack can be represented by using both Array and LinkedList. Different type of  implementations could be suitable used under different conditions according to their characteristics. We will analyze their implementation at first, and then compare their dis/advantages.

### LinkedList based

The raw recursive LinkedList is naturally correspond to the property of stack, LIFO.

We can identify that from the following picture. The raw recursive LinkedList has a pointer always pointing to the first node of entire list, which is also the most recently added or removed node of entire list. It is similar that stack pointer always point to the Top of stack.

![Stack structure example: naturally represented by raw recursive LinkedList](../.gitbook/assets/image%20%2820%29.png)

#### Code implementation

{% code-tabs %}
{% code-tabs-item title="LinkedList based stack implementation" %}
```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Stack<Item> implements Iterable<Item> {
    private Node<Item> first;     // top of stack
    private int n;                // size of the stack

    // helper linked list class
    private static class Node<Item> {
        private Item item;
        private Node<Item> next;
    }
    
    public Stack() {
        first = null;
        n = 0;
    }

    public boolean isEmpty()  { return first == null; }
    public int size()         { return n; }
    
    public void push(Item item) {
        Node<Item> oldfirst = first;
        first = new Node<Item>();
        first.item = item;
        first.next = oldfirst;
        n++;
    }

    public Item pop() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        Item item = first.item;        // save item to return
        first = first.next;            // delete first node
        n--;
        return item;                   // return the saved item
    }

    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        return first.item;
    }  

    /* Returns an iterator to this stack that iterates through the items in LIFO order.*/
    public Iterator<Item> iterator() {
        return new ListIterator<Item>(first);
    }

    // an iterator, doesn't implement remove() since it's optional
    private class ListIterator<Item> implements Iterator<Item> {
        private Node<Item> current;

        public ListIterator(Node<Item> first) { 
            current = first; 
        }
        public boolean hasNext()   { return current != null; }
        public void remove()       { throw new UnsupportedOperationException();}

        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            Item item = current.item;
            current = current.next; 
            return item;
        }
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Time complexity

Every operation of LinkedList based Stack including iterator will take **constant** time.

#### Space complexity

![Memory used example: 40bytes per stack ndoe](../.gitbook/assets/image%20%285%29.png)

### Array based

To implement abstract data structure of stack, we will need:

* Array `a[]` to store `N` Items on stack
* push\(\): add new item at `a[N]`
* pop\(\): remove item from `s[N-1]`;

![Stack structure example: represented by array](../.gitbook/assets/image%20%2819%29.png)

It seems very convenient to manipulate items of stack based on Array because of the invariant stack size `N`. However, the stack has fixed capacity in this case, which is unpractical. And we still need to do something to not only keep having sufficient stack capacity, but also using stack capacity efficiently.

* Induce `resize()` to enlarge and shrink the capacity of array based stack.
* Full array: 
  * create a new array of **twice** the size and then copy items.
* **One-quarter** full array:  
  * create a new array of **half** the size and then copy items.

> **why shrink the stack when having one-quarter full array other than one-half full array?**  
> To avoid thrashing, which we frequently trigger the costly operation of enlarging and shrinking when having a full stack and push - pop repeatedly.

#### Code implementation

{% code-tabs %}
{% code-tabs-item title="Array based stack implementation" %}
```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Stack<Item> implements Iterable<Item> {
    private Item[] a; 
    private int n;             // size of the stack

    public Stack(int capacity) {
        a = (Item[]) new Object[capacity];
        n = 0;
    }

    public boolean isEmpty()  { return n == 0; }
    public int size()         { return n; }
    
    public void push(Item item) {
        a[n] = item;
        n++;
        if (n > 0 && n == a.length) resize(2 * a.length);
    }

    public Item pop() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        Item item = a[n - 1];             // save item to return
        a[n - 1] = null;                  // delete item and avoid loitering
        n--;
        if (n > 0 && n == a.length/4) resize(a.length/2);
        return item;                      // return the saved item
    }

    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        return a[n - 1];
    }  
    
    private void resize(int capacity) {
        Item[] newArray = (Item[]) new Object[capacity];
        System.arraycopy(a, 0, newArray, 0, n);
        a = newArray;
    }
    
    /* Returns an iterator to this stack that iterates through the items in LIFO order.*/
    public Iterator<Item> iterator() {
        return new ListIterator<Item>(n - 1);
    }

    // an iterator, doesn't implement remove() since it's optional
    private class rrayIterator<Item> implements Iterator<Item> {
        private int index;

        public ListIterator(int last) { 
            index = last; 
        }
        public boolean hasNext()   { return index >= 0; }
        public void remove()       { throw new UnsupportedOperationException();}

        public Item next() {
            if (!hasNext()) throw new NoSuchElementException();
            Item item = a[index];
            index--; 
            return item;
        }
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}



