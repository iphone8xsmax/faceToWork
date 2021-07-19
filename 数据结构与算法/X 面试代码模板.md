[TOC]

### 面试代码模板

##### 1. LRU算法

利用双端队列和map实现

```go
package main

import (
	"container/list"
	"errors"	
)

// 保存缓存信息
type CacheNode struct {
	Key,Value interface{}	
}

// 生成新的结点
func (cnode *CacheNode)NewCacheNode(k,v interface{})*CacheNode{
	return &CacheNode{k,v}
}

// LRU缓存类
type LRUCache struct {
	Capacity int	// 容量
	dlist *list.List	// 双端链表
	cacheMap map[interface{}]*list.Element	//缓存用的 Map，value 是链表结点
}

// 生成新的LRU缓存对象，初始化传入缓存大小
func NewLRUCache(cap int)(*LRUCache){
	return &LRUCache{
				Capacity:cap,
				dlist: list.New(),
				cacheMap: make(map[interface{}]*list.Element)}
}

// 获取当前LRU缓存的大小
func (lru *LRUCache)Size()(int){
	return lru.dlist.Len()
}

// 更新LRU
func (lru *LRUCache)Set(k,v interface{})(error){
	if lru.dlist == nil {
		return errors.New("LRUCache结构体未初始化.")		
	}
 	// 如果结点存在，就把结点往前移
	if pElement,ok := lru.cacheMap[k]; ok {		
		lru.dlist.MoveToFront(pElement)
		pElement.Value.(*CacheNode).Value = v // 设置LRU结点的值中缓存结点的值
		return nil
	}
 	
    // 如果不存在就从前端进入链表
	newElement := lru.dlist.PushFront( &CacheNode{k,v} )
	lru.cacheMap[k] = newElement // 缓存键值对
 	
    // 如果当前LRU缓存满了
	if lru.dlist.Len() > lru.Capacity {		
		//移掉最后一个
		lastElement := lru.dlist.Back()
		if lastElement == nil {
			return nil
		}
        // 从链表中拿到这个最后的结点，从map中也删除
		cacheNode := lastElement.Value.(*CacheNode)
		delete(lru.cacheMap,cacheNode.Key)
		lru.dlist.Remove(lastElement)
	}
	return nil
}
 
// 获取LRU，传入Key，返回结点值，是否存在，错误值
func (lru *LRUCache)Get(k interface{})(v interface{},ret bool,err error){
	if lru.cacheMap == nil {
		return v,false,errors.New("LRUCache结构体未初始化.")		
	}
 	
    // 如果结点信息存在，把这个结点移到链表头部
	if pElement,ok := lru.cacheMap[k]; ok {		
		lru.dlist.MoveToFront(pElement)		
		return pElement.Value.(*CacheNode).Value,true,nil
	}
	return v,false,nil
}
 
// 移除结点
func (lru *LRUCache)Remove(k interface{})(bool){
	if lru.cacheMap == nil {
		return false
	}
 	
    // 如果map中存在这个结点，删除，从链表中也进行删除
	if pElement,ok := lru.cacheMap[k]; ok {
		cacheNode := pElement.Value.(*CacheNode)
		delete(lru.cacheMap,cacheNode.Key)		
		lru.dlist.Remove(pElement)
		return true
	}
	return false
}


func main(){
	lru := NewLRUCache(3)
 
	lru.Set(10,"value1")
	lru.Set(20,"value2")
	lru.Set(30,"value3")
	lru.Set(10,"value4")
	lru.Set(50,"value5")
 
	fmt.Println("LRU Size:",lru.Size())
	v,ret,_ := lru.Get(30)
	if ret  {
		fmt.Println("Get(30) : ",v)
	}
 
	if lru.Remove(30) {
		fmt.Println("Remove(30) : true ")
	}else{
		fmt.Println("Remove(30) : false ")
	}
	fmt.Println("LRU Size:",lru.Size())
```

运行结果

```go
LRU Size: 3
Get(30) :  value3
Remove(30) : true
LRU Size: 2
```

##### 2.二分查找

###### [704. 二分查找](https://leetcode-cn.com/problems/binary-search/)

