# 二分查找

## 寻找某个值

### i<=j
👉 思路：

如果循环条件使用 `while(i <= j)` ，此时循环的终止条件为 `i == j + 1` ，此时区间为 `[j + 1, j]`，代入一个具体的数字进去 `[3, 2]`，此时所有的数字都被搜索完了，因为没有任何一个数字既大于等于 3 又小于等于 2 

最后一次循环在 i == j 时：

- 此时 `mid = 2j/2 = j`，说明 j 可以被取到，同时为了避免 `nums[mid]` 索引越界，j 初始化时的初值为 `nums.length - 1` 

  > 是初始化 `j = nums.length - 1` 的原因

- 此时 `mid = 2i/2 = i` ，说明 i 也可以被取到

综合以上两点，说明循环中 i 到 j 的任意一个数都可能被取到， `while(i <= j)` 的搜索区间为  `[i, j]`


```java
public int search(int[] nums, int target) {
    int i = 0, j = nums.length - 1, mid = 0;
    while (i <= j) {
        mid = i + ((j - i) >> 1);
        if (nums[mid] == target) {
            return mid;
        }
        if (nums[mid] < target) {
            i = mid + 1;
        } 
        else {
            j = mid - 1;
        }
    }
    return -1;
}
```


### i < j

👉边界条件：

找到具体值才返回，没找到直接返回 -1


👉 思路：

如果循环条件使用 `while(i < j)` ，此时循环的终止条件为 `i == j ` 

最后一次循环在 `i = j - 1` 时：

- 此时  `mid = (2j - 1) / 2 = j - 1 ` ，说明 mid 无法取到 j 的，于是 j 初始化时的初值设为 `nums.length` ，这样`nums[mid]` 索引就不会越界

  > 是初始化 `j = nums.length` 的原因

- 此时 `mid = (2i + 1) / 2 = i` ，说明 i 可以被取到

综合以上两点， `while(i < j)` 的搜索区间为  `[i, j)`



👉边界条件：

循环的终止条件为 `i == j`  ，此时搜索区间为 `[j, j)` ，j 是取不到的，所以仍有一个数字 j 没有被搜索到，但 while 循环已经停止，所以此时需要多加一个判断。

判断没被搜索的数字 j 是否是 target，不是就返回 -1 


```java
public int search(int[] nums, int target) {
    int i = 0, j = nums.length, mid = 0;
    while (i < j) {
        mid = i + ((j - i) >> 1);
        if (nums[mid] == target) {
            return mid;
        }
        if (nums[mid] < target) {
            i = mid + 1;
        } 
        else {
            j = mid - 1;
        }
    }
    return nums[i] == target ? i : -1;
}
```





## 寻找某个值的右侧边界

### i<j

👉 思路：

同理，由于是寻找右侧边界，所以 `target == nums[mid]` 时缩小左侧边界



👉 边界情况：

对于 target 不存在于 nums[i] 的判断，循环结束前的最后搜索区间为 `[i, j) = [j-1, j)`，只能取到 j - 1

- 当 target 不存在于 nums[i] 且较小时，走 `j = mid`，有 `[j-1, j) -> [j-1, j-1)`，左边界 i 一直为 0，故 `[j-1, j-1) = [0,0)`

- 当 target 不存在于 nums[i] 且较大时，走 `i = mid + 1 `，` [j-1, j) -> [j-1+1,j) = [j,j)  `，右边界 j 一直为 nums.length

```java
int right_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int i = 0, j = nums.length;

    while (i < j) {
        int mid = (i + j) / 2;
        if (target == nums[mid]) {
            i = mid + 1; // 注意
        }
        else if (target > nums[mid]) {
            i = mid + 1;
        }
        else if (target < nums[mid]) {
            j = mid;
        }
    }
    // target小于nums中任何一个值
    if (i == 0 || nums[i - 1] != target) {
        return -1;
    }
    // target大于nums中任何一个值
    return i - 1;
}
```



### i<=j

👉 思路：

同理



👉 边界情况：

对于 target 不存在于 nums[i] 的判断，最后一次循环的搜索区间为 `[i, j] = [j, j]`，只能取到 j（或者i）

- 当 target 不存在于 nums[i] 且较小时，有 j = j - 1，则 `[j, j-1]`，而左边界最小是 0，所以 j 为 -1

- 当 target 不存在于 nums[i] 且较大时，有 i = j + 1 ，则`[j+1,j]` ，右边界一直是 nums.length - 1，所以 j = nums.length - 1

