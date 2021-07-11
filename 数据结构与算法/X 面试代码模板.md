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



