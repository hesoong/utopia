---
title: "Leetcode之探索学习"
date: 2023-08-24T17:22:31+08:00
draft: false
toc: true
images:
tags:
  - concept
---
## Binary Tree(二叉树)
---
### Introduction
*A `tree` is a frequently-used data structure to simulate a hierarchical tree structure.*

树是经常使用的数据结构，以模拟分层树结构。

*Each node of the tree will have a root value and a list of references to other nodes which are called child nodes. From graph view, a tree can also be defined as a directed acyclic graph which has `N nodes` and `N-1 edges`.*

树的每个节点都有一个根值和对其他节点(称为子节点)的引用列表。从图形的角度来看，一棵树也可以定义为一个有 `N` 个节点和 `N-1`条边的有向无环图。

*A `Binary Tree` is one of the most typical tree structure. As the name suggests, a binary tree is a tree data structure in which each node has `at most two children`, which are referred to as the left child and the right child.*

***二叉树***是最典型的树结构之一。顾名思义，二叉树是一种树型数据结构，其中每个节点***最多有两个***子节点，即左子节点和右子节点。

### Traverse a Tree
#### Pre-order Traversal - 前序

---
Pre-order traversal is to visit the root first. Then traverse the left subtree. Finally, traverse the right subtree.

预序遍历是先访问根，然后遍历左侧子树，最后遍历右侧子树。
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */

func preorderTraversal(root *TreeNode) []int {
    res := []int{}
    if(root == nil){
        return res
    }
    res = append(res, root.Val)
    if(root.Left != nil){
        res = append(res, preorderTraversal(root.Left)...)
    }
    if(root.Right != nil){
        res = append(res, preorderTraversal(root.Right)...)
    }
    return res
}
```
#### In-order Traversal - 中序

---

In-order traversal is to traverse the left subtree first. Then visit the root. Finally, traverse the right subtree.

顺序遍历是先遍历左边的子树，然后访问根，最后遍历右边的子树。
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func inorderTraversal(root *TreeNode) []int {
    res := []int{}
    if(root == nil){
        return res
    }
    if(root.Left != nil){
        res = append(res, inorderTraversal(root.Left)...)
    }
    res = append(res, root.Val)
    if(root.Right != nil){
        res = append(res, inorderTraversal(root.Right)...)
    }
    return res
}
```
#### Post-order Traversal - 后序

---

Post-order traversal is to traverse the left subtree first. Then traverse the right subtree. Finally, visit the root.

后序遍历是先遍历左侧子树，然后遍历右侧子树，最后访问根。
```go
/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
func postorderTraversal(root *TreeNode) []int {
    res := []int{}
    if(root == nil){
        return res
    }
    if(root.Left != nil){
        res = append(res, postorderTraversal(root.Left)...)
    }
    if(root.Right != nil){
        res = append(res, postorderTraversal(root.Right)...)
    }
    return append(res, root.Val)
}
```
#### Recursive or Iterative

---
递归和迭代
