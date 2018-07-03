# Stack

## Model Design

### What is a stack?

A stack is a collection that is based on __**last-in-first-out\(LIFO\)** policy.

![Abstract data structure example: Stack, a collection LIFO principle](../.gitbook/assets/image%20%2816%29.png)

As we can see from the picture above, stack is different with bag in terms of supporting both insert\(push\) and remove\(pop\) operation, iterating through all items in stack, and existing specific order of items that in & out of stack.

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

![Stack structure example: naturally represented by raw recursive LinkedList](../.gitbook/assets/image%20%2822%29.png)

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
    public Iterator<Item> iterator() { /* see LinkedList based stack iterator below */ }

    // an iterator, doesn't implement remove() since it's optional
    private class ListIterator<Item> implements Iterator<Item> { 
    /* see LinkedList based stack iterator below */
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Iterator implementatIon

{% code-tabs %}
{% code-tabs-item title="LinkedList based stack iterator" %}
```java
public Iterator<Item> iterator() {
    return new ListIterator<Item>(first);
}

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
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Time&Space complexity

{% tabs %}
{% tab title="Time" %}
Every operation of LinkedList based Stack including iterator will take **constant** time.

| LinkedList based | Best | Worst | Amortized |
| --- | --- | --- | --- | --- |
| Construct | 1  | 1 | 1 |
| push | 1 | 1 | 1 |
| pop | 1 | 1 | 1 |
| size | 1  | 1 | 1   |
{% endtab %}

{% tab title="Space" %}
![Memory used example: 40bytes per stack ndoe](../.gitbook/assets/image%20%285%29.png)
{% endtab %}
{% endtabs %}

### Array based

To implement abstract data structure of stack using array, we will need:

* Array `s[]` to store `N` Items on stack
* push\(\): add new item at `s[N]`
* pop\(\): remove item from `s[N-1]`;

![Stack structure example: represented by array](../.gitbook/assets/image%20%2821%29.png)

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
    private Item[] s; 
    private int n;             // size of the stack

    public Stack(int capacity) {
        s = (Item[]) new Object[capacity];
        n = 0;
    }

    public boolean isEmpty()  { return n == 0; }
    public int size()         { return n; }
    
    public void push(Item item) {
        if (item == null) throw new IllegalArgumentException("can not add null");
        s[n] = item;
        n++;
        if (n > 0 && n == s.length) resize(2 * s.length);
    }

    public Item pop() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        Item item = s[n - 1];             // save item to return
        s[n - 1] = null;                  // delete item and avoid loitering
        n--;
        if (n > 0 && n == s.length/4) resize(s.length/2);
        return item;                      // return the saved item
    }

    public Item peek() {
        if (isEmpty()) throw new NoSuchElementException("Stack underflow");
        return s[n - 1];
    }  
    
    private void resize(int capacity) {
        Item[] newArray = (Item[]) new Object[capacity];
        System.arraycopy(s, 0, newArray, 0, n);
        s = newArray;
    }
    
    /* Returns an iterator to this stack that iterates through the items in LIFO order.*/
    public Iterator<Item> iterator() { /* see Array based stack iterator below */ }

    // an iterator, doesn't implement remove() since it's optional
    private class ArrayIterator<Item> implements Iterator<Item> {
    /* see Array based stack iterator below */
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Iterator implementation

{% code-tabs %}
{% code-tabs-item title="Array based stack iterator" %}
```java
public Iterator<Item> iterator() {
    return new ArrayIterator<Item>(n - 1);
}

private class ArrayIterator<Item> implements Iterator<Item> {
    private int index;
    
    public ArrayIterator(int last) { 
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
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Time&Space complexity

{% tabs %}
{% tab title="Time" %}
Operations NOT including resize of array based Stack will take **constant** time. The resize operation will take **linear** time in the worst case.

{% hint style="info" %}
However,  combining the mechanism of rationally resizing array, operations including resize, such as push/pop will cost **constant amortized** time.
{% endhint %}

| Array based | Best | Worst | Amortized |
| --- | --- | --- | --- | --- |
| construct | 1  | 1 | 1 |
| push | 1 | **N** | 1 |
| pop | 1 | **N** | 1 |
| size | 1  | 1 | 1   |
{% endtab %}

{% tab title="Space" %}
The memory used by array based stack is relevant to the stack size `N`, and directly corresponds to array size assigned by resize mechanism. According to the ratio of `stack items / array size`  , There is a memory interval used by array based stack: \(Object reference of stack node = 8 bytes\)

* Full array: `8N + ～`
* One - quarter full array: `32N + ～`
{% endtab %}
{% endtabs %}

### Comparison

After having both LinkedList and array based implementation, we come out a question:   
**Which one is better to use?**

Linked-List implementation :

* Every operation take **constant** time in worst case
* Use extra time to deal with links and address reference

Array implementation:

* Every operation takes **constant amortized** time.
* Less waste space. 

As we can see, the Linked-List implementation is good for time consume of each  operation, but fails to maintain a lower cost of memory. While the Array implementation saves times and memory from manipulating address reference, but cost time proportional to size N when resizing.

Therefore, the answer is conditional. When having no limitation of memory usage or caring about the performance of each single operation, using Linked-List is better choice. When caring about the efficiency of memory usage or generally maintaining a good time performance, using array is good.

## Shortcut - another way out

### Delegation

We have developed two primary implementation using LinkedList and Array to represent stack. Both these design uses primitive elements like Node class and array structure to construct the abstract data stricture of Stack. However, we could also find another way out by using a well-developed API called _Deque,_ a collection could add/remove from both sides.

It is a easier and common to way to construct our Stack API by delegating the Deque API. I write the example of Stack API delegated by ArrayDeque, maintain the same performance as primitive array implementation.

{% code-tabs %}
{% code-tabs-item title="Delegation based stack implementation" %}
```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.Iterator;

public class Stack<Item> implements Iterable<Item> {
    private Deque<Item> stack;

    public Stack() {
        stack = new ArrayDeque<>();
    }
    public void push(Item item) {
        stack.addLast(item);
    }
    public Item pop() {
        Item returnValue = stack.removeLast();
        return returnValue;
    }
    public int size() {
        return stack.size();
    }
    public boolean isEmpty() {
        return size() == 0;
    }
    public Iterator<Item> iterator() {
        return stack.descendingIterator();
    }
    //test
    public static void main(String[] args) {
        Stack<Integer> intStack = new Stack<>();
        intStack.push(1);
        intStack.push(2);
        intStack.push(3);
        intStack.push(4);
        intStack.pop();
        for (int i : intStack) {
            System.out.println(i);
        }
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

 

