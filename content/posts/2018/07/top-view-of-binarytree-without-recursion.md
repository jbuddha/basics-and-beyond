---
title: Top View of Binary Tree without Recursion
date: 2018-07-23T00:00:00.000Z
tags:
    - java
    - algorithms
    - data-structures
    - problem
    - interview-question
    - binary-tree
author: Reshma Beesetty
description: 'This article shows you how to print top view of a binary tree, which using recursion.'
lastmod: '2022-02-04T08:58:22.318Z'
---

<img src="/images/svgs/2016/top-view-of-binarytree.svg" alt="Top view of a binary tree"/>

If you have a binary tree and wants to get all the nodes that will be visible when seen from the top of the tree, how do you print all such nodes? Final output for this tree should be 7, 13, 23, 44, 51, 65. A similar problem about printing right view is given in the previous post about [Right View of Binary Tree without Recursion ]({{< ref "posts/2018/07/right-view-of-binarytree-without-recursion" >}})

<!--more-->

## The Solution

We can use a mechanism similar preorder traversal, but the solution is not as straight forward. When we are printing nodes on left subtree, ignoring all right nodes, we will visit each left node starting from root, however we can't print them straight away, as we need to print them in reverse order. Otherwise the output will be 44, 23, 13, 7. So, in order to print them in reverse order, we make use of auxiliary datastructure. Stack is what we need in this case.

We keep pushing left node onto stack until we reach a leaf node while visiting only the left nodes. Once we reach a leaf node, just pop all elements from the stack and print all of them. Once left subtree is complete, print the root node and continue with right subtree.

Right subtree is much simpler solution, as we have to print the right most nodes in the order we visit them. Begin with root and move to its right node, print the node and continue to its right node. Proceed till the right child becomes null. Find the program below written in java.


```java {title=true}
Program to print top view of a Binary Tree
```
```java {linenos=true}
public void topView(Node root) {
    Node temp = root.left;
    Stack<Node> stack = new Stack<>();

    while(temp != null) {
        stack.push(temp);
        temp = temp.left;
    }
    while (!stack.isEmpty())
        System.out.println(stack.pop().data);

    System.out.println(root.data);
    temp = root.right;
    while (temp != null) {
        System.out.println(temp.data);
        temp = temp.right;
    }
}
```


What do you think of this solution? Can you think of a simpler solution? Let me know in the comments.

---
SVG for BinaryTree diagram is generated by using [Binary Tree Visualizer](http://btv.melezinek.cz/binary-search-tree.html)
