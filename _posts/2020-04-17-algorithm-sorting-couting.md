---
layout: post
title: 排序-计数排序
tags:
- 计数排序
categories: 算法
description: 计数排序
---

# 计数排序

计数排序应该算是桶排序的一种: 

- 把n个待排序的区间在[0...k]之间数字, 放到k个桶里面, 
- 每放一个桶, 桶里里面的数字就加1
- 然后我们可以知道每个数字在每个桶里面出现的次数，然后根据这个来得到结果.

```java
public class CountingSort {
    public static void main(String[] args) {
        CountingSort countingSort = new CountingSort();
        // 范围[0, 9]的数字10个
        int[] nums = {2, 3, 5, 2, 0, 7, 9, 0, 3, 5};
        // 10表示桶为[0 - 9]
        int[] result = countingSort.sort(nums, 10);
        // 输出
        Arrays.stream(result).forEach(System.out::print);
        // 排序后结果: 0022335579
    }

    private int[] sort(int[] nums, int k) {
        int n = nums.length;
        // 桶数
        int[] buckets = new int[k];
        // 每个桶中保存nums中元素的个数
        for (int i = 0; i < n; i++) {
            buckets[nums[i]] += 1;
        }
        // 对桶中的元素进行处理
        // 比如 buckets[0] = 1; buckets[1] = 2;
        // 则处理后 buckets[1] = 3, 这个3表示值为1这个数排在第3位
        for (int i = 1; i < buckets.length; i++) {
            buckets[i] = buckets[i] + buckets[i - 1];
        }

        int[] result = new int[n];
        for (int i = n - 1; i >= 0; i--) {
            // 获取此元素应该排在的位置
            int position = buckets[nums[i]];            
            result[position - 1] = nums[i];
            // 桶中元素位置向左移一位, 表示当前position位置已经被占了,值相同的元素排在前面
            // 从这里可以看出此排序是稳定的
            buckets[nums[i]] -= 1;
        }

        return result;
    }
}
```

