---
title: Breadth First Traversal In a Binary Tree Without Recursion
date: 2016-05-17T00:00:00.000Z
tags:
  - java
  - algorithms
  - data-structures
  - problem
  - interview-question
  - binary-tree
author: Buddha
thumbnail: /images/svgs/2016/breadth-first-traversal-2.svg
description: Breadth first Traversal in a binary tree is a famous problem related to binary trees tree
lastmod: '2022-02-02T19:19:32.895Z'
---

## The Problem

Imagine you have a binary tree where as shown above. You may be aware of InOrder traversal where you follow a scheme of visiting left subtree and then visit root node and finally visit right subtree. With small variations in order same is done in pre-order as well as post-order traversal. How do you do a breadth first traversal? It is slightly more tricky. Doing it non-recursively is even more difficult at first sight. Let me first explain what is breadthfirst traversal. You start at root and process it and then you process both children of the root from left to right or right to left. Then you visit all the children of both the nodes you just visited and continue to do till all the levels are finished.

<!--more-->
{{< figure src="/images/svgs/2016/breadth-first-traversal-2.svg" caption="Visualization of Breadth first/Level order traversal of a binary tree" >}}


Different traversals produce different output as shown below

```md {title=true}
In-Order Traversal
```
```md {linenos=false}
10 15 20 25 28 30 32 35 45 50 55 60 62 65 70 75 80 90 95
```

```md {title=true}
Post-Order Traversal
```
```md {linenos=false}
10 20 15 28 32 30 45 35 25 55 62 70 65 60 80 95 90 75 50
```

```md {title=true}
Pre-Order Traversal
```
```md {linenos=false}
50 25 15 10 20 35 30 28 32 45 75 60 55 65 62 70 90 80 95
```

```md {title=true}
Level-Order Traversal
```
```md {linenos=false}
50 25 75 15 35 60 90 10 20 30 45 55 65 80 95 28 32 62 70
```

Trick to solve any binary tree problem without Recursion is to use an auxiliary datastructure like Queue or Stack. For this problem, we shall use Queue as shown below code snippet.

Logic is actually pretty simple, at every level after the root node, add left and right elements to the queue and repeat the process for child levels. As Queue, will give us the elements in FIFO order, when we pop the elements, we will be getting them in the order they are added, which is breadth wise.

```java
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
```

What do you think of this solution? Can you think of a simpler solution? Let me know in the comments.
