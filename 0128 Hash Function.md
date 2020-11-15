# 题目地址

https://www.lintcode.com/problem/hash-function



# 题目描述

In data structure Hash, hash function is used to convert a string(or any other type) into an integer smaller than hash size and bigger or equal to zero. The objective of designing a hash function is to "hash" the key as unreasonable as possible. A good hash function can avoid collision as less as possible. A widely used hash function algorithm is using a magic number 33, consider any string as a 33 based big integer like follow:

hashcode("abcd") = (ascii(a) * 333 + ascii(b) * 332 + ascii(c) *33 + ascii(d)) % HASH_SIZE 

​               = (97* 333 + 98 * 332 + 99 * 33 +100) % HASH_SIZE

​               = 3595978 % HASH_SIZE

here HASH_SIZE is the capacity of the hash table (you can assume a hash table is like an array with index 0 ~ HASH_SIZE-1).

Given a string as a key and the size of hash table, return the hash value of this key.



### Example

**Example 1:**

```
Input:  key="abcd", size = 1000
Output: 978
Explanation: (97*33^3 + 98*33^2 + 99*33 + 100*1)%1000 = 978
```

**Example 2:**

```
Input:  key="abcd", size = 100
Output: 78
Explanation: (97*33^3 + 98*33^2 + 99*33 + 100*1)%100 = 78
```

### Clarification

For this problem, you are not necessary to design your own hash algorithm or consider any collision issue, you just need to implement the algorithm as described.



# 思路

Use **magic number 33** to calculate

Like hashcode("abcd") = (ascii(a) * 33<sup>3</sup>  + ascii(b) * 33<sup>2</sup> + ascii(c) *33 + ascii(d)) % HASH_SIZE 

​         = (97* 33<sup>3</sup>  + 98 *33<sup>2</sup> + 99 * 33 +100) % HASH_SIZE  = 3595978 % HASH_SIZE

此处的hash size是给丁的,<font color = green>即开多少个数组space for store</font>

**我们的要求只是用代码实现这个hashfuntion**

可以想到是按照input的length进行一个foorloop 求sum然后module HASH_SIZE 即可

但是这样的话<font color = red>Sum很容易超过Integer的最大值!</font>

利用module的特性, sum的module可以拆分成每个元素的module继续加和

(97* 33<sup>3</sup>  + 98 * 33<sup>2</sup>  + 99 * 33 +100) % HASH_SIZE = 

=[ ((97 * 33 + 98) * 33 + 99) * 33 + 100 ] % HASH_SIZE

用for 循环表示就是:

```java
long answer = 0;
for (int i = 0; i < key.length; i++) {
    answer = ( answer * 33 + (int) key[i] ) % HASH_SIZE;
}
```

**仔细体会!**



# 题解

时间复杂度O(n), 空间复杂度O(1)

```java
public int hashCode(char[] key, int HASH_SIZE) {
        // write your code here
        long answer = 0;
        for (int i = 0; i < key.length; i++) {
            answer = ( answer * 33 + (int) key[i] ) % HASH_SIZE;
        }
        return (int) answer;
    }
```





