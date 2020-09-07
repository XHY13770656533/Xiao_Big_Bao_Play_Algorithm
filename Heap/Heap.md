# 二叉堆详解

我刚开始学数据结构的时候，二叉堆是我觉得最恶心的结构了。看着像树不是树，上浮下沉好头大。最后看了labuladong大佬的文章，终于好像有点明白了。写篇文章记录一下。

堆这个数据结构在实际问题中很常用，最直接的就是堆排序和优先队列。虽然很多语音提供堆实现的库函数直接调用即可，但它作为一种基础数据结构还是应该掌握原理和实现。

## 开始建一个堆

堆的本质是一颗完全二叉树，满足要求所有节点都大于等于或小于等于它的两个子节点（大顶堆或小顶堆）。这一步不难理解，接下来，在实际情况中我们会把这个堆放在一个数组里面，可能大家之前了解过用数组存放完全二叉树的方法，这边是一模一样的。这种方法可以让我们利用数组的索引快速的算出子节点和父节点。

比如在数组arr中，我们将树的根放在arr[1]的位置，**注意这里的索引是1，不是0。0的位置暂时不用，我们的堆从1开始。**然后对于一个节点arr[n]，它的左右孩子分别是arr[2\*n]和arr[2\*n+1]。那么我们可以有以下计算节点索引的方法

```python
#父节点索引
def parent(root):		#root->int
	return root // 2
#左孩子索引
def left(root):			#root->int
	return root * 2
#右孩子索引
def right(root):		#root->int
	return root * 2 + 1
```

