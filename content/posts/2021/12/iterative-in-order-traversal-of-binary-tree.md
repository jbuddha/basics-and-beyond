---
title: In Order Traversal Of a Binary Tree Without Recursion
date: 2021-12-26
tags: ['java', 'algorithms', 'data-structures', 'problem', 'interview-question', 'binary-tree']
author: Buddha
description: Algorithm and Solution for Iteratively traversing a binary tree in-order
draft: true
---

<img src="/blog/assets/svgs/2021/12/in-order-traversal-new.svg" alt="Animation to visualise in order traversal of a binary tree"/>
<span class="caption">In order traversal of a binary tree</span>

## The Problem

Imagine you have a binary tree as shown above, and you are given a task of traversing it in-order. Which simply means inorder to visit a tree, you process left sub-tree and then process root and then process right sub-tree. In a binary search tree, traversing in-order results in processing all the nodes in ascending order.

{% codeblock In-Order Traversal lang:java %}
10 15 20 25 28 30 32 35 45 50 55 60 62 65 70 75 80 90 95
{% endcodeblock %}

{% codeblock Post-Order Traversal lang:java %}
10 20 15 28 32 30 45 35 25 55 62 70 65 60 80 95 90 75 50
{% endcodeblock %}

<!-- more -->

Trick to solve any binary tree problem without Recursion.... is to use an auxiliary data-structure like Queue or Stack. For this problem, we shall use Stack to replicate a recursive function. 

Logic is actually pretty simple, at every level after the root node, add left and right elements to the queue and repeat the process for child levels. As Queue, will give us the elements in FIFO order, when we pop the elements, we will be getting them in the order they are added, which is breadth wise.

{% codeblock lang:java %}
public void levelOrderTraversal(BinaryTree<Integer> tree) {
  Queue<BinaryTreeNode<Integer>> q = new LinkedBlockingQueue<>();
  q.add((tree.getRootNode()));
  while(true) {
    BinaryTreeNode<Integer> temp = q.poll();

    if(temp == null)
      break;

    System.out.println(temp.getValue());

    if(temp.left != null)
      q.add(temp.left);
    if(temp.right != null)
      q.add(temp.right);
  }
}
{% endcodeblock %}

What do you think of this solution? Can you think of a simpler solution? Let me know in the comments.
