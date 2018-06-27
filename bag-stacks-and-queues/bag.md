# Bag

## Model Design

### What is a bag?

![Bag example: a collection of marbles](../.gitbook/assets/image%20%2822%29.png)

A bag is an abstract data type of collection which does NOT support removing items. It is simply to provide ability of collecting items and iterating through each item in a bag, at the same time, recording size of this bag and whether the bag is empty.

For clients which will manipulate items in certain bags, we iterate through and return each item in a bag to manipulate each item one by one.

![Manipulating items example: using iterator to reach each item](../.gitbook/assets/image%20%2812%29.png)

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

