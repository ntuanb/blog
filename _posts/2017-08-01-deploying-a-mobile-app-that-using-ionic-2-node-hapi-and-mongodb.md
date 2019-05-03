---
layout: post
title: Deploying a Mobile App That Uses Ionic 2, Node, Hapi and MongoDB
author: tuan
categories: [tutorial, software]
image: assets/images/2017/ionic.jpg
---

A very short tutorial to help me remember how to deploy one of the mobile applications I created in my own time for fun. 

The app uses a variety of different libraries and tools, mostly for practice and learning purposes. I don't plan on any more updates to this app unless required because it is a very simple idea that works as intended.

The app is called Last Link because it displays what the last person searched using the search form. You can see the app on the Play Store using the following [link](https://play.google.com/store/apps/details?id=com.ntuanb.lastlink).

Note, if the connection does not work, that might be because I haven't set up SSL renewing properly yet.

# Angular

1. Run the command `ionic-app-scripts build` to build the APK.
1. Deploy the APK to the Google Play Store.

# Node and Hapi

Make sure your Hapi server points to the right chosen port.

{% highlight javascript %}

self.server = new Hapi.Server();
self.server.connection({
    port: 3000, 
    host: 'localhost',
    routes: {
        cors: {
            origin: ['*'],
            additionalHeaders: ['key', 'cache-control', 'x-requested-with', 'Access-Control-Allow-Origin']
        }
    }
});

{% endhighlight %}

Make sure that your MongoDB connection is set to the right internal url.

{% highlight javascript %}

mongoose.connect('mongodb://localhost/lastLink');

{% endhighlight %}

1. Install Package Manager 2 with the following cmd `sudo npm install pm2 -g`.
1. Run your application with the following cmd `sudo pm2 start /var/www/index.js`.
1. Check you app is in a healthy state with `sudo pm2 l`.

# MongoDB

1. Install Mongod as a service with the following cmd `sudo apt-get install -y mongodb-org`.
1. Start Mongod with `sudo service mongod start`.

# Finally
Setup your Nginx install to use a proxy pass configuration that points to the point that we are using, in this case port `3000` and include any relavent SSL configurations.

{% highlight nginx %}

server_name ...

location / {
    proxy_pass http://localhost:3000;
    proxy_redirect off;
    proxy_set_header Host $http_host;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-NginX-Proxy true;
    proxy_set_header X-Real-IP $remote_addr;
}

...

{% endhighlight %}

Restart your server `sudo service nginx restart` and test your application's api endpoints via `curl localhost:3000`.

# Conclusion

My plan was to add in `[Fastlane](https://fastlane.tools/)` at some point.