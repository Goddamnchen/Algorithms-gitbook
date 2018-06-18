# Insertion Sort

## Model Design

### Insertion Sort Problem

To avoid exerting redundant comparing operation when already having parts of ordered objects, we come out with this new sort algorithms, which is more efficient than selection sort.

* **Abstraction**: 
  * Use`array[N]` to abstract these unordered objects 
  * Sort items, corresponding to the sequence of ordered index of array.
* **Define** _**insertion**_
  * The idea of insertion is that we want to keep every newly entry object in order by inserting each object to a supposedly right position.
  * For each `iteration i`, we will iterate to encounter a newly entry object `a[i]`. And insert this object to its proper position by `swap a[i]` with each larger entry on its left until arriving its supposed position.
* **Consider invariant**
  * For insertion sort, we only focus on the existing sorted sequence of objects OTHER THAN entire   collection. For each `iteration i` or pointer, the invariant is that, having no idea about  objects to be encountered in the future,  the existing sequence of objects, which have been iterated currently or before, is CHANGEABLE and MUST be maintained in order. 

![](.gitbook/assets/image%20%2811%29.png)

Therefore, the insertion sort model turns out to be:

1. Strat from `Iteration i`
2. Compare current `array[i]` with it's left `array[i-1], i > 0 ;`
   * if `array[i - 1] <= array[i]`, iterate to `i + 1;`
   * else if `array[i - 1] > array[i], swap(a[i], a[i-1])`, then iterate to `i - 1;`
3. Go to step 2

## Algorithm Analysis

The core operations in insertion sort process is the same as  **compare** and **swap** in selection sort. So we will skip the detail of support methods `less()` and `swap()`, and directly call them in our integral code implementation.

### Code implementation

{% code-tabs %}
{% code-tabs-item title="InsertionSort java implementation" %}
```java
public class InsertionSort {
    public static void sort(Comparable a[]) {
        for (int i = 0; i < a.length; i++) {
            for (int j = i; j > 0; j--) {
                if (less(a[j], a[j - 1]) swap(a, j, j - 1);
                else break;
            }
        }
    }       
     private static boolean less(Comparable a, Comparable b) {/* saem as selection sort */}
     private static void swap(Comparable[] a, int i, int j) {/* same as selection sort */}
 }
```
{% endcode-tabs-item %}
{% endcode-tabs %}

