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
  console.log(
    `server got: ${msg} from ${rinfo.address}:${rinfo.port}`
  )
);

server.send(Buffer.from("command"), 8889, "192.168.0.2");
```

## Delaying Commands

The robot can't execute multiple commands all at once. It needs time to complete a command. We need to delay commands being sent, so it can execute them. Luckily JavaScript has a built in method for doing this.

The method is `setTimeout()` and is part of JavaScript's built in timing events. Open the [JS Timing Events](https://www.w3schools.com/js/js_timing.asp) documentation.

So in order to delay a command, we would use our command as the function parameter of the `setTimeout()` method. For example:

```javascript
// Code to connect to setup dgram ommitted but still necessary

setTimeout(
  () => server.send(Buffer.from("command", 8889, "192.168.0.2")),
  1000
);
```

In the example above, the function will execute after 1s or 1000ms. In the function parameter, we are using an arrow function. It is good practice to use an anonymous function or an arrow function in this case. Eventually we want to abstract the `server.send` method, but for now you have all the tools you need to send a sequence of commands to the drone. Have fun!
