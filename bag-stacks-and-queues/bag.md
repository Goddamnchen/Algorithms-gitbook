# Bag

## Model Design

### What is a bag?



![Abstract data structure example: Bag, a collection of marbles](../.gitbook/assets/image%20%289%29.png)

A bag is an abstract data type of collection which does NOT support removing items. It is simply to provide ability of collecting items and iterating through each item in a bag, at the same time, recording size of this bag and whether the bag is empty.

For clients which will manipulate items in certain bags, we iterate through and return each item in a bag to manipulate each item one by one.  


![Manipulating items example: using iterator to reach each item](../.gitbook/assets/image%20%2826%29.png)

{% hint style="info" %}
As to clients, the order of iteration is unspecified and should be immaterial because clients do NOT care about the order how items actually line up in collection and that we take items out of collection.
{% endhint %}

### API definition

In this case, we define the API of bag as following:

{% code-tabs %}
{% code-tabs-item title="API of Bag in java implementation" %}
```java
public class Bag<Item> implements Iterable<Item> {
         Bag();
    void add(Item item);
 boolean isEmpty();
     int size();
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Algorithm Analysis

Because of the inconsequential order of items in collections, defining the implementation of Bag API is easy and  we can arbitrarily use  **LinkedList** or **Resizing Array** to implement.

I choose to use **LinkedList**, which does not have extra cost of resizing ,to implement Bag API.

### Code implementation

{% code-tabs %}
{% code-tabs-item title="API of Bag in java implementation, refer to: https://algs4.cs.princeton.edu/13stacks/Bag.java.html" %}
```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class Bag<Item> implements Iterable<Item> {
    private Node<Item> first;    // beginning of bag
    private int n;               // number of elements in bag

    // helper linked list class
    private static class Node<Item> {
        private Item item;
        private Node<Item> next;
    }

    public Bag() {
        first = null;
        n = 0;
    }
    
    public boolean isEmpty() { return first == null; }
    public int size() { return n; }
    
    public void add(Item item) {
        Node<Item> oldfirst = first;
        first = new Node<Item>();
        first.item = item;
        first.next = oldfirst;
        n++;
    }
    /* Returns an iterator that iterates over the items in this bag in arbitrary order. */
    public Iterator<Item> iterator()  {
        return new ListIterator<Item>(first);  
    }

    // an iterator, doesn't implement remove() since it's optional
    private class ListIterator<Item> implements Iterator<Item> {
        private Node<Item> current;

        public ListIterator(Node<Item> first) {
            current = first;
        }

        public boolean hasNext()  { return current != null;                     }
        public void remove()      { throw new UnsupportedOperationException();  }

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

### Asymptotic Analysis

All operations including iterator obviously take $$constant$$ time .

## Q&A

### The principle of `iterator()` in the delegation design pattern?

Aside from going in to details of LinkedList when designing Bag API, such as Node manipulation, i directly made use of completed API of LinkedList, using a design pattern of abstract data structure, which is called _Delegation_, to implement Bag API.

{% code-tabs %}
{% code-tabs-item title="Bag API design using delegation of LinkedList" %}
```java
import java.util.LinkedList<E>;
​
public class Bag implements Iterable<Item> {
    private LinkedList<Item> list;
    
    public Bag() {
        this.list = new LinkedList<Item>();
        this.size = 0;
    }
    public void add(Item item)    { list.add(item); } 
    public boolean isEmpty()      { return this.size == 0; }  
    public int size()             { return list.size(); } 
    // using iterator of LinkedList<Item>
    public Iterator<Item> iterator()  {
        return list.iterator();
    }          
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

 As the code shown above, there are serval interesting points needing to be notified:

* To implements `Iterable<Item>`, we only need a method of `iterator()`;
* We may not need build a inner class of Iterator&lt;Item&gt;  to define our own iterator. **We can directly use the iterator which has been defined in other abstract data structure!** .

