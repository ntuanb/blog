---
layout: post
title: Git Pre Push
author: tuan
categories: [tutorial]
image: /assets/images/2020/composer.jpg
tags: []
---

Adding a git pre push to force linting and unit tests before its push to remote when working with composer projects.

{% highlight json %}

...
"scripts": {
    "post-install-cmd": [
        "php -r \"copy('hooks/prepush', '.git/hooks/prepush');\""
    ]
},
...

{% endhighlight %}