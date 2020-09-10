# 从罗马数字转整数到各种数字转整数

leetcode刷到了罗马数字和整数互相转换的问题，再加上面试被问到了，干脆总结整理一下。读完本文可以解决如下两题。完了之后我们还的思考一下，如果面试官问你英文数字和整数转换，或者中文数字和整数转换，该怎么办呢。

[12. 整数转罗马数字](https://leetcode-cn.com/problems/integer-to-roman/)

[13. 罗马数字转整数](https://leetcode-cn.com/problems/roman-to-integer/)

## 先解leecode原题

我们要先分析一下罗马数字的表示规则，罗马数字中没有百千万这种量词的概念，每一个字符都表示一个数字(比如2000是MM而不是IIM)，而相邻的字符有些时候是相减的关系，有些时候是相加的关系。所以我们在转化的时候要考虑下一个字符是执行加法还是减法。当然也可以把所有减法情况列举出来，然后统一成加法。

然后我们考虑利用一个关系表将罗马数字和对应的整数一一对应起来，然后依次将其相加，最后得到结果。当然也要考虑减法的情况。对应关系如下所示：



我们可以利用贪心的思想，用上面这张表中右边的数字作为“加法因子”去分解一个整数，目的是“分解的整数个数”尽可能少。因此我们要先将关系表按整数大小降序排序，然后依次从中取最大的来减。

```python
    def intToRoman(self, num):
        r = ''
        L = [1000,900,500,400,100,90,50,40,10,9,5,4,1]
        Rama = ['M','CM','D','CD','C','XC','L','XL','X','IX','V','IV','I']
        for i in range(13):
            while num>=L[i]:
                r = r+Rama[i]
                num = num-L[i]
        return r
```


第二题难度还更小一些，同样我们构造一个字符和数字对应表，然后按照罗马数字的规则依次相加或者相减，最终得到结果。

一般情况数字都是从大到小的出现，这种情况是加法，但是一旦出现一个较小的字符出现在一个大字符的左边，会出现相减的情况，我们判断一下即可。
```python
class Solution:
    def romanToInt(self, s):
        Roman2Int = {'I':1,'V':5,'X':10,'L':50,'C':100,'D':500,'M':1000}
        Int = 0
        n = len(s)
        for index in range(n - 1):
            if Roman2Int[s[index]] < Roman2Int[s[index + 1]]:
                Int -= Roman2Int[s[index]]
            else:
                Int += Roman2Int[s[index]]
        return Int + Roman2Int[s[-1]]
```

## 头脑风暴

我们来整个头脑风暴，罗马数字解决了，如果改一下题目，问把一串英文数字转为整数。姑且按照标准的英语表达方式，不考虑大小写和单词拼写和语法错误，有什么思路吗?
```
比如输入：'thirteen million one hundred and forty thousand five hundred and twenty one'

输出：'13140521'
```
同样我们要先分析英文的表达方式，首先英文数字表示中可以分成三个部分：数词（如 one,two,thirteen等）、量词（hundred,thousand,million等）和连词and。首先我们可以发现连词没有用，遇到可以直接跳过。然后来分析量词和数词的关系,观察用例可以发现英文里面都是 **数词+量词** 这样的表示并且交替出现，他们之间是乘法的关系，相邻的两个数词量词对之间又是加法的关系。最后英文是从大到小说数字，我们从前向后遍历即可。

关系确定了，我们可以列出英文-数字对应表，然后开始转换就行啦。（**我下面写的是伪代码，不能运行**）

```python
def EngToNum(string):
	s = string.split(' ')
	D = {'one':1,'two':2,...,'hundred':100,'thousand':1000,'million':1000000,...}
	res = 0
	Num = 0
	for i in s:
		if i 是数词：
			Num += D[i]
		elif i 是量词:
			res += (Num * D[i])
			Num = 0
		else:		#如果是and
			continue
	res += Num		#最后加上十位和个位
	return res
```
英文的表述相对容易一点，它只涉及加法和乘法，而且数词量词交替出现。

如果再提升一下难度，用我们博大精深的汉语表示数字，要转成整数又该怎么办呢？假设不考虑汉字编码和其他细节，有什么思路吗？
```
比如输入:'一千三百一十四万零五百二十一'

输出：'13140521'
```
大眼一看似乎可以使用上面的方法继续套用，因为汉语表达中也只有乘法和加法，数词和量词交替出现这样的特点。但是仔细观察发现中文不是从大到小表示数字，比如用例中‘万’是最大的单位，但它出现在中间，处理起来很麻烦。

我一开始在这个地方纠结了很久，后来经高人指点，给我讲了一个绝妙的方法：我们按‘万’来分割字符串，那么前后两个部分都可以用一般的思路转换，最后再把‘万’乘上去。

```python
def ChinToNum(string):
	D = {'一':1,'二':2,....,'百':100,'千':1000}
	def helper(s):
		res = 0
		Num = 0
		for i in s:
			if i是数词：
				Num += D[i]
			else: 
				res+=(Num * D[i])
				Num = 0
		res += Num		#最后加个位
		return res
	
	L = string.split('万')
	return helper(L[0]) * 10000 + helper(L[1])
```

## 总结
转换的过程本身不难，难点在于分析不同数字的表达方式，并找到规律。

## 参考文章
[12. 整数转罗马数字 Leedcode题解](https://leetcode-cn.com/problems/integer-to-roman/solution/zheng-shu-zhuan-luo-ma-shu-zi-by-leetcode/)

[13. 罗马数字转整数 Leedcode题解](https://leetcode-cn.com/problems/roman-to-integer/solution/qing-xi-tu-jie-python3-by-ml-zimingmeng/)
