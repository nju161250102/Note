# leetcode 分类

[前300题分类参考](https://cspiration.com/leetcodeClassification#103)

### Array

#### 基础

>  27 Remove Element
>
> 从数组中移除元素，无须保证顺序，返回剩余元素长度

```python
def removeElement(self, nums, val):
    """
    :type nums: List[int]
    :type val: int
    :rtype: int
    将需要删除的元素通过交换位置全部移至数组末尾
    """
    i = 0
    end = len(nums) - 1
    # 末尾向前跳过需要删除的元素，注意不能越界
    while end >= 0 and nums[end] == val:
        end -= 1
    # 向末尾遍历
    while i < end:
        if nums[i] == val:
            nums[i], nums[end] = nums[end], nums[i]  # 交换元素
            # 交换后向前跳过需要删除的元素
            while nums[end] == val:
                end -= 1
        i += 1
    return end + 1
```

> 26 Remove Duplicates from Sorted Array
>
> 从有序数组中移除重复元素

```python
def removeDuplicates(self, nums):
    """
    :type nums: List[int]
    :rtype: int
    """
    if len(nums) <= 1:
       return len(nums)
    p = 0
    for i in range(1, len(nums)):
        # 元素变化时记录元素，通过p指示位置
        if nums[i] != nums[i-1]:
            p += 1
            nums[p] = nums[i]
    return p + 1
```

> 80 Remove Duplicates from Sorted Array II
>
> 从有序数组中移除重复元素，最多保留两个重复元素

```python
def removeDuplicates(self, nums):
    """
    :type nums: List[int]
    :rtype: int
    """
    if len(nums) < 3:
        return len(nums)
    p = 0
    begin = 0  # 使用begin记录当前重复元素开始处
    for i in range(1, len(nums)):
        if nums[i] != nums[i-1]:
            if i - begin > 1:
                # 重复了两次及以上
                nums[p] = nums[p+1] = nums[i-1]
                p += 2
            else:
                nums[p] = nums[i-1]
                p += 1
            begin = i
    # 最后再判断一次
    if len(nums) - begin > 1:
        nums[p] = nums[p+1] = nums[-1]
        p += 2
    else:
        nums[p] = nums[-1]
        p += 1
    return p
```

> 189 Rotate Array
>
> 旋转数组，本质是移动数组元素

```python
def rotate(self, nums, k):
    """
    :type nums: List[int]
    :type k: int
    :rtype: None Do not return anything, modify nums in-place instead.
    """
    if len(nums) < 2:
        return 
    k = k % len(nums)
    nums[:] = nums[-k:] + nums[:-k]
```

> 299 Bulls and Cows
>
> 使用map或array记录数字出现次数
>
> collections.defaultdict() 可以指定键缺失的默认值

```python
def getHint(self, secret, guess):
    """
    :type secret: str
    :type guess: str
    :rtype: str
    """
    a = 0
    b = 0
    s_nums = collections.defaultdict(lambda: 0)  # 默认值是0
    g_nums = collections.defaultdict(lambda: 0)
    for i in range(len(secret)):
        if secret[i] == guess[i]:
            a += 1
        s_nums[secret[i]] += 1
        g_nums[guess[i]] += 1
    for i in g_nums:
        b += min(s_nums[i], g_nums[i])
    return "%dA%dB" % (a, b-a)
```

> 41 First Missing Positive
>
> 找到第一个缺失的正数，先排序后遍历

```python
def firstMissingPositive(self, nums):
    """
    :type nums: List[int]
    :rtype: int
    """
    nums.sort()
    n = 0
    for k in nums:
        if k > 0:
            if k-n == 1:
                n = k
            elif k-n > 1:  # 提前结束遍历
                return n+1
    return n+1
```

> 134 Gas Station
>
> 重设起点，遍历一遍

```python
def canCompleteCircuit(self, gas, cost):
    """
    :type gas: List[int]
    :type cost: List[int]
    :rtype: int
    """
    left, lack = 0,0
    start = 0

    for i in range(len(gas)):
        left += gas[i] - cost[i]
        # 剩余汽油不足时将当前站点设为出发点
        if left < 0:
            start = i+1
            lack += left
            left = 0
    # 最后检查到终点时剩余汽油能否回到起点
    if left + lack >= 0:
        return start
    else:
        return -1
```

> 274 H-Index
>
> 先排序，再遍历

```python
def hIndex(self, citations):
    """
    :type citations: List[int]
    :rtype: int
    """
    h = len(citations)
    citations.sort()
    for i in range(len(citations)):
        if citations[i] < h:
            h -= 1
    return h
```

