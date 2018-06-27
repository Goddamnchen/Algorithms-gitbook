# Selection Sort

## Model Design

### Selection Sort Problem

When talking about sort problem, we want to build a model which could sort an unordered sequence be means of "_selection_".

![Problem example: unordered sequence](../.gitbook/assets/image%20%2836%29.png)

* **Abstraction**: 
  * Firstly, we naturally use `array[N]` to abstract these unordered items and their sequence.
  * With the ordered index sequence of array, we want to sort these unordered objects, corresponding to array index.
* **Define** _**selection**_ ****
  * _Selection_ is the crucial way that we sort. The basic idea of selection is that we repeatedly select an object which is the **smallest** one **in certain range of sequence** AND place the smallest object to the first index of this range of sequence.
  * In this case, we exert iteration starting from the first index of array. In `iteration i`, find index `min` of smallest **in remaining objects** and put it to `array[i]`
* **Consider invariant**
  * For selection sort, we have an pointer or `iteration i` scanning entire collection of items from left to right.  To each iteration, the invariant is that the left side of pointer is sorted, which is UNCHANGEABLE, and the pointer ALWAYS point to the smallest objects among the right of pointer, which is unsorted.

![Invariant example: unchangeable left side, pointer always need to point to the smallest of remain](../.gitbook/assets/image%20%2827%29.png)

Therefore, the selection sort model turns out to be:

1. Strat from `Iteration i`
2. Compare current `array[i]` with EACH remaining entry to find the minimum entry `min` on the right
3. Swap `array[i]` and `array[min]`
4. Pointer to the next,`i++`

## Algorithm Analysis

According to analysis above, we could easily find there are 2 repeatedly core operations, **compare** and **swap**.  
Therefore, we start from building modules of support methods `less()` and `swap()`

### Code implementation

{% code-tabs %}
{% code-tabs-item title="Compare and swap module java implementation" %}
```java
/* implementing comparasion between array[i] and each remaining object */
private static boolean less(Comparable a, Comparable b) {
    if (a.compareTo(b) == -1) return true;
    else                      return false;
}
/* swap two elements with index i and j in certain type of array implementing Compararble */
private static void swap(Comparable[] a, int i, int j) {
    Comparable exch = a[i];
    a[i] = a[j];
    a[j] = exch;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

With these support modules, all we left to do is building iteration in main method.

{% code-tabs %}
{% code-tabs-item title="Selection sort java implementation " %}
```java
 public class SelectionSort {
     public static void sort(Comparable[] a) {
         int N = a.length;
         for (int i = 0; i < N; i++) {
             int min = i;
             for (int j = i + 1; j < N; j++) 
                 if (less(a[min], a[j]) min = j;
             swap(a, i, min);
        }
    }
     private static boolean less(Comparable a, Comparable b) {/* as above */}
     private static void swap(Comparable[] a, int i, int j) {/* as above */}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Asymptotic Analysis

cost model: comparison operation `if (less(a, b))`

We can using _geometric argument_  to clearly display and calculate the order-of-growth of  selection sort:

![Geometric argument example: order of growth = N^/2 + ~](../.gitbook/assets/image%20%2812%29.png)

{% hint style="info" %}
Obviously, the order-of-growth will be `N^2/2 + ~`, which will take **quadratic** time. 
{% endhint %}

## Defect

Unfortunately, Whatever ratio of ordered objects in the array, the cost of selection sort is **stable** and **costly** as to input size `N`. This is because the algorithm need to iterate all remaining entries to compare and find the smallest one during each iteration even though the smallest one is already at its place.