```go
给定一个 n 个元素有序的（升序）整型数组 nums 和一个目标值 target  ，写一个函数搜索 nums 中的 target，如果目标值存在返回下标，否则返回 -1。

示例 1:
输入: nums = [-1,0,3,5,9,12], target = 9
输出: 4
解释: 9 出现在 nums 中并且下标为 4
示例 2:
输入: nums = [-1,0,3,5,9,12], target = 2
输出: -1
解释: 2 不存在 nums 中因此返回 -1
```

```go
func search(nums []int, target int) int {
    l, r := 0, len(nums) - 1
    for l <= r{
        mid := (l + r) / 2
        if nums[mid] == target{
            return mid
        }else if nums[mid] < target{
            l = mid + 1
        }else{
            r = mid - 1
        }
    }
    return -1
}
```

###### [278. 第一个错误的版本](https://leetcode-cn.com/problems/first-bad-version/)

```go
你是产品经理，目前正在带领一个团队开发新的产品。不幸的是，你的产品的最新版本没有通过质量检测。由于每个版本都是基于之前的版本开发的，所以错误的版本之后的所有版本都是错的。

假设你有 n 个版本 [1, 2, ..., n]，你想找出导致之后所有版本出错的第一个错误的版本。

你可以通过调用 bool isBadVersion(version) 接口来判断版本号 version 是否在单元测试中出错。实现一个函数来查找第一个错误的版本。你应该尽量减少对调用 API 的次数。

 
示例 1：
输入：n = 5, bad = 4
输出：4
解释：
调用 isBadVersion(3) -> false 
调用 isBadVersion(5) -> true 
调用 isBadVersion(4) -> true
所以，4 是第一个错误的版本。

示例 2：
输入：n = 1, bad = 1
输出：1
```

```go
/** 
 * Forward declaration of isBadVersion API.
 * @param   version   your guess about first bad version
 * @return 	 	      true if current version is bad 
 *			          false if current version is good
 * func isBadVersion(version int) bool;
 */
func firstBadVersion(n int) int {
    left, right := 0, n
    for left < right{
        mid := (left + right) / 2
        if isBadVersion(mid){
            right =  mid
        }else{
            left = mid + 1
        }
    }    
    return left
} 
```

###### [35. 搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

```
给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
你可以假设数组中无重复元素。

示例 1:
输入: [1,3,5,6], 5
输出: 2

示例 2:
输入: [1,3,5,6], 2
输出: 1

示例 3:
输入: [1,3,5,6], 7
输出: 4

示例 4:
输入: [1,3,5,6], 0
输出: 0
```

```go
func searchInsert(nums []int, target int) int {
    l, r := 0, len(nums) - 1
    if len(nums) == 0 || nums[r] < target{
        return len(nums)
    }
    if nums[l] > target{
        return 0
    }
    for l <= r{
        mid := (l + r) / 2
        if nums[mid] == target{
            return mid
        }else if nums[mid] < target{
            l = mid + 1
        }else{
            r = mid - 1
        }
    }
    return l
}
```

##### 3.双指针

###### [977. 有序数组的平方](https://leetcode-cn.com/problems/squares-of-a-sorted-array/)

```
给你一个按 非递减顺序 排序的整数数组 nums，返回 每个数字的平方 组成的新数组，要求也按 非递减顺序 排序。
示例 1：
输入：nums = [-4,-1,0,3,10]
输出：[0,1,9,16,100]
解释：平方后，数组变为 [16,1,0,9,100]
排序后，数组变为 [0,1,9,16,100]

示例 2：
输入：nums = [-7,-3,2,3,11]
输出：[4,9,9,49,121]
```

```go
func sortedSquares(nums []int) []int {
    n := len(nums)
    lastIndex := -1
    for i := 0; i < n; i++{
        if nums[i] < 0 {
            lastIndex = i
        }
    }
    res := make([]int, 0, n)
    for i, j := lastIndex, lastIndex + 1; i >= 0 || j < n;{
        if i < 0{
            res = append(res, nums[j] * nums[j])
            j++
        }else if j == n{
            res = append(res, nums[i] * nums[i])
            i--
        }else if nums[i] * nums[i] < nums[j] * nums[j]{
            res = append(res, nums[i] * nums[i])
            i--
        }else{
            res = append(res, nums[j] * nums[j])
            j++
        }
    }
    return res
}
```

