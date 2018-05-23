---
layout: post
title: Deploy Upload to Dropbox
---

One of the projects requirements was to upload the project to Dropbox when a new release occurred. 

The bash script [Dropbox Uploader](https://github.com/andreafabrizi/Dropbox-Uploader) on Github was the perfect solution. 

{% highlight bash %}

scp -rp ~/clone/* root@192.168.1.1:/var/www/website.com/
curl "https://raw.githubusercontent.com/andreafabrizi/Dropbox-Uploader/master/dropbox_uploader.sh" -o dropbox_uploader.sh
chmod +x dropbox_uploader.sh
echo "OAUTH_ACCESS_TOKEN=token" > ~/.dropbox_uploader
./dropbox_uploader.sh -s -q -x node_modules -x build -x static/imgs -x tmp -x .git upload ~/clone /website

{% endhighlight %}

The Dropbox script also checks checksums to not avoid uploading the same file multiple times by using the `-s` command.

`-q` allows us to ignore any existing folder creation messages. If upload does not work, remove this parameter for testing.

Upon testing it out with Codeship, the builds would not finish because the uploader would always try to create new folders, causing errors. 

The code is now updated to zip and upload instead.

{% highlight bash %}

...
zip -r website.zip ~/clone
./dropbox_uploader.sh -s upload website.zip /website

{% endhighlight %}
