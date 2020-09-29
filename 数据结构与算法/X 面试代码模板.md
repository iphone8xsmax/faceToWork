[TOC]

### 面试代码模板

##### 1. LRU算法

```java
private static class LRUNode {
    String key;
    Object value;
    LRUNode next;
    LRUNode pre;

    public LRUNode(String key, Object value) {
        this.key = key;
        this.value = value;
    }
}
```

```java
public class LRUCache {

    Map<String, LRUNode> map = new HashMap<>();
    LRUNode head;
    LRUNode tail;
    // 缓存最⼤容量大于1
    int capacity;

    public LRUCache(int capacity) {
        this.capacity = capacity;
    }

    /**
	 * 插入元素
	 * @param key 键
	 * @param value 值
	 */
    public void put(String key, Object value) {
        // 说明缓存中没有任何元素
        if (head == null) {
            head = new LRUNode(key, value);
            tail = head;
            map.put(key, head);
        }
        // 说明缓存中已经存在这个元素了
        LRUNode node = map.get(key);
        if (node != null) {
            // 更新值
            node.value = value;
            // 把这个结点从链表删除并且插⼊到头结点
            removeAndInsert(node);
        } else {
            // 说明当前缓存不存在这个值
            LRUNode newHead = new LRUNode(key, value);
            // 此处溢出则需要删除最近最近未使用的节点
            if (map.size() >= capacity) {
                map.remove(tail.key);
                // 删除尾结点
                tail = tail.pre;
                tail.next = null;
            }
            map.put(key, newHead);
            // 将新的结点插入链表头部
            newHead.next = head;
            head.pre = newHead;
            head = newHead;
        }
    }

    /**
	 * 从缓存中取值
	 */
    public Object get(String key) {
        LRUNode node = map.get(key);
        // 如果有值则需要将这个结点放到链表头部
        if (node != null) {
            // 把这个节点删除并插⼊到头结点
            removeAndInsert(node);
            return node.value;
        }
        return null;
    }

    private void removeAndInsert(LRUNode node) {
        // 特殊情况先判断，例如该节点是头结点或是尾部节点
        if (node == head) {
            return;
        } else if (node == tail) {
            tail = node.pre;
            tail.next = null;
        } else {
            node.pre.next = node.next;
            node.next.pre = node.pre;
        }
        // 插⼊到头结点
        node.next = head;
        node.pre = null;
        head.pre = node;
        head = node;
    }
}
```

##### 2. 快排

```java
public static void quickSort(int[] array) {
    if (array == null || array.length < 2) return;
    quickSort(array, 0, array.length - 1);
}

private static void quickSort(int[] array, int left, int right) {
    if (left < right) {
        int pivot = partition(array, left, right);
        quickSort(array, left, pivot - 1);
        quickSort(array, pivot + 1, right);
    }
}

private static int partition(int[] array, int left, int right) {
    // 挑选一个随机的pivot索引并交换到第一个位置上
    int randomPivot = left + (int) (Math.random() * (right - left + 1));
    swap(array, randomPivot, left);
    // 数组第一个值为pivot值
    int pivotValue = array[left];
    int i = left + 1;
    int j = right;
    while (true) {
        while (i <= j && array[i] <= pivotValue) i++;
        while (i <= j && array[j] >= pivotValue) j--;
        // 数组越界退出
        if(i >= j) break;
        // 交换两个值
        swap(array, i, j);
    }
    // 交换第一个pivot元素和j位置
    swap(array, left, j);
    return j;
}
```

