---
title: Breadth First Traversal In a Binary Tree Without Recursion
date: 2016-05-17
tags: ['java', 'algorithms', 'data-structures', 'problem', 'interview-question', 'binary-tree']
author: Buddha
description: Breadth first Traversal in a binary tree is a famous problem related to binary trees tree
---

<img src="/blog/assets/svgs/2016/breadth-first-traversal-2.svg" alt="Breadth first/Level order traversal of a binary tree"/>
<span class="caption">Breadth first/Level order traversal of a binary tree</span>

## The Problem

Imagine you have a binary tree where as shown above. You may be aware of InOrder traversal where you follow a scheme of visiting left subtree and then visit root node and finally visit right subtree. With small variations in order same is done in pre-order as well as post-order traversal. How do you do a breadth first traversal? It is slightly more tricky. Doing it non-recursively is even more difficult at first sight. Let me first explain what is breadthfirst traversal.

Different traversals produce different output as shown below

{% codeblock In-Order Traversal lang:java %}
10 15 20 25 28 30 32 35 45 50 55 60 62 65 70 75 80 90 95
{% endcodeblock %}

{% codeblock Post-Order Traversal lang:java %}
10 20 15 28 32 30 45 35 25 55 62 70 65 60 80 95 90 75 50
{% endcodeblock %}

{% codeblock Pre-Order Traversal lang:java %}
50 25 15 10 20 35 30 28 32 45 75 60 55 65 62 70 90 80 95
{% endcodeblock %}

{% codeblock Level-Order Traversal lang:java %}
50 25 75 15 35 60 90 10 20 30 45 55 65 80 95 28 32 62 70
{% endcodeblock %}


<!-- more -->
Trick to solve any binary tree problem without Recursion is to use an auxiliary datastructure like Queue or Stack. For this problem, we shall use Queue as shown below code snippet.

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