###### [189. 旋转数组](https://leetcode-cn.com/problems/rotate-array/)

```
给定一个数组，将数组中的元素向右移动 k 个位置，其中 k 是非负数。
进阶：
尽可能想出更多的解决方案，至少有三种不同的方法可以解决这个问题。
你可以使用空间复杂度为 O(1) 的 原地 算法解决这个问题吗？

示例 1:
输入: nums = [1,2,3,4,5,6,7], k = 3
输出: [5,6,7,1,2,3,4]
解释:
向右旋转 1 步: [7,1,2,3,4,5,6]
向右旋转 2 步: [6,7,1,2,3,4,5]
向右旋转 3 步: [5,6,7,1,2,3,4]

示例 2:
输入：nums = [-1,-100,3,99], k = 2
输出：[3,99,-1,-100]
解释: 
向右旋转 1 步: [99,-1,-100,3]
向右旋转 2 步: [3,99,-1,-100]
```

```go
// 先整体翻转，最后的 K % n 个就会在前面，再分别把前面的 k % n翻转，最后反转剩下的
func rotate(nums []int, k int) {
    n := len(nums)
    k = k % n
    reverse(nums)
    reverse(nums[:k])
    reverse(nums[k:])
}

func reverse(nums []int){
    for i := 0; i < len(nums) / 2; i++{
        nums[i], nums[len(nums) - 1 - i] = nums[len(nums) - 1 - i], nums[i]
    }
}
```

###### [283. 移动零](https://leetcode-cn.com/problems/move-zeroes/)

```
给定一个数组 nums，编写一个函数将所有 0 移动到数组的末尾，同时保持非零元素的相对顺序。

示例:
输入: [0,1,0,3,12]
输出: [1,3,12,0,0]

说明:
必须在原数组上操作，不能拷贝额外的数组。
尽量减少操作次数。
```

```go
func moveZeroes(nums []int)  {
    n := len(nums)
    left, right := 0, 0
    for right < n{
        if nums[right] != 0{
            nums[left], nums[right] = nums[right], nums[left]
            left++
        }
        right++
    }
}
```

###### [167. 两数之和 II - 输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/)

```
给定一个已按照 升序排列  的整数数组 numbers ，请你从数组中找出两个数满足相加之和等于目标数 target 。

函数应该以长度为 2 的整数数组的形式返回这两个数的下标值。numbers 的下标 从 1 开始计数 ，所以答案数组应当满足 1 <= answer[0] < answer[1] <= numbers.length 。
你可以假设每个输入只对应唯一的答案，而且你不可以重复使用相同的元素。

示例 1：
输入：numbers = [2,7,11,15], target = 9
输出：[1,2]
解释：2 与 7 之和等于目标数 9 。因此 index1 = 1, index2 = 2 。

示例 2：
输入：numbers = [2,3,4], target = 6
输出：[1,3]

示例 3：
输入：numbers = [-1,0], target = -1
输出：[1,2]
```

```go
// 双指针
func twoSum(numbers []int, target int) []int {
    left, right := 0, len(numbers) - 1
    for left < right{
        sum := numbers[left] + numbers[right]
        if sum == target{
            return []int{left + 1, right + 1}
        }else if sum < target{
            left++
        }else{
            right--
        }
    }
    return []int{-1, -1}
}
```

```go
// map
func twoSum(numbers []int, target int) []int {
    pairs := make(map[int]int)
    for i , v := range numbers{
        if j, ok := pairs[target - v]; ok && i != j{
            return []int{j + 1 ,i + 1}
        }
        pairs[v] = i
    }
    return []int{-1, -1}
}
```

###### [557. 反转字符串中的单词 III](https://leetcode-cn.com/problems/reverse-words-in-a-string-iii/)

```
给定一个字符串，你需要反转字符串中每个单词的字符顺序，同时仍保留空格和单词的初始顺序。

示例：
输入："Let's take LeetCode contest"
输出："s'teL ekat edoCteeL tsetnoc"
提示：
在字符串中，每个单词由单个空格分隔，并且字符串中不会有任何额外的空格。
```