```java
int right_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int i = 0, j = nums.length - 1;

    while (i <= j) {
        int mid = (i + j) / 2;
        if (target == nums[mid]) {
            i = mid + 1;
        }
        else if (target > nums[mid]) {
            i = mid + 1;
        }
        else if (target < nums[mid]) {
            j = mid - 1;
        }
    }
    // target小于、大于nums中任何一个值的情况
    if (j < 0 || nums[j] != target) {
        return -1;
    }
    return j;
}
```





## 寻找某个值的左侧边界

### i<j

👉 思路：

如果使用了 `while(i < j)`，配套使用 `j = nums.length`，搜索空间为 `[i, j)` ，在 mid 被搜索过之后，区间应该被分成 `[i, mid)` 和 `[mid + 1, j)`

所以 `target < nums[mid]` 时，缩小右侧区间，使 `j = mid`，`target > nums[mid]` 时，缩小左侧区间，使 `i = mid + 1`

之所以能够搜索左侧区间，关键在于对 `target == nums[mid]` 这种情况的处理，因为要取左侧边界，所以要缩小右侧区间，因此取 `j = mid` ，在区间 `[i, mid）` 中继续搜索



👉 边界情况：

最后一次循环时，i = j - 1

- target 小的情况下，更新 `j = mid = (2j - 1)/ 2 = j - 1`，i 一直是 0

  > 2j - 1 是一个奇数，然后再向下整除就得到 j - 1

- target 大的情况下，更新 `i = mid + 1 = (2j - 1) / 2 + 1 = j`，j  一直是 nums.length - 1，直接索引即可

```java
int left_bound(int[] nums, int target) {
    if (nums.length == 0) return -1;
    int i = 0;
    int j = nums.length;
    
    while (i < j) { 
        int mid = i + (j - i) / 2;
        if (target == nums[mid]) {
            j = mid;
        } 
        else if (target > nums[mid] ) {
            i = mid + 1;
        } 
        else if (target < nums[mid]) {
            j = mid; 
        }
    }
    // 检查target大于nums中任何一个数以及小于的情况
    if (i >= nums.length || nums[i] != target) {
        return -1;
    }    
    return i;
}
```



### i<=j

👉 思路：

如果使用了 `while(i <= j)`，配套使用 `j = nums.length - 1` ，搜索空间为 `[i, j]` ，在 mid 被搜索过之后，区间应该被分成 `[i, mid - 1]` 和 `[mid + 1, j]` ，区间缩小的思路同理。



👉 边界情况：

最后一次循环时，i = j

- target 小的情况下，更新 j = mid - 1 = i - 1，i 一直是 0

- target 大的情况下，更新 i = mid + 1 = j + 1，j  一直是nums.length - 1，故 i 为 nums.length，索引时注意不要越界

```java
int left_bound(int[] nums, int target) {
    int i = 0;
    int j = nums.length - 1;
    
    while (i <= j) { 
        int mid = i + (j - i) / 2;
        if (target == nums[mid]) {
            j = mid - 1;
        } 
        else if (target > nums[mid] ) {
            i = mid + 1;
        } 
        else if (target < nums[mid]) {
            j = mid - 1; 
        }
    }
    // 检查target大于nums中任何一个数以及小于的情况
    if (i >= nums.length || nums[i] != target) {
        return -1;
    }
    return i;    
}
```







## 防止溢出

代码中 `i + (j - i) / 2` 和 `(i + j) / 2` 的结果相同，但可以有效防止了 `i` 和 `j` 太大直接相加导致溢出。

`i + (j - i) / 2` 也可以写成 `i + ((j - i) >> 1)`



## 向上/向下取整

取中值 mid 时，有两种方式：

- 向下取整：`mid = i + ((j - i) >> 1`

- 向上取整：`mid = i + ((j - i + 1) >> 1`

对于  `while(i <= j)`  ，两种取整方式的结果没有任何区别，因为搜索空间是 `[i, j]` ，每一个数都被搜索到了。

对于 `while(i < j)` ，有一点区别：

- 一般都是把 `while(i < j)` 的区间视为`[i, j)` ，此事应该使用向下取整，如果向上取整则会死循环

  区间变化为 i = mid + 1 和 j = mid ，如果 i = 0, j = 1，向上取整时 m = 1，如果走 j = mid 分支，那么 j 仍然为 1，直接死循环 

- 区间也可以视为 `(i, j]`，同理，这时就必须向下取整，如果向上取整则会死循环