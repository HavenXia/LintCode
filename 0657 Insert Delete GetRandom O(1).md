# 题目地址

https://www.lintcode.com/problem/insert-delete-getrandom-o1



# 题目描述

Design a data structure that supports all following operations in average `O(1)` time.

- `insert(val)`: Inserts an item val to the set if not already present.
- `remove(val)`: Removes an item val from the set if present.
- `getRandom`: Returns a random element from current set of elements. Each element must have the same probability of being returned.

### Example

```
// Init an empty set.
RandomizedSet randomSet = new RandomizedSet();

// Inserts 1 to the set. Returns true as 1 was inserted successfully.
randomSet.insert(1);

// Returns false as 2 does not exist in the set.
randomSet.remove(2);

// Inserts 2 to the set, returns true. Set now contains [1,2].
randomSet.insert(2);

// getRandom should return either 1 or 2 randomly.
randomSet.getRandom();

// Removes 1 from the set, returns true. Set now contains [2].
randomSet.remove(1);

// 2 was already in the set, so return false.
randomSet.insert(2);

// Since 2 is the only number in the set, getRandom always return 2.
randomSet.getRandom();
```



# 思路

首先想到用HashSet来做, 这样add和remove都可以实现O(1), 但是取random却不好做, 因为HashSet和Table都不能按index query

因此考虑添加一个arraylist来存储, 但是arraylist能做到add O(1))(<font color = green>因为只在最后add</font>), 但是并不能做到remove O(1)

**解决办法**

用一个**ArrayList**来存储数字, 并且用HashMap存储integer和其index! <font color = red>注意: 绝对不能用arraylist的remove, 那必然不是O(1)</font>

+ add, 检测是否Map存在, 否就直接add到list里, 然后Map存入其index, <font color = green>用list.size() 来获取index</font>
+ remove, 首先通过map获取index, 然后
  + **取arraylist最后一位为last O(1)**
  + **把last put在arraylist(index)里O(1)**
  + **然后删去arraylist最后一位O(1)** 
  + **最后HashMap里remove这个valueO(1)**
  + **检测下val是不是等于last, <font color = red>是否value是arraylist最后一个值,是的话, 做到这里就够了,不然下一步就找不到这个key了</font> **
    + **把HashMap里last的index修改为value的indexO(1)**

+ random, 首先`import Java.util.Random` ,然后`Random random = new Random()` , 通过 `ran.nextInt(UpperBound)` 获取 **0~Upperbound - 1 中任意值**



# 题解

时间复杂度都是O(1)

```java
import java.util.Random;
public class RandomizedSet {
    Map <Integer, Integer> map;
    List <Integer> list;
    Random random;

    public RandomizedSet() {
        // do intialization if necessary
        list = new ArrayList <Integer>();
        map = new HashMap <Integer, Integer>();
        random = new Random();
    }
    /*
     * @param val: a value to the set
     * @return: true if the set did not already contain the specified element or false
     */
    public boolean insert(int val) {
        // write your code here
        if (!map.containsKey(val)) {

            list.add(val);
            map.put(val, list.size() - 1);
            return true;
        } else {
            return false;
        }
    }
    /*
     * @param val: a value from the set
     * @return: true if the set contained the specified element or false
     */
    public boolean remove(int val) {
        // write your code here
        if (map.containsKey(val)) {
            int last = list.get(list.size() - 1);
            int index = map.get(val);
            list.set(index, last);
            list.remove(list.size() - 1);
            map.remove(val);
            if (val != last) {
                map.put(last, index);  
            }
            return true;
        } else {
            return false;
        }
    }
    /*
     * @return: Get a random element from the set
     */
    public int getRandom() {
        // write your code here
        return list.get(random.nextInt(list.size()));
    }
}
```