```go
import "strings"
func reverseWords(s string) string {
	a := strings.Split(s, " ")
	for i, v := range a {
		a[i] = reverse(v)
	}
	return strings.Join(a, " ")
}

func reverse(s string) string{
    sByte := []byte(s)
    for i := 0; i < len(s) / 2; i++{
        sByte[i], sByte[len(s) - 1 -i] = sByte[len(s) - 1 -i], sByte[i]
    }
    return string(sByte)
}
```

##### 4.滑动窗口

###### [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

```
给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。

 

示例 1:

输入: s = "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
示例 2:

输入: s = "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
示例 3:

输入: s = "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

```go
func lengthOfLongestSubstring(s string) int {
    n := len(s)
    // map 用来验证是否当前字符与之前的字串有重复
    m := make(map[byte]int, n)
    right := -1 //当前字串的右指针
    res := 0
    for i := 0; i < n; i++{
        if i != 0{
            // 每次窗口向右移一位，即删掉最左侧的
            delete(m, s[i - 1])
        }
        for right + 1 < n && m[s[right + 1]] == 0{ //当前右指针还未重复
            m[s[right + 1]]++
            right++
        }
        res = max(res, right + 1 - i)
    }
    return res
}
func max(a, b int) int{
    if a > b{
        return a
    }
    return b
}
```

###### [567. 字符串的排列](https://leetcode-cn.com/problems/permutation-in-string/)

```
给定两个字符串 s1 和 s2，写一个函数来判断 s2 是否包含 s1 的排列。
换句话说，第一个字符串的排列之一是第二个字符串的 子串 。

示例 1：
输入: s1 = "ab" s2 = "eidbaooo"
输出: True
解释: s2 包含 s1 的排列之一 ("ba").

示例 2：
输入: s1= "ab" s2 = "eidboaoo"
输出: False
```

**(1) 滑动窗口**

```go
func checkInclusion(s1 string, s2 string) bool {
    if len(s1) > len(s2){
        return false
    }
    count1 := [26]int{}
    count2 := [26]int{}
    for i, v := range s1{
        count1[v - 'a']++
        count2[s2[i] - 'a']++
    } 
    if count1 == count2{
        return true
    }
    // 左出右进，滑动窗口
    for i := len(s1); i < len(s2); i++{
        count2[s2[i - len(s1)] - 'a']--
        count2[s2[i] - 'a']++
        if count1 == count2{
        return true
        }
    } 
    return false
}
```

**优化**

```go
func checkInclusion(s1 string, s2 string) bool {
    n, m := len(s1), len(s2)
    if n > m{
        return false
    }
    count := [26]int{}
    for i, v := range s1{
        count[v - 'a']++
        count[s2[i] - 'a']--
    } 
    diff := 0
    for _, v := range count{
        if v != 0{
            diff++
        }
    }
    if diff == 0 {
        return true
    }    
    for i := len(s1); i < len(s2); i++{
        x, y := s2[i]-'a', s2[i-n]-'a'
        if x == y {
            continue
        }
        if count[x] == 0 {
            diff++
        }
        count[x]--
        if count[x] == 0 {
            diff--
        }
        if count[y] == 0 {
            diff++
        }
        count[y]++
        if count[y] == 0 {
            diff--
        }
        if diff == 0 {
            return true
        }
    } 
    return false
}
```

**(2)双指针**

```go
func checkInclusion(s1, s2 string) bool {
    n, m := len(s1), len(s2)
    if n > m {
        return false
    }
    cnt := [26]int{}
    for _, ch := range s1 {
        cnt[ch-'a']--
    }
    left := 0
    for right, ch := range s2 {
        x := ch - 'a'
        cnt[x]++
        for cnt[x] > 0 { //加入当前值之后，导致字符超出，则右移至满足为止
            cnt[s2[left]-'a']--
            left++
        }
        if right-left+1 == n {
            return true
        }
    }
    return false
}
```

##### 5.BFS/DFS

###### [733. 图像渲染](https://leetcode-cn.com/problems/flood-fill/)

```
有一幅以二维整数数组表示的图画，每一个整数表示该图画的像素值大小，数值在 0 到 65535 之间。
给你一个坐标 (sr, sc) 表示图像渲染开始的像素值（行 ，列）和一个新的颜色值 newColor，让你重新上色这幅图像。

