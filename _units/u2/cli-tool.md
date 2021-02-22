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
  - [Top-Down Design](#top-down-design)
  - [Accessing the Project on Repl.it](#accessing-the-project-on-replit)
  - [What We Are Going to Build](#what-we-are-going-to-build)
  - [Dependencies](#dependencies)
- [Setting Up a Node Project](#setting-up-a-node-project)
- [Initalizing the CLI](#initalizing-the-cli)
  - [Structuring the Project](#structuring-the-project)
  - [Utilities](#utilities)
  - [Checking to See If Already a Git Repo](#checking-to-see-if-already-a-git-repo)
- [User Input](#user-input)
  - [Testing](#testing)

---

**Note:** This project is based on the excellent tutorial written by Lukas White and James Hibbard on [SitePoint](https://www.sitepoint.com/javascript-command-line-interface-cli-node-js/). While we are going to deviate from this tutorial in some significant ways, the initial tutorial deserves much credit.

# Overview

Node.js is a versatile tool that is used to build many high level applications, but there are many other ways to used this tool. While we will eventually learn to create some high level programs, sometimes a task requires requires us to interact with system-level software in order to solve the problem. We're going to use Node to build a command line tool that will help us when we are starting a new project.

## Top-Down Design

Top-down design is where you break a larger problem down into (smaller) sub-problems, until the subproblems are directly solvable. A top-down design process can be represented by a structure diagram, you don't start coding until you have a solid idea of how you will break the problem down.

For our project, our larger problem is how to initiate a problem. Let's look at a strugure digram for our app.

![Top-Down Design for the Project](../images/topdown.jpg)

## Accessing the Project on Repl.it

The project is setup on Repl.it, and you'll need to open it using the link below. We'll use Repl.it to build the project, but you could easily build it on your Mac using VS Code. I'm going to request you work on Repl.it, so I can monitor your progress and leave you notes as you work.

[Open Project on Repl.it](https://repl.it/team/dbcs/012-Backend-DevelopmentCommand-Line-Tool){: style="border: 1px solid #ccc; border-radius: 5px; padding: 8px 10px; background: #efefef;" target="\_blank"}

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
- [clui](https://www.npmjs.com/package/clui) — draws command-line tables, gauges and spinners
- [configstore](https://www.npmjs.com/package/configstore) — easily loads and saves config
- [figlet](https://www.npmjs.com/package/figlet) — creates ASCII art from text
- [inquirer](https://www.npmjs.com/package/inquirer) — creates interactive command-line user interface
- [lodash](https://www.npmjs.com/package/lodash) — a JavaScript utility library
- [minimist](https://www.npmjs.com/package/minimist) — parses argument options
- [@octokit/rest](https://www.npmjs.com/package/@octokit/rest) — a GitHub REST API client for Node.js
- [@octokit/auth-token](https://www.npmjs.com/package/@octokit/auth-basic) — an implementation of one of GitHub’s authentication strategies
- [simple-git](https://www.npmjs.com/package/simple-git) — a tool for running Git commands in a Node.js application

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
$ npm install chalk figlet
```

These are all we need to start with. We'll add more later as we need them.

# Initalizing the CLI

Great! We now have everything setup and ready to start building our CLI tool. First let's setup a function that will contain the entire app. We'll have sever asyncronous calls in our app, so let's make this an async function.

```javascript
const main = async () => {
  // We'll add our code to run the app here.
};

require.main === module && main();
```

We are going to store our function in a `const main = async () => {}` rather than just using `async function main() {}` to define the function. This will prevent the name from being overridden later.

Unlike most programming languages, node does not have a built-in entry point. We can add our own by checking if the current module is the main module `require.main === module`. If this is true, the current file has been run directly - as opposed to have been imported by another file - and in this case we can call the main function. The `statement && statement` example of short circuit logic. If the first statement is true, then the second statement is run.

## Structuring the Project

Instead of writing everything in the `index.js`, we are going create use sub-modules to break our code up into smaller sub-programs like we did in the top-down desgin. We'll have a `lib` directory that will house all of these helper modules.

- **utils.js** — general utilies
- **prompt.js** — command-line user interaction
- **github.js** — github access token management
- **repo.js** — git repository management

In the shell run the following:

```bash
$ mkdir lib
$ touch lib/utils.js lib/prompt.js lib/github.js lib/repo.js
```

## Utilities

Now that we have a way to run our code, let's add a title to display to our user when they start up our applicaiton. We'll be using two packages we imported earlier: `chalk` and `figlet`.

Now open up `lib/utils.js` and add the following. Note the `require` method will import the node modules we installed in the last step, so we can use them in our project.

```javascript
const chalk = require("chalk");
const figlet = require("figlet");
```

Since this will be imported in the `index.js` we need to export this code.

```javascript
module.exports = {
  displayTitle: () => {
    console.clear();

    console.log(
      chalk.yellowBright(
        figlet.textSync("Super Init", { horizontalLayout: "full" })
      )
    );

    console.log(
      "A simple utility that runs git init and adds a remote on GitHub.",
      "\n"
    );
  },
};
```

First we export an object that has a single method with the key `displayTitle`. It has a function as value that will console.log our title.

In the first part of the method, we clear the console and use `chalk` and `figlet` to create a fancy title. The `"\n"` adds a new line after our description. It's an escape character which is why it starts with the `\`. This will make the menu we're about to build appear a line lower, which will just look cleaner.

Now we can go back to `index.js` and add the following line to the top to require the new module:

```javascript
const files = require("./lib/files");
```

Then let's call our `displayTitle` method. Inside the `main` add:

```javascript
utils.displayTitle();
```

Now you can run your app using `node index.js` in the shell.

![The title of our app](../images/titlesetup.png)

Now that we have a cool title, let's add a quick check to see if git has already been setup.

## Checking to See If Already a Git Repo

Now lets add a new method to `lib/utils.js`. This helper method will take care of the following:

- get the current directory to use as the repo name
- check whether a directory already exists so we can see if it's already a git repo

```javascript
// With the other imports
const fs = require("fs");

module.exports = {
  displayTitle: () => {...},

  // Adding the new module
  checkForGitFolder: () => {
    if (fs.existsSync(".git")) {
      console.log(chalk.red("Already a Git repository!"));
      process.exit();
    }
  },
}

```

Right after the code for the title in `index.js` add the following:

```javascript
utils.checkForGitFolder();
```

This will call the method we setup in `utils.js`. If a `.git` folder exists, then we'll notify the user and exit the program. Note the `process.exit()` will stop our app and return to the command prompt.

Go ahead and test this out. You can temporarily create a `.git` directory and run `node index.js` to check if it is working.

# User Input

Our next step is to start prompting the user for input. We'll use the `inquirer` package to create various types of prompts.

Open the `lib/prompt.js`. Then import the `inquirer` package.

```javascript
const inquirer = require("inquirer");
```

Just like in the `lib/utils` we'll be exporting the various methods, so go ahead and add the following:

```javascript
module.exports = {
  askGithubCredentials: () => {
    const questions = [
      {
        name: "token",
        type: "password",
        message: "Enter your personal access token:",
        validate: (value) =>
          value.length
            ? true
            : "Please enter your personal access token.",
      },
    ];

    return inquirer.prompt(questions);
  },
};
```

As you can see we first create an array of questions to prompt the user for their personal access token. This is a one time password that allows our app to access GitHub's api. You can read more about it at [https://git.io/JJyrT](https://git.io/JJyrT). For now just know that it works like a password.

## Testing

Let's test our new menu. Go back to `index.js` and import our new modules.

```javascript
const prompt = require("./lib/prompt");
```

Then add the following to main after our previous code.

```javascript
const credentials = await prompt.askGithubCredentials();
console.log(credentials);
```

Now run `node index.js`. We won't be actually calling `prompt` from the `main` function, so go ahead and remove the import for `prompt` adn the code to get the credentials.

<!--
# Github

## Authenticating with GitHub

## GitHub API

# Local Repository

# Final Steps -->
