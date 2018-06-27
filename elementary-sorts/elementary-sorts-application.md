# Elementary Sorts Application

## Shuffling

![](../.gitbook/assets/image%20%2831%29.png)

Shuffling is that, given a array containing items in certain sequence, we want to rearrange the array so that result is a uniformly random permutation.

The natural way to shuffle is that we can generate a random real number for each array entry and then sort the array according to their assigned random number. Obviously, this natural shuffling heavily depends on sort operation, which may takes long time for huge array.

### Knuth Shuffle

We introduce a more efficient way of shuffling, which is _Knuth Shuffle._

The idea of Knuth Shuffle is to Iterate through whole array and :

* In `iteration i`, pick integer `r` between `0` and `i` uniformly at random.
* Swap `a[i]` and `a[r]`.

After iterating through whole array, the Knuth shuffling algorithm produces a uniformly random permutation of the input array in **linear** time.

{% hint style="warning" %}
There is a common pitfall that programmer tends to pick a random integer belonged to `[0, N - 1]` and swap for each iteration i. This can not give a uniformly random result.

The key is random generated number from `[0, i]` or `[i, N - 1]` which has not been seen yet.
{% endhint %}