为了完成上色工作，从初始坐标开始，记录初始坐标的上下左右四个方向上像素值与初始坐标相同的相连像素点，接着再记录这四个方向上符合条件的像素点与他们对应四个方向上像素值与初始坐标相同的相连像素点，……，重复该过程。将所有有记录的像素点的颜色值改为新的颜色值。

最后返回经过上色渲染后的图像。

示例 1:
输入: 
image = [[1,1,1],[1,1,0],[1,0,1]]
sr = 1, sc = 1, newColor = 2
输出: [[2,2,2],[2,2,0],[2,0,1]]

解析: 
在图像的正中间，(坐标(sr,sc)=(1,1)),
在路径上所有符合条件的像素点的颜色都被更改成2。
注意，右下角的像素没有更改为2，
因为它不是在上下左右四个方向上与初始点相连的像素点。
```

**(1)BFS**

```go
var(
    dx = []int{1, 0, 0, -1}
    dy = []int{0, 1, -1, 0}
)

func floodFill(image [][]int, sr int, sc int, newColor int) [][]int {
    curcolor := image[sr][sc]
    if curcolor == newColor{
        return image
    }
    queue := [][]int{}
    queue = append(queue, []int{sr, sc})

    for len(queue) > 0{
        node := queue[0]
        image[node[0]][node[1]] = newColor
        queue = queue[1:]
        for i := 0; i < 4; i++{
            x := node[0] + dx[i]
            y := node[1] + dy[i]
            if x >= 0 && x < len(image) && y >= 0 && y < len(image[0]){
                if image[x][y] == curcolor{
                    queue = append(queue, []int{x ,y})
                }
            }
        }
    }
    return image
}
```

**(2)DFS**

```go
var(
    dx = []int{1, 0, 0, -1}
    dy = []int{0, 1, -1, 0}
)

func floodFill(image [][]int, sr int, sc int, newColor int) [][]int {
    curcolor := image[sr][sc]
    if curcolor != newColor{
        dfs(image, sr, sc, curcolor, newColor)
    }
    return image
}

func dfs(image [][]int, sr, sc int, curcolor, newColor int){
    if image[sr][sc] == curcolor{
        image[sr][sc] = newColor
        for i := 0; i < 4; i++{
            x := sr + dx[i]
            y := sc + dy[i]
            if x >= 0 && x < len(image) && y >= 0 && y < len(image[0]){
                dfs(image, x, y, curcolor, newColor)
            }
        }
    }
}
```

###### [695. 岛屿的最大面积](https://leetcode-cn.com/problems/max-area-of-island/)

```go
给定一个包含了一些 0 和 1 的非空二维数组 grid 。

一个 岛屿 是由一些相邻的 1 (代表土地) 构成的组合，这里的「相邻」要求两个 1 必须在水平或者竖直方向上相邻。你可以假设 grid 的四个边缘都被 0（代表水）包围着。

找到给定的二维数组中最大的岛屿面积。(如果没有岛屿，则返回面积为 0 。)

 

示例 1:

[[0,0,1,0,0,0,0,1,0,0,0,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,1,1,0,1,0,0,0,0,0,0,0,0],
 [0,1,0,0,1,1,0,0,1,0,1,0,0],
 [0,1,0,0,1,1,0,0,1,1,1,0,0],
 [0,0,0,0,0,0,0,0,0,0,1,0,0],
 [0,0,0,0,0,0,0,1,1,1,0,0,0],
 [0,0,0,0,0,0,0,1,1,0,0,0,0]]
