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

### Asymptotic Analysis

cost model: swap operation `swap (a, j, j - 1)`

The runtime of insertion sort is a little complicate to calculate because it actually  depends on the order of original input array. The key is that the numbers [_inversions_](https://en.wikipedia.org/wiki/Inversion_%28discrete_mathematics%29) determine the amount of works and runtime of insertion sort.\( i.e. n inversions need n swap operations\)

* Best case : `Θ(n)` ,when having 0 inversions and thus already ordered
* Worst case: `Θ(n^2)`,  when having `n(n - 1)/2` inversions and in descending order.

> Given this, we can accurately express the runtime of insertion sort as `Θ(n + I)`, where `I` is the number of inversions of the original array. This matches our original runtime bounds - in a sorted array, there are 0 inversions, and the runtime is `Θ(n + 0) = Θ(n),` and in a reverse-sorted array, there are `n(n - 1)/2` inversions, and the runtime is `Θ(n + n(n-1)/2) = Θ(n^2)`.

* Average case? `Θ(n^2)`

Reference: [Why is insertion sort Θ\(n^2\) in average case?](https://stackoverflow.com/questions/17055341/why-is-insertion-sort-%CE%98n2-in-the-average-case)

