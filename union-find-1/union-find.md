# Union-Find

## Model Design 

### **Dynamic Connectivity Problem**

![Connectivity example: is there a path between p and q?](../.gitbook/assets/image%20%2825%29.png)

Given a dynamic connectivity problem above,  we could address this problem of filtering out pairs that connected or disconnected by means of building a model and data structure with sufficient information about pairs to decide whether a new pair of objects is connected, or called in the same _equivalence class_

We could build this model with following steps:

* **Abstract objects:** 
  * Use `index` of array to represent objects 0 to N - 1 
  * Hide details relevant to object itself \(i.e. object attributes\)

![Abstract example: Using index \[1-10\] to represent ten objects](../.gitbook/assets/image%20%2858%29.png)

* **Define logic connection:**
  * Connect objects by partitioning different `Set`
  * Maximal `Set` of objects are mutually connected
  * Use information relevant to abstracted object \(i.e. array index\) to record connection,  such as the the array `value` of index.

![Logic connection example: mutually connected objects are divided to 3 sets](../.gitbook/assets/image%20%2844%29.png)

Now, if we want to connect  `object[2]` and `object[5]`, all objects will connect together except `object[0]`. Therefore, we could simply achieve their connection by merging connected components of these two objects.

![Connecting example: union two connected components to form a new connection ](../.gitbook/assets/image%20%2837%29.png)

Then, the model of dynamic connectivity problem turns out to be summarized as steps or operations to implement :  

1. Given a set of N objects
2. Input a pair of $$(p,q)∈ N$$ 
   * `Find`: check if two objects are in the same set
   * `Union`: replace components containing two objects with their union
3. Iterate to previous step 

## Algorithm Analysis

### Quick-Find

To implement `Find()` and `Union(p, q)` operations of model, we use quick find algorithms and  described data structures as below:

* `int[] id = new int[N]`
* p and q are connected if and only if they have same **id**, which means  `id[p] == id[q]`

> id: each index of array`id[N]`, which represents an object, initially has a corresponding indexed value of itself, delegating N connected sets of single object. After connecting objects,  objects of a specific connected set will have a same **component identifier** of certain index, demonstrating that objects in this set are all connected to the indexed object.

![Quick find data structure example: objects being connected have same component identifier](../.gitbook/assets/image%20%2835%29.png)

#### Code implementation

Why is quick-find algorithms?

Because it finds whether a pair of objects `(p, q)` are in the same connected component quickly. However, as to `union (p, q)`, the method need to iterate through all objects, trying to find out objects having the same component identifier and updating it to a new one representing a new merged set. This method is absolutely **costly**, as the algorithms implementation shown below:

{% code-tabs %}
{% code-tabs-item title="Quick-find java implementation " %}
```java
public class QuickFindUf {
    private int[] id;
    
    public QuickFindUF(int N) {
        id = new int[N];
        for (int i = 0; i < N; i++)
            id[i] = 1;
    }
    /* check whether p and q are in the same component */
    public boolean connected(int p, int q) {
        return id[p] == id[q];                      //only 2 array accesses
    }
    /* change all entries with id[p] to id[q] */
    public void union(int p, int q) {
        int pid = id[p].
        int qid = id[q];
        for (int i = 0; i < id.length; i++)        //2N + 2 array accesses in worst case
            if (id[i] == pid) id[i] == qid;
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Asymptotic Analysis

cost model: number of array accesses

| Algorithms | Initialize | Union | Find |
| --- | --- |
| Quick-Find | $$N$$ | $$N$$ | $$1$$ |

{% hint style="warning" %}
Union for the Quick-Find is too expensive, taking **quadratic** time to process a sequence of N union commands on N objects.

How can we change our set representation so that combining two sets into their union only requires changing **one** value?
{% endhint %}

### 

### Quick-Union

Another algorithm quick-union is designed to  have smaller order of growth and faster worst-case runtime of  `union(p, q)` operations.

Data structure required by Quick-Union algorithm:

* `int[] id = new int[N]`
* `id[i]` is the parent of object\[i\]
* _Root_ of object\[i\] = `id[id[id[...id[i]...]]]`

_Root_  is  the component identifier of a unique set containing object\[i\].

![Quick-union data structure example: Set{0},{1},{9,2,4,3}, {6,5}, {7}, {8}](../.gitbook/assets/image%20%288%29.png)



![Quick-union data structure example: Object\[i\] is indexing its parent if they are in same component](../.gitbook/assets/image%20%2848%29.png)

#### Code implementation

Differences in quick-union implementation?

The most significant distinction for quick-union algorithm is the **indexed value of object\[i\]** will be the **parent of object\[i\]** if existing connection relationship, which means different objects in a same connected component will NOT have the same indexed value as component identifier any more.

In which case,  we need a `private root(i)` method to get the object\[i\]'s parent repeatedly until reaching the root of a set, then checking if two objects are in the same set by comparing their component identifier.   
And we should only focus on the conversion of component identifier of different sets when connecting and construct a new union.

{% code-tabs %}
{% code-tabs-item title="Quick-union java implementation" %}
```java
public class QuickUnionUF {
    private int[] id;
    
