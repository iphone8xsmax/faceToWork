[TOC]

### 基础查找算法

#### 顺序查找

顺序查找一般是基于**无序的数组或者链表**，采用顺序查找方式两者其实差不多，就是挨着遍历进行对比。效率很低。需要比较的次数较多。



####  二分查找

二分查找基于**有序数组**。如果是存储的符号表，此时可以使用两个数组分别存放键和值，也可以用二分查找对键进行查找。

> 命题：在 N 个键的有序数组中进行**二分查找**最多需要 **(logN + 1)** 次比较（无论是否成功）。

##### 1. 递归实现

**递归查找流程：**

1、结束递归基础条件：

- 找到就结束递归。

- 递归完整个数组，仍然没有找到 target，也需要结束递归，当 **left > right** 就需要退出。

2、首先确定该数组的**中间**的下标。

```java
int mid = (left + right) / 2;
```

这样写可能超出范围，所以改写一下：

```java
int mid = left + (right - left) / 2;
```

3、然后让需要查找的数 target 和 arr[mid] 比较

```java
target > arr[mid] 	// 说明要查找的数在mid的右边, 因此递归向右查找
target < arr[mid]   // 说明要查找的数在mid的左边, 因此递归向左查找
target == arr[mid]  // 说明找到就返回
```

代码如下：

```java
/**
 * 二分查找算法 递归实现
 *
 * @param array      数组
 * @param left  左边的索引
 * @param right 右边的索引
 * @param target     要查找的值
 * @return 如果找到就返回下标，如果没有找到就返回 -1
 */
public static int binarySearch(int[] array, int left, int right, int target) {
	// Base case
	if (array == null || array.length == 0) return -1;

	// 当left >right 时，说明已经递归整个数组，但是没有找到
	if (left > right) {
		return -1;
	}

	// 找中间索引与值
	int mid = left + (right - left) / 2;
	int midValue = array[mid];

	// 向右递归
	if (target > midValue) {
		return binarySearch(array, mid + 1, right, target);
		// 向左递归
	} else if (target < midValue) {
		return binarySearch(array, left, mid - 1, target);
	} else {
		// 等于时直接返回
		return mid;
	}
}
```

##### 2. 迭代实现

也可**迭代**实现二分查找。注意结束迭代的条件就是 **leftIndex <= rightIndex** 。

```java
/**
 * 二分查找的非递归实现
 *
 * @param array  待查找的数组, arr是升序排序
 * @param target 需要查找的数
 * @return 返回对应下标，-1表示没有找到
 */
public static int binarySearch(int[] array, int target) {
	// 左右索引值
	int left = 0;
	int right = array.length - 1;
	// 说明继续查找
	while (left <= right) {
		int mid = left + (right - left) / 2;
		if (array[mid] == target) {
			return mid;
		} else if (array[mid] > target) {
			// 需要向左边查找
			right = mid - 1;
		} else {
			// 需要向右边查找
			left = mid + 1;
		}
	}
	// Not find
	return -1;
}
```



#### 插值查找

**插值查找**算法类似于二分查找，不同的是插值查找每次从**自适应** mid 处开始查找。将折半查找中的求 mid 索引的公式 , **low** 表示左边索引 left, **high** 表示右边索引 right。

二分查找中计算 mid 索引的公式：

```java
int mid = (left + right) / 2;
```

插值查找中**自适应计算 mid** 索引的算法：

```java
int mid = low + (high - low) * (key - arr[low]) / (arr[high] - arr[low]);   // 插值索引
```

**举例说明插值查找算法的数组**：数组  arr = [1, 2, 3, ......., 100]，假如需要查找的值 1。使用二分查找的话，我们需要**多次递归**，才能找到 1，因为 mid 的值**固定为中间值**。使用插值查找算法，mid 的值根据所**查找的值**进行**自适应计算**出来。

```java
int mid = left + (right – left) * (findVal – arr[left]) / (arr[right] – arr[left])
```

```java
int mid = 0 + (99 - 0) * (1 - 1)/ (100 - 1) = 0 + 99 * 0 / 99 = 0 
```

比如查找值 100。

```java
int mid = 0 + (99 - 0) * (100 - 1) / (100 - 1) = 0 + 99 * 99 / 99 = 0 + 99 = 99 
```

