---
layout: post
title: Abstraction
---

> If you find yourself passing parameters and adding conditional paths through shared code, the abstraction is incorrect.

[The Wrong Abstraction by Sandi Metz](https://www.sandimetz.com/blog/2016/1/20/the-wrong-abstraction).

Solving the problem should be the main priority. 

Sometimes it is okay to write 'messy' code, especially when it comes to prototyping, but keep in mind if its done that way, the codebase must be thrown out for a rewrite at the very end to properly abstract the logic. 

In addition, a better understanding is formed after the prototyping phase, which can allow better abstraction.

Code duplication is okay when complexity for a section is higher than it should be.