对于上面这个给定矩阵应返回 6。注意答案不应该是 11 ，因为岛屿只能包含水平或垂直的四个方向的 1 。
```

**(1)递归**

```go
func maxAreaOfIsland(grid [][]int) int {
    if len(grid) == 0 || len(grid[0]) == 0{
        return 0
    }
    res := 0
    //遍历二维数组
    for r := 0; r < len(grid); r++{
        for c := 0; c < len(grid[0]); c++{
            //如果当前格子为岛屿
            if grid[r][c] == 1{
                //求相邻的岛屿个数
                count := area(grid, r, c)
                res = max(res, count)
            }
        }
    }
    return res
}
func area(grid [][]int, r, c int) int{
    //如果不是有效的位置
    if !(r >= 0 && r < len(grid) && c >= 0 &&c < len(grid[0])){
        return 0
    }
    //如果不是岛屿1直接返回0个
    if grid[r][c] != 1{
        return 0 
    }
    //到这里说明是岛屿1，此时标记访问过的岛屿为2
    grid[r][c] = 2
    //递归求上下左右的位置是否是1，至少也要返回一个1
    return 1 + area(grid, r - 1, c) + area(grid, r + 1, c) + area(grid, r, c - 1) + area(grid, r, c + 1)
}
func max(x, y int) int{
    if x > y {
        return x
    }
    return y
}
```

**(2)DFS**

```go
var(
    dx = []int{1, 0, 0, -1}
    dy = []int{0, 1, -1, 0}
)
func maxAreaOfIsland(grid [][]int) int {
    x := len(grid)
    y := len(grid[0])
    if x == 0 || y == 0{
        return 0
    }
    res := 0
    for i := 0 ; i < x; i++{
        for j := 0; j < y; j++{
            if grid[i][j] == 1{
                res = max(res, dfs(grid, i, j))
            }
        }
    }
    return res
}

func dfs(grid [][]int, x, y int) int{
    if grid[x][y] == 0{
        return 0
    }
    grid[x][y] = 0
    res := 1
    for i := 0; i < 4; i++{
        mx := x + dx[i]
        my := y + dy[i]
        if mx >= 0 && mx < len(grid) && my >= 0 && my < len(grid[0]){
            res += dfs(grid, mx, my)
        }
    } 
    return res
}
func max(a, b int) int{
    if a > b{
        return a
    }
    return b
}
```

**(3)BFS**

```go
var(
    dx = []int{1, 0, 0, -1}
    dy = []int{0, 1, -1, 0}
)
func maxAreaOfIsland(grid [][]int) int {
    x := len(grid)
    y := len(grid[0])
    if x == 0 || y == 0{
        return 0
    }
    res := 0
    for i := 0 ; i < x; i++{
        for j := 0; j < y; j++{
            if grid[i][j] == 1{
                count := 0
                queue := [][]int{}
                queue = append(queue, []int{i, j})
                for len(queue) > 0{
                    count++
                    node := queue[0]
                    queue = queue[1:]
                    nodeX := node[0]
                    nodeY := node[1]
                    for i := 0; i < 4; i++{
                        mx := nodeX + dx[i]
                        my := nodeY + dy[i]
                        if mx >= 0 && mx < x && my >= 0 && my < y{
                            if grid[mx][my] == 1{
                                queue = append(queue, []int{mx, my})
                            }
                        }
                    }
                }
                res = max(res, count)
            }
        }
    }
    return res
}

