---
layout: post
title: 字符串-利用有限自动机进行字符串匹配
tags:
- 字符串
categories: 算法
description: 利用有限自动机进行字符串匹配
---

# 字符串-利用有限自动机进行字符串匹配

```java
public class FiniteAutoMachine {
    public static void main(String[] args) {
        FiniteAutoMachine finiteAutoMachine = new FiniteAutoMachine();

        // 随便一个模式串
        String pattern = "abcda";
        Set<Character> finiteChars = new HashSet<>();
        for (char c : pattern.toCharArray()) {
            finiteChars.add(c);
        }
        // 创建状态机
        Map<Integer, Map<Character, Integer>> stateMachine = finiteAutoMachine.createStateMachine(pattern, finiteChars);

        // 测试字符串
        String string1 = "efaabcda";

        if (finiteAutoMachine.isMatch(string1, pattern, stateMachine, finiteChars)) {
            System.out.println("匹配成功");
        } else {
            System.out.println("匹配失败");
        }
    }

    private boolean isMatch(String string, String pattern, Map<Integer, Map<Character, Integer>> stateMachine,  Set<Character> finiteChars) {
        int state = 0;
        for (char c : string.toCharArray()) {
            if (finiteChars.contains(c)) {
                state = stateMachine.get(state).get(c);
                if (state == pattern.length()) {
                    return true;
                }
            } else {
                state = 0;
            }
        }
        return false;
    }

    private Map<Integer, Map<Character, Integer>> createStateMachine(String pattern, Set<Character> finiteChars) {

        Map<Integer, Map<Character, Integer>> map = new HashMap<>();
        int m = pattern.length();
        // q表示状态, 0为初始状态, 没有任何字符, 所以最大状态为m
        for (int q = 0; q < m; q++) {
            // 在状态q的情况下, 在接受一个字符c后, 进入的下个状态
            // 按最长后缀开始匹配, 匹配到就终止
            for (char c : finiteChars) {
                // (上一个状态 + c) 后的字符
                String pqc = (q > 0 ? pattern.substring(0, q) : "") + c;
                // 下一个状态: 初始为q + 1, 逐渐递减, 初始就匹配上就表示能够进入下一个状态, 否则就表示需要回到初始状态或者初始与当前状态之间
                int k = Math.min(q + 1, m);
                while (k > 0) {
                    String pk = pattern.substring(0, k);
                    if (pqc.endsWith(pk)) {
                        break;
                    } else {
                        k--;
                    }
                }

                Map<Character, Integer> charToStateMap;
                if (map.containsKey(q)) {
                    // 在当前状态q下, 输入字符c后进入的状态k
                    charToStateMap = map.get(q);
                } else {
                    charToStateMap = new HashMap<>();
                }
                charToStateMap.put(c, k);
                map.put(q, charToStateMap);
            }
        }

        return map;
    }

}
```

