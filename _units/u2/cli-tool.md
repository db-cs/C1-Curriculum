---
title: Command Line Tool
unit: Drone Backend
order: 6
is_assignment: true
objectives:
  - Distiguish the purpose of system level apps versus application level apps
  - Break code down into smaller functions that abstract complexity
  - Discover useful packages that could be applied to other projects
est_time: 1 week
dropbox:
  title: 011.E Dev Blog Post
  url: https://kingsport.instructure.com/courses/20674/assignments/257511
---

<p><strong>Table of Contents:</strong></p>
- [Overview](#overview)
  - [Accessing the Project](#accessing-the-project)
  - [What We Are Going to Build](#what-we-are-going-to-build)
  - [Dependencies](#dependencies)
- [Setting Up a Node Project](#setting-up-a-node-project)
  - [Structuring the Project](#structuring-the-project)
- [Initalizing the CLI](#initalizing-the-cli)

---

# Overview

**Note:** This project is based on the excellent tutorial written by Lukas White and James Hibbard on [SitePoint](https://www.sitepoint.com/javascript-command-line-interface-cli-node-js/).

Node.js is a versatile tool that is used for many Web Applications, but there are many other applicaitons. While we will learn to create high level programs, sometimes a task requires requires us to interact with system-level software in order to solve the problem.

We're going to use Node to build a command line tool that will help us when we are starting a new project.

## Accessing the Project

The project is setup on Repl.it, and you'll need to open it using the link below.

[Open Project on Repl.it](https://repl.it/team/dbcs/012-Backend-DevelopmentCommand-Line-Tool){: style="border: 1px solid #ccc; border-radius: 5px; padding: 8px 10px; background: #efefef;" target="\_blank"}

Go ahead and setup the GitHub connection under the Version Control Tab, and then we'll be ready to dive in.

## What We Are Going to Build

![What we are going to build](../images/superinit.png)

When you first start a project, it's important to begin using git and to push that code to GitHub. This usually takes several steps and is a pretty repetitive task, so we're going to create a simple system utility to simplify this process. We'll call our application `super init`.

Typically starting a new project involves the following steps:

1. Run `git itit` to start tracking files.
2. Create a remote repo in Github.
3. Connect the local repo to the remote repo.
4. Create a `.gitignore` file to distiguish what we don't need to track.
5. Add all the initial files to the local repo.
6. Commit those changes to the local repo.
7. Then push those changes to the remote repo on GitHub.

Our new CLI tool will do all of this in just a few key strokes.

## Dependencies

In addition to writing our own code, we are also going to be importing quite a bit of additional code from packages, which will allow us to work quicker and build on the shoulders of giants. Node.js has a robust collection of packages that extends and enhances the build in modules available in node itself. The packages are housed online at [npmjs.com](https://npmjs.com).

Here's what all we'll be using:

- [chalk](https://www.npmjs.com/package/chalk) — colorizes the output
- [clear](https://www.npmjs.com/package/clear) — clears the terminal screen
- [clui](https://www.npmjs.com/package/clui) — draws command-line tables, gauges and spinners
- [configstore](https://www.npmjs.com/package/configstore) — easily loads and saves config
- [figlet](https://www.npmjs.com/package/figlet) — creates ASCII art from text
- [inquirer](https://www.npmjs.com/package/inquirer) — creates interactive command-line user interface
- [lodash](https://www.npmjs.com/package/lodash) — a JavaScript utility library
- [minimist](https://www.npmjs.com/package/minimist) — parses argument options
- [@octokit/rest](https://www.npmjs.com/package/@octokit/rest) — a GitHub REST API client for Node.js
- [@octokit/auth-token](https://www.npmjs.com/package/@octokit/auth-basic) — an implementation of one of GitHub’s authentication strategies
- [simple-git](https://www.npmjs.com/package/simple-git) — a tool for running Git commands in a Node.js application
- [touch](https://www.npmjs.com/package/touch) — a tool for implementing the Unix touch command.

# Setting Up a Node Project

First we need to make this a Node.js project. Open the shell in your Repl.it project and run the following command.

```bash
$ npm init -y
```

Open the new file it created called `package.json` and update it with the following.

```json
{
  "name": "superinit",
  "version": "1.0.0",
  "description": "'git init' turned up to 11",
  "main": "index.js",
  "keywords": ["Git", "CLI"],
  "author": "<YOUR NAME>",
  "license": "ISC"
}
```

Now let's install a couple of our dependencies using the shell.

```bash
$ npm install chalk figlet clear
```

These are all we need to start with. We'll add more later as we need them. The packages are stored in the folder named `node_modules`, which is not necessary for us to add to GitHub. Let's create a file that will tell git to ignore anything in the `node_modules` directory.

```bash
$ echo "node_modules" > .gitignore
```

This simple creates the file and then inserts a new line with the of `node_modules`.

Now open up `index.js` and add the following. Note the `require` method will import the node modules we installed in the last step, so we can use them in our project.

```javascript
const chalk = require("chalk");
const clear = require("clear");
const figlet = require("figlet");
```

## Structuring the Project

Instead of writing everything in the `index.js`, we are going ot setup some helper methods to break our code up into seperate modules. We'll have a `lib` directory that will house all of these helper modules.

- **files.js** — basic file management
- **inquirer.js** — command-line user interaction
- **github.js** — access token management
- **repo.js** — Git repository management.

In the shell run the following:

```bash
$ mkdir lib
$ touch lib/files.js lib/prompt.js lib/github.js lib/repo.js
```

Let’s start with `lib/files.js`. This helper will take care of the following:

- get the current directory to use as the repo name
- check whether a directory already exists so we can see if it's already a git repo

```javascript
const fs = require("fs");
const path = require("path");

module.exports = {
  getCurrentDirectoryBase: () => path.basename(process.cwd()),
  directoryExists: (filePath) => fs.existsSync(filePath),
};
```

Now we can go back to `index.js` and add the following line to require the new module:

```javascript
const files = require("./lib/files");
```

# Initalizing the CLI

Great! We now have everything setup and ready to start building our CLI tool. Let's start off by add a title to display to our user when they start up our applicaiton. We'll be using two packages we imported earlier: `chalk` and `figlet`.

Add the following below your imports in `index.js`.

```javascript
clear();

console.log(
  chalk.yellowBright(
    figlet.textSync("Super Init", { horizontalLayout: "full" })
  )
);

console.log(
  "A simple utility that runs git init and adds a remote on GitHub.",
  "\n"
);
```

Note that the `"\n"` adds a new line after our description. It's an escape character which is why it starts with the `\`. This will make the menu we're about to build appear a line lower, which will just look cleaner.

Now you can run your app using `node index.js` in the shell.

![The title of our app](../images/titlesetup.png)

Now that we have a cool title let's setup a quick check to see if git has already been setup. Right after the code for the title in `index.js` add the following:

```javascript
if (files.directoryExists(".git")) {
  console.log(chalk.red("Already a Git repository!"));
  process.exit();
}
```

This will call the function we setup in `files.js` named `directoryExists()`. If it does exist, then we'll notify the user and exit the program. `process.exit()` will stop our app and return to the command prompt.

Go ahead and commit your changes if you haven't already.

<!-- # User Input

# Github

## Authenticating with GitHub

## GitHub API

# Local Repository

# Final Steps -->
