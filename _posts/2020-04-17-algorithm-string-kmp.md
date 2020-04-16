---
layout: post
title: 字符串-KMP
tags:
- 字符串
categories: 算法
description: KMP
---

# 字符串-利用有限自动机进行字符串匹配

直接上代码, 代码里面有详细解释!

# 字符串-KMP算法

```java
public class Kmp {
    public static void main(String[] args) {
        Kmp kmp = new Kmp();

        String pattern = "ababaca";
        String string = "cdefaababacaasc";

        if (kmp.isMatch(string, pattern)) {
            System.out.println("匹配成功!");
        }
    }

    private boolean isMatch(String string, String pattern) {
        int[] prefixTable = this.genPrefixTable(pattern);
        // j表示已经匹配到的前缀个数, 因为prefixTable是是从1开始的
        int j = 0;
        for (int i = 0; i < string.length(); i++) {
            // 如果匹配不上, 则根据前缀表, 计算出j的值
            while (j > 0 && pattern.charAt(j) != string.charAt(i)) {
                j = prefixTable[j];
            }

            // 如果相等, 则模式向前移动
            if (string.charAt(i) == pattern.charAt(j)) {
                j++;
            }

            if (j == pattern.length()) {
                return true;
            }
        }
        return false;
    }

    private int[] genPrefixTable(String pattern) {
        int[] prefixTable = new int[pattern.length() + 1];
        // 当前最长公共前缀长度
        int k = 0;
        // i = 1, 表示第1个元素的公共前缀为0
        // 所以程序从i = 2即第2个元素开始
        for (int i = 2; i <= pattern.length(); i++) {
            // 用j表示, 方便程序阅读, 因为prefixTable的数组是从1开始的
            int j = i - 1;
            // 当k > 0的情况, 表示上一个(i - 1)位置有公共前缀
            while (k > 0 && pattern.charAt(k) != pattern.charAt(j)) {
                // 当k > 0时, 不能匹配时, 则需要对前缀表向前进行迭代:
                // 这里用到了一个推论 prefixTable[i] = 1 + max { prefixTable[i-1] 前缀的迭代}
                // 上面的迭代是前缀函数的迭代引理, 即迭归地迭代下去
                // 此时prefixTable[i - 1]即为k = prefixTable[k]
                // prefixTable[prefixTable[prefixTable[i-1]].....]
                k = prefixTable[k];
            }
            // k表示上一次的公共最长前缀, +1后表示此时的前缀
            if (pattern.charAt(k) == pattern.charAt(j)) {
                k = k + 1;
            }
            prefixTable[i] = k;
        }

        return prefixTable;
    }
}
```

