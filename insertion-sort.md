# Insertion Sort

## Model Design

### Insertion Sort Problem

To avoid exerting redundant comparing operation when already having parts of ordered objects, we come out with this new sort algorithms, which is more efficient than selection sort.

* **Abstraction**: 
  * Use`array[N]` to abstract these unordered objects 
  * Sort items, corresponding to the sequence of ordered index of array.
* **Define** _**insertion**_
  * The idea of insertion is that we want to keep every newly entry object in order by inserting each object to a supposedly right position.
  * For each `iteration i`, we will iterate to encounter a newly entry object `a[i]`. And insert this object to its proper position by `swap a[i]` with each larger entry on its left.
* **Consider invariant**
  * 