```java
/**
 * 插指查找算法
 *
 * 插值查找算法也要求数组是有序的
 * @param array 数组
 * @param left 左边索引
 * @param right 右边索引
 * @param target 查找值
 * @return 如果找到，就返回对应的下标，如果没有找到返回-1
 */
public static int insertValueSearch(int[] array, int left, int right, int target) {

	System.out.println("插值查找被调用1次");

	// 后面两个条件必须要，否则我们得到的 mid 可能越界（比如findValue值特别巨大，而findValue参与到了mid值的计算中）
	// 同时如果待查找的值比最小值小或比最大值还大，直接返回-1
	if (left > right || target < array[0] || target > array[array.length - 1]) {
		return -1;
	}

	// 使用自适应公式计算 mid 索引值
	int mid = left + (right - left) * (target - array[left]) / (array[right] - array[left]);
	int midValue = array[mid];
	if (target > midValue) {
		// 说明应该向右边递归
		return insertValueSearch(array, mid + 1, right, target);
	} else if (target < midValue) {
		// 说明向左递归查找
		return insertValueSearch(array, left, mid - 1, target);
	} else {
		// 找到就返回
		return mid;
	}
}
```

这里其实和二分查找的代码一模一样，只是中值索引 mid 的计算方式不同，可以有效减少递归的次数。

**注意**：对于数据量**较大**，**关键字分布比较均匀**的查找表来说，采用插值查找, 速度**较快**。比如上面代码中的 1 - 100 的有序数组分布就**比较均匀**。关键字分布不均匀的情况下，该方法不一定比折半查找要好。



#### 裴波那契查找

也叫黄金分割查找。斐波那契数列 {1, 1, 2, 3, 5, 8, 13, 21, 34, 55 } 发现斐波那契数列的两个相邻数的比例，无限接近黄金分割值 0.618。也需要数组**有序**。

**原理**：斐波那契查找原理与前两种相似，仅仅改变了**中间结点（mid）的位置**，mid 不再是通过中间或插值得到，而是位于黄金分割点附近，即 

```java
mid = low + F(k - 1) - 1（F代表斐波那契数列）
```

如下图所示

<img src="../../JavaNotes/B 数据结构与算法/assets/1569571346588.png" alt="1569571346588" style="zoom:70%;" />

**对F(k - 1) - 1的理解：**

由斐波那契数列 F[k] = F[k-1] + F[k-2] 的性质，可以得到 

```java
（F[k]-1）=（F[k-1]-1）+（F[k-2]-1）+1
```

该式说明：只要顺序表的长度为 F[k]-1，则可以将该表分成长度为 F[k-1] - 1 和 F[k-2] - 1的两段，即如上图所示。从而中间位置为 

```java       
mid = low + F(k-1) - 1    
```

类似的，每一子段也可以用相同的方式分割。
但顺序表长度 n **不一定刚好等于** F[k] - 1，所以需要将原来的顺序表长度 n **扩容增加**至 F[k] - 1。这里的 k 值只要能使得 F[k] - 1恰好大于或等于 n 即可, 顺序表长度增加后，新增的位置（从n+1到F[k]-1位置），都赋为 n 位置的值即可。

```java
/**
 * 裴波那契算法
 * @author cz
 */
public class FibonacciSearch {

    public static int maxSize = 20;
    public static void main(String[] args) {
        int [] arr = {1, 8, 10, 89, 1000, 1234};
        System.out.println("index = " + fibonacciSearch(arr, 89));
    }

    /**
     * 因为后面我们 mid = low+F(k-1)-1 需要使用到斐波那契数列，因此我们需要先获取到一个斐波那契数列
     * 此处使用非递归方法得到一个斐波那契数列
     * @return 斐波那契数列
     */
    public static int[] getFibonacciArray() {
        int[] fibo = new int[maxSize];
        fibo[0] = 1;
        fibo[1] = 1;
        for (int i = 2; i < maxSize; i++) {
            fibo[i] = fibo[i - 1] + fibo[i - 2];
        }
        return fibo;
    }

    /**
     * 斐波那契查找算法 非递归方式实现
     *
     * @param array 待查有序数组
     * @param findValue 我们需要查找的关键码(值)
     * @return 返回对应的下标，如果没有返回-1
     */
    public static int fibonacciSearch(int[] array, int findValue) {
        int low = 0;
        int high = array.length - 1;
        // 表示斐波那契分割数值的下标
        int k = 0;
        // 存放mid值
        int midValue = 0;
        // 获取到斐波那契数列
        int[] fiboArray = getFibonacciArray();
        // 获取到斐波那契分割数值的下标
        while(high > fiboArray[k] - 1) {
            k++;
        }
        // 因为 f[k] 值可能大于a的长度，因此我们需要使用Arrays类，构造一个新的数组并指向temp[]
        // 不足的部分会使用0填充
        int[] temp = Arrays.copyOf(array, fiboArray[k]);
        // 实际上需求使用a数组最后的数填充temp
        // 举例:
        // temp = {1,8, 10, 89, 1000, 1234, 0, 0}  =>  {1,8, 10, 89, 1000, 1234, 1234, 1234}
        for(int i = high + 1; i < temp.length; i++) {
            temp[i] = array[high];
        }

        // 使用while来循环处理，找到需要的数据
        while (low <= high) {
            // 只要这个条件满足，就可以找
            midValue = low + fiboArray[k - 1] - 1;
            // 我们应该继续向数组的左边查找
            if(findValue < temp[midValue]) {
                high = midValue - 1;
                // 为甚是 k--
                // 1. 全部元素 = 前面的元素 + 后边元素
                // 2. f[k] = f[k-1] + f[k-2]
                // 因为前面有 f[k-1]个元素,所以可以继续拆分 f[k-1] = f[k-2] + f[k-3]
                // 即在 f[k-1] 的前面继续查找 k--
                // 即下次循环 mid = f[k-1-1] - 1
                k--;
            } else if ( findValue > temp[midValue]) {
                // 我们应该继续向数组的右边查找
                low = midValue + 1;
                // 为什么是k = k - 2
                // 1. 全部元素 = 前面的元素 + 后边元素
                // 2. f[k] = f[k-1] + f[k-2]
                // 3. 因为后面我们有f[k-2] 所以可以继续拆分 f[k-1] = f[k-3] + f[k-4]
                // 4. 即在f[k-2] 的前面进行查找 k = k - 2
                // 5. 即下次循环 mid = f[k - 1 - 2] - 1
                k = k - 2;
            } else {
                // 找到元素，需要确定返回的是哪个下标
                if(midValue <= high) {
                    return midValue;
                } else {
                    return high;
                }
            }
        }
        return -1;
    }
}
```



