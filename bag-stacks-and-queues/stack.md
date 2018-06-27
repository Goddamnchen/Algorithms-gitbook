# Stack

## Model Design

### What is a stack?

A stack is a collection that is based on _last-in-first-out_\(LIFO\) policy.

![Abstract data structure example: Stack, a collection LIFO principle](../.gitbook/assets/image%20%2813%29.png)

As we can see from the picture above, stack is different with bag in terms of supporting both add\(push\) and remove\(pop\) operation, iterating through all items in stack, and existing specific order of items that in & out of stack.

> _last-in-first-out_\(LIFO\):  Both push and pop operations will manipulate items in/out the same side of collection.

### API definition

{% code-tabs %}
{% code-tabs-item title="API of Stack in java implementation" %}
```java
public class Stack<Item> implements Iterable<Item> {
         Stack();
    void push(Item item);
    Item pop();
 boolean isEmpty();
     int size();
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Algorithm Analysis



### Code implementation

