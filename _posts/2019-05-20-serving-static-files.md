---
layout: post
title: Serving Static Files
author: tuan
categories: [tutorial]
image: /assets/images/2019/storage.jpg
tags: []
---

Files should be served directly and whitelisted by the web server where applicable. If there comes a time
when the files timeout especially if of larger than normal size, this may be because the filetype has 
not been added to the list to bypass and delivered as is.