func max(a, b int) int{
    if a > b{
        return a
    }
    return b
}
```

###### [617. 合并二叉树](https://leetcode-cn.com/problems/merge-two-binary-trees/)

**(1)DFS**

```go
func mergeTrees(t1, t2 *TreeNode) *TreeNode {
    if t1 == nil {
        return t2
    }
    if t2 == nil {
        return t1
    }
    t1.Val += t2.Val
    t1.Left = mergeTrees(t1.Left, t2.Left)
    t1.Right = mergeTrees(t1.Right, t2.Right)
    return t1
}
```

**(2)BFS**

```go
func mergeTrees(t1, t2 *TreeNode) *TreeNode {
    if t1 == nil {
        return t2
    }
    if t2 == nil {
        return t1
    }
    merged := &TreeNode{Val: t1.Val + t2.Val}
    queue := []*TreeNode{merged}
    queue1 := []*TreeNode{t1}
    queue2 := []*TreeNode{t2}
    for len(queue1) > 0 && len(queue2) > 0 {
        node := queue[0]
        queue = queue[1:]
        node1 := queue1[0]
        queue1 = queue1[1:]
        node2 := queue2[0]
        queue2 = queue2[1:]
        left1, right1 := node1.Left, node1.Right
        left2, right2 := node2.Left, node2.Right
        if left1 != nil || left2 != nil {
            if left1 != nil && left2 != nil {
                left := &TreeNode{Val: left1.Val + left2.Val}
                node.Left = left
                queue = append(queue, left)
                queue1 = append(queue1, left1)
                queue2 = append(queue2, left2)
            } else if left1 != nil {
                node.Left = left1
            } else { // left2 != nil
                node.Left = left2
            }
        }
        if right1 != nil || right2 != nil {
            if right1 != nil && right2 != nil {
                right := &TreeNode{Val: right1.Val + right2.Val}
                node.Right = right
                queue = append(queue, right)
                queue1 = append(queue1, right1)
                queue2 = append(queue2, right2)
            } else if right1 != nil {
                node.Right = right1
            } else { // right2 != nil
                node.Right = right2
            }
        }
    }
    return merged
}
```

###### [116. 填充每个节点的下一个右侧节点指针](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

```
给定一个 完美二叉树 ，其所有叶子节点都在同一层，每个父节点都有两个子节点。二叉树定义如下：

struct Node {
  int val;
  Node *left;
  Node *right;
  Node *next;
}
填充它的每个 next 指针，让这个指针指向其下一个右侧节点。如果找不到下一个右侧节点，则将 next 指针设置为 NULL。

初始状态下，所有 next 指针都被设置为 NULL。
```

**(1)BFS**

```go
/**
 * Definition for a Node.
 * type Node struct {
 *     Val int
 *     Left *Node
 *     Right *Node
 *     Next *Node
 * }
 */

func connect(root *Node) *Node {
    if root == nil {
        return root
    }

    // 初始化队列同时将第一层节点加入队列中，即根节点
    queue := []*Node{root}

    // 循环迭代的是层数
    for len(queue) > 0 {
        tmp := queue
        queue = nil

        // 遍历这一层的所有节点
        for i, node := range tmp {
            // 连接
            if i+1 < len(tmp) {
                node.Next = tmp[i+1]
            }

            // 拓展下一层节点
            if node.Left != nil {
                queue = append(queue, node.Left)
            }
            if node.Right != nil {
                queue = append(queue, node.Right)
            }
        }
    }
    return root
}
```

###### [542. 01 矩阵](https://leetcode-cn.com/problems/01-matrix/)

```
给定一个由 0 和 1 组成的矩阵，找出每个元素到最近的 0 的距离。
两个相邻元素间的距离为 1 。

示例 1：
输入：
[[0,0,0],
 [0,1,0],
 [0,0,0]]
输出：
[[0,0,0],
 [0,1,0],
 [0,0,0]]
```

```go
var(
    dx = []int{1, 0, 0, -1}
    dy = []int{0, 1, -1, 0}
)
func updateMatrix(mat [][]int) [][]int {
    x := len(mat)
    y := len(mat[0])
    res := make([][]int, x)
    for i := 0; i < x; i++{
        res[i] = make([]int, y)
    }
    visited := make([][]bool, x)
    for i := 0; i < x; i++{
        visited[i] = make([]bool, y)
    }

    queue := [][]int{}
    for i := 0; i < x; i++{
        for j := 0; j < y; j++{
            if mat[i][j] == 0{
                queue = append(queue, []int{i, j})
                res[i][j] = 0
                visited[i][j] = true
            }
        }
    } 

    for len(queue) > 0{
        node := queue[0]
        queue = queue[1:]
        nodeX := node[0]
        nodeY := node[1]
        for i := 0; i < 4; i++{
            mx := nodeX + dx[i]
            my := nodeY + dy[i]
            if mx >= 0 && mx < x && my >= 0 && my < y && !visited[mx][my]{
                visited[mx][my] = true
                res[mx][my] = res[nodeX][nodeY] + 1
                queue = append(queue, []int{mx, my})
            }
        }
    }
    return res
}
```

###### [994. 腐烂的橘子](https://leetcode-cn.com/problems/rotting-oranges/)

```
在给定的网格中，每个单元格可以有以下三个值之一：
值 0 代表空单元格；
值 1 代表新鲜橘子；
值 2 代表腐烂的橘子。
每分钟，任何与腐烂的橘子（在 4 个正方向上）相邻的新鲜橘子都会腐烂。
返回直到单元格中没有新鲜橘子为止所必须经过的最小分钟数。如果不可能，返回 -1。

