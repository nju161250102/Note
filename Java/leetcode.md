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