#### 原理

##### 1. 正常实现

```java
public int binarySearch(int[] nums, int key) {
    int l = 0, h = nums.length - 1;
    while (l <= h) {
        int m = l + (h - l) / 2;
        if (nums[m] == key) {
            return m;
        } else if (nums[m] > key) {
            h = m - 1;
        } else {
            l = m + 1;
        }
    }
    return -1;
}
```

##### 2. 时间复杂度

二分查找也称为折半查找，每次都能将查找区间减半，这种折半特性的算法时间复杂度为 **O(logN)**。

##### 3. m计算

有两种计算中值 m 的方式：

- m = (l + h) / 2
- m = l + (h - l) / 2

l + h 可能出现加法溢出，最好使用第二种方式。

##### 4. 返回值

循环退出时如果仍然没有查找到 key，那么表示查找失败。可以有两种返回值：

- -1：以一个错误码表示没有查找到 key
- l：将 key 插入到 nums 中的正确位置

##### 5. 变种

二分查找可以有很多变种，变种实现要注意边界值的判断。例如在一个有重复元素的数组中查找 key 的最左位置的实现如下：

```java
public int binarySearch(int[] nums, int key) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] >= key) {
            h = m;
        } else {
            l = m + 1;
        }
    }
    return l;
}
```

该实现和正常实现有以下不同：

- 循环条件为 l < h
- h 的赋值表达式为 h = m
- 最后返回 l 而不是 -1

在 nums[m] >= key 的情况下，可以推导出最左 key 位于 [l, m] 区间中，这是一个闭区间。h 的赋值表达式为 h = m，因为 m 位置也可能是解。

在 h 的赋值表达式为 h = mid 的情况下，如果循环条件为 l <= h，那么会出现循环无法退出的情况，因此循环条件只能是 l < h。以下演示了循环条件为 l <= h 时循环无法退出的情况：

```text
nums = {0, 1, 2}, key = 1
l   m   h
0   1   2  nums[m] >= key
0   0   1  nums[m] < key
1   1   1  nums[m] >= key
1   1   1  nums[m] >= key
...
```

当循环体退出时，不表示没有查找到 key，因此最后返回的结果不应该为 -1。为了验证有没有查找到，需要在调用端判断一下返回位置上的值和 key 是否相等。

---

#### 例题

##### 1. 求开方

