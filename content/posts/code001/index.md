---
title: "两数之和 IV - 输入二叉搜索树"
date: 2025-05-16T10:25:11+08:00
description: ""
tags: [leetcode]
featured_image: ""
# images is optional, but needed for showing Twitter Card
images: []
categories:
comment: true 
---

原题:
给定一个二叉树、目标和K, 求任意两个数字之和是否等于 K,如果找到则返回 true;

## 思路
对于这种题目, 先收集所有的 value, 再使用暴力返回结果

难点可能是: 
1. 一开始想不到要收集
2. 会先想高性能的算法版本
3. 不知道如何遍历二叉树

## 收集
```java 
void collect(TreeNode root, List<Integer> values) {
    if( root == null ) return values;
    values.add(root.val);
    
    collect(root.left, values);
    collect(root.right, values);
}
```

## 暴力
```java 
for(int i = 0; i< values.size() - 1; i++) {
    for(int j = i; j < values.size(); j++) {
        if(values[i] + values[j] == k) {
            return true;
        }
    }
}

return false;

```



