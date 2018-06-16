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



