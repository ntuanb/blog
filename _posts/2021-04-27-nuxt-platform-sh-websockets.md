---
layout: post
title: Running Nuxt with websockets on Platform.sh
author: tuan
image: /assets/images/2021/platform.sh.jpg
tags: []
---

To get a websocket server running on Platform.sh, create the following configuration files in a brand new repository:
```
# .platform.app.yaml
name: socket
type: 'nodejs:14'
build:
  flavor: none
dependencies:
  nodejs:
    yarn: "*"
hooks:
  build: |
    yarn install
web:
  commands:
    start: node io.js
  locations:
    '/':
      passthru: true
      request_buffering:
        enabled: false
```
```
# .platform/routes.yaml
"https://www.{default}/":
  type: upstream
  upstream: "socket:http"
  cache:
    enabled: false

"https://{default}/":
  type: redirect
  to: "https://www.{default}/"
```
Create the entry file for the node server. The `platformsh-config` library is important to run the server with the provided port in Platform.sh:
```
const express = require('express');
const app = express();
const http = require('http');
const server = http.createServer(app);
const { Server } = require("socket.io");

const config = require('platformsh-config').config();

const io = new Server(server, {
  cors: {
    origin: '*',
  },
});

app.get('/', (req, res) => {
  res.json('success')
});

io.on('connection', (socket) => {
  console.log('a user connected');
});

io.on('message', (msg) => {
  console.log(msg);
});

server.listen(config.port, () => {
  console.log(`listening on *:${config.port}`);
});

module.exports = {
  io
}
```

Log into Platform.sh, set up the Github (or any other service) integration and push the code and a build should process. Once completed, the url provided by Platform.sh should display the expected response when visiting in any browser.

In another repository, install `Nuxt` and the module `nuxt-socket-io`. Add the following code to the config file for Nuxt, updating the url with the provided url from the top:
```
  io: {
    // module options
    sockets: [{
      default: true,
      name: 'main',
      url: 'https://{name}.platformsh.site/'
    }]
  }
```
And `serverMiddleware/event.js`:
```
const io = require("socket.io-client");
const socket = io("https://{name}.platformsh.site/");
const bodyParser = require('body-parser')
const app = require('express')()

app.use(bodyParser.json())

app.all('/', async (req, res) => {
  console.log(req.body)

  socket.emit('test', req.body);
  res.json('Success')
});

module.exports = app
```
And `pages/index.vue`:
```
<template>
  <div>
    {{ tick }}
  </div>
</template>

<script>
export default {
  mounted() {
    this.socket = this.$nuxtSocket({
      name: 'main'
    })
    this.socket.on("tick", (tickId) => {
      this.tick = tickId
    });
    this.socket.on("test", (msg) => {
      console.log(msg)
    });
  }
}
</script>
```

Once completed, firing a request to the middleware will send a message to the Nuxt frontend.