这样堆的存储方式就基本讲清楚了，如果还是不太懂，看我盗的这个图(摘自：http://labuladong.gitbook.io/algo/shu-ju-jie-gou-xi-lie/er-cha-dui-xiang-jie-shi-xian-you-xian-ji-dui-lie)

![1](https://github.com/XHY13770656533/Xiao_Big_Bao_Play_Algorithm/blob/master/Heap/heap1.png "1")

## 我们来插入和删除 
然后要说最让人头疼的地方，堆的上浮和下沉操作。我在看数据结构书的时候一直搞不懂他们的差别，后来觉得应该先看作用，再讲实现，由浅入深的来看这个问题也许会好一点。

那就从作用开始讲。这两个操作都是为了当堆结构被破坏时将其调整为正常结构，那什么样的操作会破坏堆结构呢？只有两种操作，插入元素和删除元素。

这个时候又要强调一下堆的特点了，**一般来讲，在堆中插入元素只能插在堆底，而删除元素只能删堆顶元素**。先用大顶堆作为例子，我们可以看一下代码，然后大家可能就可以了解为什么需要两种调整堆的方法了。

```python
	#### 插入操作 ####
def insert(key):
	#堆大小加1
	N+=1
	#把新元素加到最后
	heap[N] = key
	#把他上浮到正确位置
	swim(N)
	
	#### 删除操作 ####
def delTop():
	#对顶元素为最大或最小元素
	res = heap[1]
	#把这个元素交换到最后,删除之，堆的大小减一
	heap[1] , heap[N] = heap[N] , heap[1]
	heap[N] = None
	N-=1
	#把现在位于索引1的元素下沉到正确位置
	sink(1)
	return res
```

## 重点难点：上浮和下沉
接下来我们就可以看看具体上浮和下沉操作是如何实现的，

对于下沉操作，节点 A 比它的子节点（中的一个）小，那么 A 就不配做父节点，应该下去，下面那个更大的节点上来做父节点，即把 A 和那个大的子节点互换位置。重复该操作直到 A 到达正确位置。

对于上浮操作，节点 A 比它的父节点大，那么 A 不应该做子节点，应该把父节点换下来，自己去做父节点，即节点 A 和他的父节点互换。重复该操作直到 A 到达正确位置。

两者看似等价，差别在于上浮用于插入操作中，下沉用于删除操作中。

```Python
#### 上浮 ####
def swim(k):		# k->int
	# 最多浮到浮到堆顶
	while(k>1 and heap[parent(k)]<k):
		#如果第k元素比他父节点大，就把他和他的父节点交换
		heap[parent(k)] , heap[k] = heap[k] , heap[parent(k)]
		k = parent(k)

#### 下沉 ####
def sink(k):		# k->int
	# 最多沉到堆底
	while(left(k)<=N):
		# 与两个孩子节点比较，如果大于两个孩子则不需要下沉，否则和较大的孩子交换
		# 先假设左孩子比较大
		older = left(k)
		# 然后判断是否存在右孩子，比较一下大小
		if right(k)<=N and heap[older] < heap[right(k)]:
			older = right(k)
		# 如果比两个都大，直接结束
		if heap[older] < heap[k]:
			break
		heap[k] , heap[order] = heap[order] , heap[k]
		k = order
```
## 完整实现
整个二叉堆内部的实现代码就是这些了，主要复杂的部分就是上浮和下沉操作，核心部分代码也就几行。感觉也没有那么难嘛。最后我试试把细节都加上，写一个完整的二叉堆代码，感兴趣的可以参考一下

```Python
class Heap:
    def __init__(self,K):
        # K->int  表示堆中最大元素数量
        self.heap = [None] * (K+1)
        self.MaxSize = K
        self.N = 0
        
     #父节点索引
    def parent(self,root):		#root->int
        return root // 2
    #左孩子索引
    def left(self,root):		#root->int
        return root * 2
    #右孩子索引
    def right(self,root):		#root->int
        return root * 2 + 1   
    
    def swim(self,k):   # k->int
        # 最多浮到浮到堆顶
        while(k>1 and self.heap[self.parent(k)]<self.heap[k]):
            #如果第k元素比他父节点大，就把他和他的父节点交换
            self.heap[self.parent(k)] , self.heap[k] = self.heap[k] , self.heap[self.parent(k)]
            k = self.parent(k)
    
    def sink(self,k):   # k->int
        # 最多沉到堆底
        while(self.left(k)<=self.N):
            # 与两个孩子节点比较，如果大于两个孩子则不需要下沉，否则和较大的孩子交换
            # 先假设左孩子比较大
            older = self.left(k)
            # 然后判断是否存在右孩子，比较一下大小
            if self.right(k)<=self.N and self.heap[older] < self.heap[self.right(k)]:
                older = self.right(k)
            # 如果比两个都大，直接结束
            if self.heap[older] < self.heap[k]:
                break
            self.heap[k] , self.heap[older] = self.heap[older] , self.heap[k]
            k = older
    
    def insert(self,key):
        # Key->int  key表示插入的值，搞简单一点就用int好了
        if self.N<self.MaxSize:
            self.N+=1
            #把新元素加到最后
            self.heap[self.N] = key
            #把他上浮到正确位置
            self.swim(self.N)
            return True
        else:
            return False
    
    def delTop(self):
        if self.N>0:
            #对顶元素为最大或最小元素
            res = self.heap[1]
            #把这个元素交换到最后,删除之，堆的大小减一
            self.heap[1] , self.heap[self.N] = self.heap[self.N] , self.heap[1]
            self.heap[self.N] = None
            self.N-=1
            #把现在位于索引1的元素下沉到正确位置
            self.sink(1)
            return res
        else:
            return -99999
```

## 总结

前面思路整理了那么多，但最后完整实现出来也费了不少劲，这个算法还是建议大家自己实现一遍比较好。

堆的本质是一个完全二叉树，所以用数组来存放。

可能上浮和下沉操作会有一点绕，多看几遍起始不难。上浮对应插入元素，下沉对应删除堆顶元素。

## 参考文章

[labuladong算法小抄：二叉堆详解实现优先队列](http://labuladong.gitbook.io/algo/gao-pin-mian-shi-xi-lie/pan-duan-hui-wen-lian-biao)

