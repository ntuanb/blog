---
layout: post
title: Missing Bindings for SQLite3
---

This particular problem is related to installing SQLite3 for Node.

To begin, run the command `npm install sqlite3`.
If it doesn't work, run the command `npm install sqlite3 --build-from-source` instead. 

If both commands don't work after starting the node application and an error similiar to 
`/node_modules/sqlite3/lib/binding/node-v43-darwin-x64/node_sqlite3.node` comes up,
we need to install SQLite3 but switching node versions.

Using NVM, install any major versions `nvm install v5.6`, switch to the version `nvm use v5.6` and run `npm install sqlite3` or `npm install sqlite3 --build-from-source`.

Attempt to start the Node application `node index`.
If a similiar error occurs again, switch to another version and repeat the process.

Once its working, switch Node back to the desired version.