    public QuickUnionUF(int N) {
        id = new int[N];
        for (int i = 0; i < N; i ++) id[i] = i;
    }
    /* chase parent pointer until reach root */
    private int root(int i) {
        while (i != id[i]) i = id[i];        //depth = N array accesses in worst case
        return i;
    }
    
    public boolean connected(int p, int q) {
        return root(p) == root(q);
    }
    /* change root of p to point to root of q */
    public void union(int p, int q) {        //depth of p and q array accesses
        int i = root(p);
        int j = root(p);
        id[i] = j;
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Asymptotic Analysis

cost model: number of array accesses

| Algorithms | Initialize | Union | Find |
| --- | --- | --- |
| Quick-Find | $$N$$ | $$N$$ | $$1$$ |
| Quick-Union | $$N$$ | \*\*\*\*$$N$$\*\*\*\* | \*\*\*\*$$N$$\*\*\*\* |

{% hint style="warning" %}
The quick-union algorithm seems carrying out a tree data structure which is faster than the implementation of quick-find because it does not need to go through the entire array for each input pair.  
Unfortunately, depending on the input size and height of trees, the `Find` operation could take time ranging from **constant** to **linear**, which can NOT enable us to guarantee it to be faster in every case.
{% endhint %}

## Improvements 

### Weighted quick-union

Now, we exert some improvements based on the previous algorithm, aiming to have better performance during runtime. We introduce weighted quick-union algorithm to optimize as following:

* Modify quick-union to **avoid tall trees**
* Keep track of the size of each connected component

To avoid tall trees and balance each connected set to be **flatter**, we keep linking  root of smaller tree to root of larger tree.

![Improvement example: always let root of larger sized tree connect to root of smaller sized tree](../.gitbook/assets/image%20%2831%29.png)

#### Code implementation

what's different?

* A new instance variable int\[\] size to keep track of trees size
* Modify method `union(p, q)` to implement connection by comparing size of component identifier
* Update the newly merged set size after connecting

{% code-tabs %}
{% code-tabs-item title="Weighted quick-union java implementation" %}
```java
public class WeightedQuickUnion {
    private int[] id;
    private int[] size;        //initialize to 1 as a single set
    
    public void union(int p, int q) {
        int i = root(p);
        int j = root(q);
        if (i == j) return;
        if (size[i] < size[j]) { id[i] = j; size[j] += size[i]; }
        else                   { id[j] = i; size[i] += size[j]; }
    }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

#### Asymptotic Analysis

cost model: number of array accesses

| Algorithms | Initialize | Union | Find |
| --- | --- | --- | --- |
| Quick-Find | $$N$$ | $$N$$ | $$1$$ |
| Quick-Union | $$N$$ | $$N$$ | $$N$$ |
| Weighed Quick-Union | $$N$$ | $$logN$$ | \*\*\*\*$$logN$$\*\*\*\* |

In this case, the path to find the root of object in any components, which is also called `Depth`,  is at most $$lgN$$ rather than $$N$$  
why?     

* The `Depth`increases by 1 only when tree $$T1$$ __containing an object __$$x$$ __is merged into another LARGER tree $$T2$$_._
* The size of merged new tree $$T2≥2*T1$$.
* So the **LARGEST** tree size $$N$$ at most comes from $$logN$$ times of double of the **SMALLEST** single tree.

![Depth illustration example: smaller tree T1 connect to larger tree T2](../.gitbook/assets/image%20%2820%29.png)

{% hint style="success" %}
Finally, the weighted quick-union is efficient and could totally take $$N+M*logN$$ linear time in worst case.  
\*$$M$$ is the executed times of `Find` operations.
{% endhint %}



### Extra improvement: path compression

One advantage of weighted quick-union algorithm is that it is easy to improve further.  
We are capable of keeping improve it performance by introducing _**Path Compression**_

_Path Compression_ == Having a further flatter tree, enabling Find operation to get the component identifier quicker by using some kind of _SHORTCUT_.

#### Code implementation

_SHORTCUT:_ Make every other node in path point to its grandparent \(thereby **halving path length**\).

{% code-tabs %}
{% code-tabs-item title="Path compression java implementation" %}
```java
private int root(int i) {
    while (i != id[i]) {
        id[i] = id[id[i]];        //one line improvement
        i = id[i];
    }
    return i;
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Except for nodes directly connecting to the root, this will authentically change and halve the path from `ith` node to the root every time when method is called. 

## Experiment

{% page-ref page="union-find-application.md" %}



