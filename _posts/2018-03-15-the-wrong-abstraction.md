---
layout: post
title: The Wrong Abstraction
author: tuan
categories: [motivation, software]
image: assets/images/2018/abstraction.jpg
tags: [featured]
---

> If you find yourself passing parameters and adding conditional paths through shared code, the abstraction is incorrect.

[The Wrong Abstraction by Sandi Metz](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction).

### Problem At Hand

Solving the problem should be the main priority. 

I believe that sometimes, it is okay to write 'messy' code. Especially when it comes to prototyping as this a conceptual phase, where we are testing whether its acheivable. But keep in mind if its done this way, the codebase must be thrown out for a rewrite at some point to a proper structure with proper abstraction.

### Code Duplication

Code duplication is okay when complexity for a part of the codebase is higher than it should be. This can usually be seen when there are too many conditionals within a block of code. 

In this case, seperating the section that we want to change to seperate blocks is the best approach, because the new section is now scene as a seperate piece of logic.
