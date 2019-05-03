---
layout: post
title: Quick Script To Rename Files
author: tuan
categories: [tutorial, software]
image: assets/images/2018/loop.jpg
---

For this particular function, I wanted quick results and didn't care about doing it 'properly'.

I wanted to loop through a list of directories to rename all pictures with there respective index in the folder and figured there were 2 ways that I would do this, by using either Node or Bash. 

I figured that since I was comfortable using Node and would have no trouble in producing a solution, bash would be the preferred method here.

Result from the first try:

{% highlight bash %}

#!/bin/bash

galleries=../static/img/gallery

find $galleries -mindepth 1 -maxdepth 1 -type d | 
    cat -n |
        while read n gallery;
            do find "$gallery" -mindepth 1 -maxdepth 1 -not -type d |
                cat -n |
                while read n image; 
                    do mv "$image" "$gallery/$n.jpg" ;
                done
        done

{% endhighlight %}

`mindepth` removes the `.` and `..` from appearing.

Is there a better solution?

For now, this solution works as it does exactly what I want so I am happy with the outcome.
