# 快排解Top-K问题

Top-k问题一直都是特别重要也是面试很常考的题型（听别人说的，其实我一次也没碰到）。我决定研究top-k问题，总结出来一套使用快速排序思路解决Top-k问题的套路，利用快速排序的思想可以将时间复杂度降低，最好状态时间复杂度O(n)空间O（logn）（参照算法导论，其实我根本没看过），相对来说是很快的啦。阅读本文可以解决一下几道题：

[215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)

[347. 前K个高频元素](https://leetcode-cn.com/problems/top-k-frequent-elements/)

[973. 最接近原点的K个点](https://leetcode-cn.com/problems/k-closest-points-to-origin/)

## 快速排序

快速排序是真的**非常重要**，写法也有好几种，但是因为太重要，至少要会一种，背也得背下来。我分析一个我自己看着比较舒服的写法。最关键的是它快排的思想来源于分治，除了排序之外还能干很多事。

这里先奉上一个快排的代码，后面我们根据这个改成做题的模版。本身快排可以一个函数写完，但是为了方便后面理解我分成了两个，重点在于第一个`partition()`函数,可以将一个元素定位到排序后的位置上。

```python
	def partition(nums,left,right):
		temp = left
		x = left+1
		y = right
		while(True):
			while(x<=y and nums[x]<nums[temp]):
				x+=1
			while(x<=y and nums[y]>nums[temp]):
				y-=1
			if x>y:
				break
			nums[x],nums[y] = nums[y],nums[x]
			x+=1
			y-=1
		nums[temp],nums[y] = nums[y],nums[temp]
		return y

	def Sort(nums,l,r):
		if l<r:
			i = partition(nums,l,r)
			Sort(nums,l,i-1)
			Sort(nums,i+1,r)
```
## 解题模版
现在有了定位一个元素的方法，我们不需要完整排序一个数组，而是只需找到排序后数组的第k个元素即可。那么我们可以考虑使用二分的方法进一步优化。接下来奉上模版。我注释的地方为实际问题中需要改动的地方。
```python
	def partition(nums,left,right):
		temp = left
		x = left+1
		y = right
		while(True):
			while(x<=y and nums[x]<nums[temp]):#下标x的元素<下标temp的元素大小
				x+=1
			while(x<=y and nums[y]>nums[temp]):#下标y的元素>下标temp的元素大小
				y-=1
			if x>y:
				break
			nums[x],nums[y] = nums[y],nums[x]	#交换下标x的元素和下标y的元素
			x+=1
			y-=1
		nums[temp],nums[y] = nums[y],nums[temp]##交换下标temp的元素和下标y的元素
		return y
	
	def findTopk(nums,k):
		'''
		边界判断和数组的预处理
		'''
		left = 0
		right = n-1
		target = n-k	#如果是找第k大target = n-k，如果是第k小target = k-1
		while(True):
			index = self.partition(nums,left,right)
			if index == target:	#找到了
				#根据问题要求返回结果
			elif index<target:	#如果在右边就向右找
				left = index+1
			else:				#如果在左边就向左找
				right = index-1
```	
## 开始解题
接下来我们开始套模版
### 215. 数组中的第K个最大元素
这道题目标是找第k大元素，而且要找的数组是已经给好的，那么我们只需定位到这个元素下标然后返回该元素即可。上代码：
```python
		def partition(self,nums,left,right):
			temp = left
			x = left+1
			y = right
			while(True):
				while(x<=y and nums[x]<nums[temp]):
					x+=1
				while(x<=y and nums[y]>nums[temp]):
					y-=1
				if x>y:
					break
				nums[x],nums[y] = nums[y],nums[x]
				x+=1
				y-=1
			nums[temp],nums[y] = nums[y],nums[temp]
			return y

		def findKthLargest(self, nums, k):
			n = len(nums)
			if n<=0:
				return 0
			left = 0
			right = n-1
			target = n-k	#找第k大选择 target = n-k
			while(True):
				index = self.partition(nums,left,right)
				if index == target:
					return nums[index]	#直接返回对应的元素
				elif index<target:
					left = index+1
				else:
					right = index-1
```
### 347. 前K个高频元素
这也是一个前k大的问题，不过人家要把前k个都输出来。而且人家没有直接给你要排序的数组，人家问的是频率，那么就需要我们自己构造这个数组。

不过有了模版就很简单了，我们可以直接用哈希表把频率统计出来，然后构造一个二维的数组，按照频率找top-k，最后输出元素的值即可。
```python
	def partition(self,nums,left,right):
		temp = left
		x = left+1
		y = right
		while(True):
			while(x<=y and nums[x][1]<nums[temp][1]):	#这里改成用频率判断
				x+=1
			while(x<=y and nums[y][1]>nums[temp][1]):	#这里改成用频率判断
				y-=1        
			if x>y:
				break
			nums[x],nums[y] = nums[y],nums[x]
			x+=1
			y-=1     
		nums[temp],nums[y] = nums[y],nums[temp]
		return y
	def topKFrequent(self, nums, k):
		D = collections.Counter(nums)
		L = []
		for i in D.keys():
			L.append((i,D[i]))
		#以上4行为我们自己构造的数组L，其中存放元素和频率的二元组：（元素，频率）
		n = len(L)
		if n<=0:
			return []
		left = 0
		right = n-1
		target = n-k	#找第k大选择 target = n-k
		while(True):
			index = self.partition(L,left,right)
			if index == target:
				return [t[0] for t in L[index:]]	#返回下标从k到n-1的元素
			elif index<target:
				left = index+1
			else:
				right = index-1
```
### 973. 最接近原点的K个点
这道题的不同点在于要求的是前k小，所以定义target的时候要修改一下，其他地方都一样。
```python
	def partition(self,nums,left,right):
		temp = left
		x = left+1
		y = right
		while(True):
			while(x<=y and nums[x][2]<nums[temp][2]):	#这里改成用距离判断
				x+=1
			while(x<=y and nums[y][2]>nums[temp][2]):	#这里改成用距离判断
				y-=1        
			if x>y:
				break
			nums[x],nums[y] = nums[y],nums[x]
			x+=1
			y-=1     
		nums[temp],nums[y] = nums[y],nums[temp]
		return y
	def kClosest(self, points, K):
		L= []
		for i in points:
			L.append([i[0],i[1],i[0]**2+i[1]**2])
		n = len(L)
		if n<=0:
			return []
		left = 0
		right = n-1
		target = K-1	#找第k小选择 target = k-1
		while(True):
			index = self.partition(L,left,right)
			if index == target:
				return [[t[0],t[1]] for t in L[:K]]	#返回对应的前k个点
			elif index<target:
				left = index+1
			else:
				right = index-1
```
## 最后
快排的模版就介绍完啦。Top-k的问题有很多种解法，我这里只是总结一种解题思路，不能保证复杂度的最优，给刚开始学的小伙伴开阔一下视野，有什么不完善的地方请大佬开喷。

最后我发现leetcode上还有一道top-k问题：692. 前K个高频单词。这道题好像不能直接套用我们的模版，似乎用堆的方法更好，我去研究研究再写。