示例1:
输入：[[2,1,1],[1,1,0],[0,1,1]]
输出：4
```

```go
var(
    dx = []int{1, 0, 0, -1}
    dy = []int{0, 1, -1, 0}
)
func orangesRotting(grid [][]int) int {
    x := len(grid)
    y := len(grid[0])
    if x == 0 || y == 0{
        return -1
    }
    res := 0
    visited := make([][]bool, x)
    for i := 0; i < x; i++{
        visited[i] = make([]bool, y)
    }
    queue := [][]int{}
    for i := 0; i < x; i++{
        for j := 0; j < y; j++{
            if grid[i][j] == 2{
                visited[i][j] = true
                queue = append(queue, []int{i, j})
            }
        }
    }

    count := 0
    for len(queue) > 0{
        temp := queue
        queue = nil
        count++
        for _, node := range temp{
            nodeX := node[0]
            nodeY := node[1]
            for i := 0; i < 4; i++{
                mx := nodeX + dx[i]
                my := nodeY + dy[i]
                if mx >= 0 && mx < x && my >= 0 && my < y && !visited[mx][my] && grid[mx][my] == 1{
                    visited[mx][my] = true
                    grid[mx][my] = 2
                    queue = append(queue, []int{mx, my})
                }
            }
        }
        if count > 1{
            res++
        }
    }
    for i := 0; i < x; i++{
        for j := 0; j < y; j++{
            if grid[i][j] == 1{
                return -1
            }
        }
    }
    return res
}
```

##### 6.递归/回溯

[递归回溯问题总集](https://leetcode-cn.com/problems/permutations/solution/dai-ma-sui-xiang-lu-dai-ni-xue-tou-hui-s-mfrp/)

###### [77. 组合](https://leetcode-cn.com/problems/combinations/)

```
给定两个整数 n 和 k，返回 1 ... n 中所有可能的 k 个数的组合。

示例:
输入: n = 4, k = 2
输出:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

```go
func combine(n int, k int) (ans [][]int) {
	temp := []int{}
	var dfs func(int)
	dfs = func(cur int) {
		// 剪枝：temp 长度加上区间 [cur, n] 的长度小于 k，不可能构造出长度为 k 的 temp
		if len(temp) + (n - cur + 1) < k {
			return
		}
		// 记录合法的答案
		if len(temp) == k {
			comb := make([]int, k)
			copy(comb, temp) //采取拷贝一份是因为传入的是对地址的引用，不然后续操作就会更改
			ans = append(ans, comb)
			return
		}
		// 考虑选择当前位置
		temp = append(temp, cur)
		dfs(cur + 1)
		temp = temp[:len(temp)-1]
		// 考虑不选择当前位置
		dfs(cur + 1)
	}
	dfs(1)
	return
}
```

###### [46. 全排列](https://leetcode-cn.com/problems/permutations/)

```
给定一个不含重复数字的数组 nums ，返回其 所有可能的全排列 。你可以 按任意顺序 返回答案。

示例 1：
输入：nums = [1,2,3]
输出：[[1,2,3],[1,3,2],[2,1,3],[2,3,1],[3,1,2],[3,2,1]]
```

```go
func permute(nums []int) [][]int {
	res := [][]int{}
	visited := map[int]bool{}

	var dfs func(path []int)
	dfs = func(path []int) {
		if len(path) == len(nums) {
			temp := make([]int, len(path))
			copy(temp, path)
			res = append(res, temp)
			return
		}
		for _, n := range nums {
			if visited[n] {
				continue
			}
			path = append(path, n)
			visited[n] = true
			dfs(path)
			path = path[:len(path)-1]
			visited[n] = false
		}
	}

	dfs([]int{})
	return res
}
```

