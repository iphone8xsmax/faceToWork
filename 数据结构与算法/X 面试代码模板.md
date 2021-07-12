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
func bsearch(start int, end int, nums[]int, target int) int{
    if start > end {
        return -1
    }
    mid := (start + end) / 2
    if nums[mid] == target {
        return mid
    } 
    if nums[mid] < target {
        return bsearch(mid + 1, end, nums, target)
    } else {
        return bsearch(start, mid -1 , nums, target)
    }

}
func search(nums []int, target int) int {
    return bsearch(0, len(nums)-1, nums, target)
}
```

#### [278. 第一个错误的版本](https://leetcode-cn.com/problems/first-bad-version/)

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

