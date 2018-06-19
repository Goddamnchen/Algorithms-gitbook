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

### Asymptotic Analysis

cost model: swap operation `swap (a, j, j - 1)`

The insertion sort is more efficient than selection because it does NOT need to iterate through all the remaining items during each iteration. The runtime of insertion sort is a little complicate to calculate because it actually  depends on the order of original input array. 

* Best case : `Θ(n)` ,when having 0 inversions and thus already ordered
* Worst case: `Θ(n^2)`,  when having `n(n - 1)/2` inversions and in descending order.

> Given this, we can accurately express the runtime of insertion sort as `Θ(n + I)`, where `I` is the number of inversions of the original array. This matches our original runtime bounds - in a sorted array, there are 0 inversions, and the runtime is `Θ(n + 0) = Θ(n),` and in a reverse-sorted array, there are `n(n - 1)/2` inversions, and the runtime is `Θ(n + n(n-1)/2) = Θ(n^2)`.

* Average case? `Θ(n^2)`

{% hint style="info" %}
The key is that the numbers [_inversions_](https://en.wikipedia.org/wiki/Inversion_%28discrete_mathematics%29) determine the amount of works and runtime of insertion sort.\( i.e. n inversions need n swap operations\). For partially sorted array, insertion sort runs in **linear** time.
{% endhint %}

Reference: [Why is insertion sort Θ\(n^2\) in average case?](https://stackoverflow.com/questions/17055341/why-is-insertion-sort-%CE%98n2-in-the-average-case)

## Improvement

However, there exists situations of which insertion sort is inefficient. This is because elements only move one position at the time even when we're kind of know that they have a long way to go, especially happening when a pair of inversions are far from each other.

To improve this, we introduce _shell sort**.**_

### Shell sort

Shell sort is also called _**h-sorting,**_ which represents the idea that move h position for EACH iteration rather than one.

![h-sorting example: sorting elements with interval of 4](.gitbook/assets/image%20%2826%29.png)

In this case, we recognize that 1-sorting is actually the same as insertion sort. And what shell sort does is to quickly sort long-path inversions in advance. After h-sorting array for a decreasing number of h,  we avoid sort pairs of inversions which are far from each other by moving one by one. And then we could use insertion sort with a better runtime performance.

![shell sort example: a process of 3x + 1 increment sequence](.gitbook/assets/image%20%2839%29.png)

The principle of shell sort has two main benefits which directly give rise to a better performance:

{% hint style="success" %}
1. Big increments  --&gt; small subarray, which can be sorted quickly by whatever ways of sort.
2. Small increments --&gt; nearly in order, which only use linear time to sort by 1-sorting.
{% endhint %}

####  Code implementation

{% code-tabs %}
{% code-tabs-item title="Shell sort with 3x + 1 increament sequence" %}
```java
public class ShellSort {
    public static void sort(Comparable a[]) {
        int h = 1;
        while (h < a.length / 3) h = 3 * h + 1;        // 3x + 1 increment sequence
        while (h >= 1) {        //start h-sorting
            for (int i = h; i < a.length; i++) {    // i <= a.length -1 
                for (int j = i; j >= h; j -= h) {
                    if (less(a[j], a[j - h]) swap(a, j, j - 1);
                    else break;
                }
            }
            h = h / 3;
        }
    }       
     private static boolean less(Comparable a, Comparable b) {/* saem as selection sort */}
     private static void swap(Comparable[] a, int i, int j) {/* same as selection sort */}
 }
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Asymptotic Analysis

The analysis of shell sort is still open and undefined  completely. For now we only know the worst-case number of compares used by shellsort with the `3x + 1` increments is `O(N^3/2).`

However,  we do know shellsort is a example of a simple idea leading to substantial performance gains. It is very practical especially in applications of small system. Because:

* Fast unless array size is huge
* Tiny and fixed amount of code, which is comportable for embedded system
* Hardware sort prototype

