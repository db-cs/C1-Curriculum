---
title: Dgram and Node.js
unit: Drone Backend
order: 5
---

Now that we can send UDP packets to the drone, let's begin exploring ways to do this programmatically.

## Node.js

Node.js is a version of Javascript that runs on the commandline. Most servers use Node.js to power their applications. It runs on the same V8 Javascript engine used to power the Chrome browser.

Let's setup a new Node project. First let's see what version you are running.

```bash
$ node -v
```

Hopefully you have Node version 12 or better. If not we'll get that installed later in class. Now navigate to your `Development` directory. Create a new directory called `NodeExperiment`. Then create a new file named `server.js`.

Open up `server.js` and place the following code:

```javascript
const dgram = require("dgram");
const server = dgram.createSocket("udp4");

server.on("message", (msg, rinfo) =>
  console.log(`server got: ${msg} from ${rinfo.address}:${rinfo.port}`);

server.send(Buffer.from("command"), rinfo.port, rinfo.address);
```
