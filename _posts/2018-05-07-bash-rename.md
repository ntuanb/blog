---
layout: post
title: Assimilation
---

For quicker side project reasons, I wanted to loop through a list of directories to rename all pictures with there respective index in the folder. 

I figured there were 2 ways to that I would do this by using either Node or Bash. 

I figured that since I was comfortable using Node and would have no trouble figuring the solution, I would try out bash.

Here is the result from the first try:

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

Is there a better solution?

For now, this solution works as it does exactly what I want so I am happy with the outcome.
