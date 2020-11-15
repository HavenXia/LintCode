# 题目地址

https://www.lintcode.com/problem/bash-game/



# 题目描述

You are playing the following Game with your friend: There is a heap of stones on the table, each time one of you take turns to remove 1 to 3 stones. The one who removes the last stone will be the winner. **You will take the first turn to remove the stones.**

Both of you are very clever and have optimal strategies for the game. Write a function to determine whether you can win the game given the number of stones in the heap.

For example, if there are 4 stones in the heap, then you will never win the game: no matter 1, 2, or 3 stones you remove, the last stone will always be removed by your friend.

### Example

**Example 1：**

```
Input：n = 4 
Output：False
Explanation：Take 1, 2 or 3 first, the other party will take the last one
```

**Example 2：**

```
Input：n = 5 
Output：True
Explanation：Take 1 first，Than，we can win the game
```



# 思路

重点在于最后一次我方取石子的时候, 是不是小于等于3个,小于等于3个是**先手必赢的**, 而4个就不一定了

用分治法的思想来做

+ 如果 **n-1/-2/-3至少有一个是必败的, 那么n必然是必胜的**, 只要我方选i,对对方而言都是必败

  如何验证?  **`(!boolean1 || !boolean2 || !boolean3)` 如果有任意一个是必败, 那么得到的就是true**

+ 而且, **如果n-1/-2/-3都是必胜的, 那么n必然是必败的,** 不管我方选什么, 对对方而言都是必胜

  如何验证?  **`(boolean1 && boolean2 && boolean3)` 只有在全都是必胜的情况下, 才能得到true**

  <font color = red>其实这个就是上面的else...没有必败的那就是都必胜了</font>

 ```java
public boolean canWinBash(int n) {
    return memoSearch(n, new HashMap<Integer, Boolean>());
}

private boolean memoSearch(int n, Map<Integer, Boolean> memo) {
    if (n <= 3) {
        return true;
    }
    if (memo.containsKey(n)) {
        return memo.get(n);
    }
    // 沿用分治法的思想
    boolean win1 = memoSearch(n - 1, memo);
    boolean win2 = memoSearch(n - 2, memo);
    boolean win3 = memoSearch(n - 3, memo);
    if (!win1 || !win2 || !win3) {
        memo.put(n, true);
        return true;
    } 
    memo.put(n, false);
    return false;
}
 ```

**然而stackOverflow了!!! 因为时间复杂度是O(n), 所以递归深度也是n层,很容易overflow**



### 直接找规律

简单粗暴

```java
public boolean canWinBash(int n) {
    if (n % 4 == 0) {
        return false;
    }
    return true;
}
```



# 题解

时间复杂度O(n),空间复杂度O(1)

```java
public boolean canWinBash(int n) {
    if (n % 4 == 0) {
        return false;
    }
    return true;
}
```