[69. Sqrt(x) (Easy)](https://leetcode.com/problems/sqrtx/description/)

```html
Input: 4
Output: 2

Input: 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since we want to return an integer, the decimal part will be truncated.
```

一个数 x 的开方 sqrt 一定在 0 \~ x 之间，并且满足 sqrt == x / sqrt。可以利用二分查找在 0 \~ x 之间查找 sqrt。

对于 x = 8，它的开方是 2.82842...，最后应该返回 2 而不是 3。在循环条件为 l <= h 并且循环退出时，h 总是比 l 小 1，也就是说 h = 2，l = 3，因此最后的返回值应该为 h 而不是 l。

```java
public int mySqrt(int x) {
    if (x <= 1) {
        return x;
    }
    int l = 1, h = x;
    while (l <= h) {
        int mid = l + (h - l) / 2;
        int sqrt = x / mid;
        if (sqrt == mid) {
            return mid;
        } else if (mid > sqrt) {
            h = mid - 1;
        } else {
            l = mid + 1;
        }
    }
    return h;
}
```

##### 2. 大于给定元素的最小元素

[744. Find Smallest Letter Greater Than Target (Easy)](https://leetcode.com/problems/find-smallest-letter-greater-than-target/description/)

```html
Input:
letters = ["c", "f", "j"]
target = "d"
Output: "f"

Input:
letters = ["c", "f", "j"]
target = "k"
Output: "c"
```

题目描述：给定一个有序的字符数组 letters 和一个字符 target，要求找出 letters 中大于 target 的最小字符，如果找不到就返回第 1 个字符。

```java
public char nextGreatestLetter(char[] letters, char target) {
    int n = letters.length;
    int l = 0, h = n - 1;
    while (l <= h) {
        int m = l + (h - l) / 2;
        if (letters[m] <= target) {
            l = m + 1;
        } else {
            h = m - 1;
        }
    }
    return l < n ? letters[l] : letters[0];
}
```

##### 3. 有序数组的 Single Element

[540. Single Element in a Sorted Array (Medium)](https://leetcode.com/problems/single-element-in-a-sorted-array/description/)

```html
Input: [1, 1, 2, 3, 3, 4, 4, 8, 8]
Output: 2
```

题目描述：一个有序数组只有一个数不出现两次，找出这个数。要求以 O(logN) 时间复杂度进行求解。

令 index 为 Single Element 在数组中的位置。如果 m 为偶数，并且 m + 1 < index，那么 nums[m] == nums[m + 1]；m + 1 >= index，那么 nums[m] != nums[m + 1]。

从上面的规律可以知道，如果 nums[m] == nums[m + 1]，那么 index 所在的数组位置为 [m + 2, h]，此时令 l = m + 2；如果 nums[m] != nums[m + 1]，那么 index 所在的数组位置为 [l, m]，此时令 h = m。

因为 h 的赋值表达式为 h = m，那么循环条件也就只能使用 l < h 这种形式。

```java
public int singleNonDuplicate(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (m % 2 == 1) {
            m--;   // 保证 l/h/m 都在偶数位，使得查找区间大小一直都是奇数
        }
        if (nums[m] == nums[m + 1]) {
            l = m + 2;
        } else {
            h = m;
        }
    }
    return nums[l];
}
```

##### 4. 第一个错误的版本

[278. First Bad Version (Easy)](https://leetcode.com/problems/first-bad-version/description/)

题目描述：给定一个元素 n 代表有 [1, 2, ..., n] 版本，可以调用 isBadVersion(int x) 知道某个版本是否错误，要求找到第一个错误的版本。

如果第 m 个版本出错，则表示第一个错误的版本在 [l, m] 之间，令 h = m；否则第一个错误的版本在 [m + 1, h] 之间，令 l = m + 1。

因为 h 的赋值表达式为 h = m，因此循环条件为 l < h。

```java
public int firstBadVersion(int n) {
    int l = 1, h = n;
    while (l < h) {
        int mid = l + (h - l) / 2;
        if (isBadVersion(mid)) {
            h = mid;
        } else {
            l = mid + 1;
        }
    }
    return l;
}
```

##### 5. 旋转数组的最小数字

[153. Find Minimum in Rotated Sorted Array (Medium)](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/description/)

```html
Input: [3,4,5,1,2],
Output: 1
```

```java
public int findMin(int[] nums) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] <= nums[h]) {
            h = m;
        } else {
            l = m + 1;
        }
    }
    return nums[l];
}
```

##### 6. 查找区间

[34. Search for a Range (Medium)](https://leetcode.com/problems/search-for-a-range/description/)

```html
Input: nums = [5,7,7,8,8,10], target = 8
Output: [3,4]

Input: nums = [5,7,7,8,8,10], target = 6
Output: [-1,-1]
```

```java
public int[] searchRange(int[] nums, int target) {
    int first = binarySearch(nums, target);
    int last = binarySearch(nums, target + 1) - 1;
    if (first == nums.length || nums[first] != target) {
        return new int[]{-1, -1};
    } else {
        return new int[]{first, Math.max(first, last)};
    }
}

private int binarySearch(int[] nums, int target) {
    int l = 0, h = nums.length; // 注意 h 的初始值
    while (l < h) {
        int m = l + (h - l) / 2;
        if (nums[m] >= target) {
            h = m;
        } else {
            l = m + 1;
        }
    }
    return l;
}
```

---

