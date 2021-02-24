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
- [Github](#github)
  - [Storing the Token](#storing-the-token)
  - [Authenticating with GitHub](#authenticating-with-github)
  - [Grabbing the Stored Token](#grabbing-the-stored-token)
  - [GitHub API](#github-api)
  - [Testing Out Access to GitHub](#testing-out-access-to-github)
  - [Dealing with Errors](#dealing-with-errors)
- [Dealing with Repos](#dealing-with-repos)
  - [Creating the Remote Repo](#creating-the-remote-repo)
  - [Prompting User for Repo Details](#prompting-user-for-repo-details)
    - [Get the Current Directory](#get-the-current-directory)
  - [Setting Up a .gitignore](#setting-up-a-gitignore)
  - [Setting Up Our Repo](#setting-up-our-repo)
- [Final Steps](#final-steps)

---

**Note:** This project is based on the excellent tutorial written by Lukas White and James Hibbard on [SitePoint](https://www.sitepoint.com/javascript-command-line-interface-cli-node-js/). While we are going to deviate from this tutorial in some significant ways, the initial tutorial deserves much credit.

# Overview

Node.js is a versatile tool that is used to build many high level applications, but there are many other ways to used this tool. While we will eventually learn to create some high level programs, sometimes a task requires requires us to interact with system-level software in order to solve the problem. We're going to use Node to build a command line tool that will help us when we are starting a new project.

## Top-Down Design

Top-down design is where you break a larger problem down into (smaller) sub-problems, until the subproblems are directly solvable. A top-down design process can be represented by a structure diagram, you don't start coding until you have a solid idea of how you will break the problem down.

For our project, our larger problem is how to initiate a problem. Let's look at a structure diagram for our app.

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

Node has a build in package manager that will handle importing code and running our code. The package manager is `npm` or Node Package Manager. Each Node project should have a `package.json` which describes the app you're building. You can read what can be included at [https://docs.npmjs.com/cli/v6/configuring-npm/package-json](https://docs.npmjs.com/cli/v6/configuring-npm/package-json). Open the new file it created called `package.json` and update it with the following.

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
const utils = require("./lib/utils");
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

# Github

GitHub changed the way you can authenticate in November of 2020, which means you can no longer use a username and password to login to a 3rd party application. Rather you must generate a token for that application and specify what it has access to within your GitHub account through the GitHub API.

In order to test your app and make changes to your account you'll need to generate a Personal Access Token for this app to access the GitHub API. You can learn how to do this at [https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token).

Go to [https://github.com/settings/tokens](https://github.com/settings/tokenshttps://github.com/settings/tokens) and create a new token for `Superinit` that has the `repo` scope access. This just means that we'll be able to make changes to repos, but not anything else via the API.

The access token that is generated will be visible once. I'd suggest creating a txt file to store it in, because you'll have to generate a new one if you forget it. 😬

## Storing the Token

The great thing about the access token is that we can store it the first time a user enters it, and then we don't have to ask again. The last code we added was the prompt for the user to enter their credentials. We tested it and then deleted the calls to it in the main function. That's because we don't want to do it everytime. Instead we're going to use our `/lib/github.js`:

- prompt the user for credentials
- store that somewhere safe
- fetch it if it's already stored

The package we'll use to store our token is `configstore`. It will write the token to a file on our machine. Unlike using `fs`, config store puts this in a smart place and then you don't have to worry about it.

Now we need to install a few new packages. Open the shell and run:

```bash
$ npm install configstore clui @octokit/rest @octokit/auth-token @octokit/request
```

Open `/lib/github.js` and add the following statements to setup our module.

```javascript
const configstore = require("configstore");
const clui = require("clui");
const chalk = require("chalk");
const { Octokit } = require("@octokit/rest");
const { createTokenAuth } = require("@octokit/auth-token");
const { request } = require("@octokit/request");

const prompt = require("./prompt");
const pkg = require("../package.json");

const conf = new configstore(pkg.name);
const Spinner = clui.Spinner;
let ocktokit;
```

Now let's add some new methods to our exports.

```javascript
module.exports = {
  getInstance: () => ocktokit,
  getStoredGithubToken: () => conf.get("github.token");
};
```

## Authenticating with GitHub

Now that we have some place to store our token, let's get authenticated with GitHub. Let's add a new method to our exports.

```javascript
module.exports = {
  getInstance: () => {...},
  getStoredGithubToken: () => {...},

  // Add the new method here
  getPersonalAccessToken: async () => {
    // Let the user know what they need to authenticate
    console.log(
      chalk.yellowBright(
        "You'll need to generate a GitHub Personal Access Token."
      )
    );

    console.log(
      chalk.yellowBright("Visit"),
      chalk.underline.yellowBright(
        "https://git.io/JJyrT"
      ),
      chalk.yellowBright("to learn how.")
    );

    // Use the prompt module to get the user token
    const credentials = await prompt.askGithubCredentials();

    // Create a spinner and start to notify the user
    const status = new Spinner(
      "Authenticating you, please wait..."
    );
    status.start();

    // Generate an auth object
    const auth = createTokenAuth(
      credentials.token
    );

    // This lets us try code that may not work
    try {
      // Use our new auth to see if we're logged in
      // Returns our username
      const {
        data: { login: username },
      } = await auth.hook(request, "GET /user");

      // If we are logged in
      if (username) {
        // Display our username
        console.log(
          chalk.yellowBright(
            "\n",
            `Authenticated in as: ${username}`
          )
        );

        // Store the token for future use
        conf.set("github.token", auth.token);
        return auth.token;
      }
    } finally {
      status.stop(); //Stop the spinner when everything is finished.
    }
  },
}

```

Great now we can access GitHub to make changes. Next we'll get additional details from the user on what we'll call the repo and we'll create a module to handle creating the repos.

## Grabbing the Stored Token

If the user gets logged in using their personal access token, there is no need to ask them for it again. Instead let's check if it is stored, then we can load it if it's avaialble or prompt the user if it's not. Let's add the following method to our `/lib/github.js` exports.

```javascript
module.exports = {
  getInstance: () => {...},
  getStoredGithubToken: () => {...},
  getPersonalAccessToken: async () => {...},

  // Add the new method here
  getGithubToken: async () => {
    let token = module.exports.getStoredGithubToken();

    return (
      token ||
      (await module.exports.getPersonalAccessToken())
    );
  },
}
```

## GitHub API

Now that we are signed in, we need some way to interact with GitHub. Luckily we are provided with an API, but communicating with that API can be tricky. Octokit provides an easy way to work with GitHub. Let's add one last method to our `/lib/github.js` exports.

```javascript
module.exports = {
  getInstance: () => {...},
  getStoredGithubToken: () => {...},
  getPersonalAccessToken: async () => {...},
  getGithubToken: async () => {...},

  // Add the new method here
  githubAuth: (token) =>
    (ocktokit = new Octokit({ auth: token })),
```

This will use our token from storage/user to send commands to and from GitHub. You now have the POWER!!!

![Heman Meme](../images/you-have-the-power.jpg)

## Testing Out Access to GitHub

Back in the `index.js` let's make some calls to our new methods and test that it is working. Add the following to our `main` function.

```javascript
// With the other imports add
const github = require("./lib/github");

// Inside the main function add
const main = async () => {
  // Below the utils calls
  try {
    const token = await github.getGithubToken();
    github.githubAuth(token);

    console.log("All done!");
  } catch (error) {
    console.error(error);
  }
};
```

Now you can run `node index.js` and you should see it prompt you for your personal access token. Then it will try to connect to GitHub. If there is an error, it will show via the `console.error(error);`. We'll work on improving the error messages next. If not you should see `All done!`.

## Dealing with Errors

Errors do happen. Whether it's a problem with the personal access token or the internet is down at GitHub we need to plan for the worst, and give meaningful messages to our users when it happens. We're going to create a new method in the `/lib/utils`. Add the following new method.

```javascript
module.exports = {
  displayTitle: () => {...},
  checkForGitFolder: () => {...},

  // Add the new method here
  displayError: (error) => {
    switch (error.status) {
      case 401:
        console.error(
          chalk.red(
            "Couldn't log you in. Please provide correct personal identificaiton token"
          )
        );
        break;
      case 422:
        console.error(
          chalk.red(
            "There is already a remote repo or token with the same name."
          )
        );
        break;
      default:
        console.error(chalk.red(err));
    }
  },
}
```

Here we're using a switch/case. It's kind of like an if/else with less curly brackets. We're going to check which kind of error we recieve and send an appropriate message to the user. The `default` at the bottom will catch any other errors and output them in red.

Back in `index.js` let's update the `catch(error)`.

```javascript
catch (err) {
  // Replace console.error(error) with
  utils.displayError(err);
}
```

# Dealing with Repos

Our last module needs to deal with the creating both our local and remote repositories. We'll be working in the `/lib/repo.js`. Let's break down what this module will take care of:

- Create remote repository
- Generate a .gitignore
- Setup and push the local repository

First let's install and import the packages we need.

```bash
$ npm install simple-git lodash
```

Now let's import and set everything up.

```javascript
const cli = require("clui");
const fs = require("fs");
const simpleGit = require("simple-git");
const _ = require("lodash");

const prompt = require("./prompt");
const gh = require("./github");

const Spinner = cli.Spinner;
const git = simpleGit();
```

## Creating the Remote Repo

Let's add a new method that creates our repo.

```javascript
module.exports = {
  createRemoteRepo: async () => {
    // First we grab our current instance of octokit
    const github = gh.getInstance();

    // We haven't created this yet, but we'll ask the user for details
    const answers = await prompt.askRepoDetails();

    // Based on what we get from the user we'll set up our data obj
    const data = {
      name: answers.name,
      description: answers.description,
      private: answers.visibility === "private",
    };

    // Let the user know what we are doing
    const status = new Spinner("Creating remote repository...");
    status.start();

    // Attempt to create a new repo
    try {
      const res = await github.repos.createForAuthenticatedUser(data);
      return res.data.clone_url; //Return the url for the repo
    } finally {
      status.stop(); // Stop the spinner
    }
  },
};
```

This should be good to go, but we've created a problem for ourselves. We used `prompt.askForRepoDetails();` but we haven't made that yet. This is perfectly normal. We're working top-down. We define what we need and then go create it.

## Prompting User for Repo Details

We'll need a new package to make this work.

```bash
$ npm install minimist
```

Let's open up `/lib/prompt.js` and add the following.

```javascript
module.exports = {
  // Add this below the other methods
  askRepoDetails: () => {
    const argv = require("minimist")(process.argv.slice(2));

    const questions = [
      {
        type: "input",
        name: "name",
        message: "Enter a name for the repository:",
        default: argv._[0] || utils.getCurrentDirectoryBase(),
        validate: (value) =>
          value.length
            ? true
            : "Please enter a name for the repository.",
      },
      {
        type: "input",
        name: "description",
        default: argv._[1] || null,
        message: "Optionally enter a description of the repository:",
      },
      {
        type: "list",
        name: "visibility",
        message: "Public or private:",
        choices: ["public", "private"],
        default: "public",
      },
    ];

    return inquirer.prompt(questions);
  },
};
```

So `minimist` is going to allow us to read arguments. Instead of just running `superinit` from the commandline, we'll be able to provide it arguments as well. For example:

```bash
$ superinit my_repo "This is an awesome repo"
```

If we supply those in the commandline we'll use them to set a default value for the name and description. Then the user will just hit enter instead of typing it again. This is just one more tool that you can add to your toolbox.

### Get the Current Directory

If you read through this carefully, you will notice that we called `utils.getCurrentDirectoryBase();`. Once again we are working top-down. We created the call to a method we haven't defined. Let's go define that now.

In the `/lib/prompt.js` we first need to import `path`. This is a build in package that checks the path of where we are working.

```javascript
// With the other imports add
const path = require("path");
```

Now, let's add the new method to our exports.

```javascript
module.exports = {
  // Below all the other methods add
  getCurrentDirectoryBase: () => path.basename(process.cwd()),
};
```

This will get the current working directory, which the directory where we run `superinit` rather than the directory that `superinit` is stored in.

## Setting Up a .gitignore

We don't want to add every file in our directory to our repo. It is unecessary to add things like our `node_modules` and other directories. These take up a lot of space and our `package.json` makes it easy to install them on other systems by running `npm install`. A `.gitignore` is a file that defines all the directories and files that we want Git to ignore. Pretty simple, right.

So let's make a method that reads all the files in our directory and asks the user which ones to ignore. Then we'll right that list of files to a `.gitignore`. Inside of `/lib/repos.js` let's add our new method.

```javascript
module.exports = {
  // Add below the other methods
  createGitignore: async () => {
    // This uses lodash to get all the files and dir exect .git and .gitignore
    const filelist = _.without(
      fs.readdirSync("."),
      ".git",
      ".gitignore"
    );

    // Now that we have a list let's see what the user wants to ignore
    if (filelist.length) {
      const answers = await prompt.askIgnoreFiles(filelist);

      if (answers.ignore.length) {
        fs.writeFileSync(".gitignore", answers.ignore.join("\n"));
      } else {
        fs.appendFileSync(".gitignore", ".DS_Store");
      }
    } else {
      fs.appendFileSync(".gitignore", ".DS_Store");
    }
  },
};
```

Did you notice that we created a new problem for ourselves? We called `prompt.askIgnoreFiles(filelist);`, but that hasn't been defined yet. Once again we're working top-down. Let's go create that method in the `/lib/prompt.js`.

```javascript
module.exports = {
  // Below the other methods add
  askIgnoreFiles: (filelist) => {
    const questions = [
      {
        type: "checkbox",
        name: "ignore",
        message:
          "Select the files and/or folders you wish to ignore:",
        choices: filelist,
        default: ["node_modules", ".DS_Store"],
      },
    ];

    return inquirer.prompt(questions);
  },
};
```

Notice that we use the list of files generated by `lodash` to provide our users with choices. We pass that list as a parameter to our method.

## Setting Up Our Repo

Great now we have a remote repo and a list of things to ignore. Let's create one last method that will do all the git commands to setup, add, commit and push our project. Back in `/lib/repo.js` let's add a new method.

```javascript
module.exports = {
  // Add below the other modules
  setupRepo: async (url) => {
    // First we'll let the user know what we're doing
    const status = new Spinner(
      "Initializing local repository and pushing to remote..."
    );
    status.start();

    // Now we'll use simple-git to run all of our commands
    try {
      await git
        .init()
        .add(".gitignore")
        .add(".")
        .commit("Initial commit")
        .branch(["-m", "master", "main"])
        .addRemote("origin", url)
        .push("origin", "main");
    } finally {
      status.stop(); //Stop the spinner when we are done.
    }
  },
};
```

# Final Steps

Awesome we have everything created. Let's put it together. Back in `index.js` let's import and call all of our newly created modules.

First let's make sure we have imported our `github.js` and `repo.js` from `/lib`.

```javascript
// At the top with the other imports
const github = require("./lib/github");
const repo = require("./lib/repo");
```

Now in our `main` function let's add the following.

```javascript
const main = async () => {
  utils.displayTitle();
  utils.checkForGitFolder();

  try {
    const token = await github.getGithubToken();
    github.githubAuth(token);

    // Start adding here...
    const url = await repo.createRemoteRepo();

    await repo.createGitignore();

    await repo.setupRepo(url);
    // That should be everything new.

    console.log("All done!");
  } catch (err) {
    utils.displayError(err);
  }
};
```

Running `node index.js` should successfully initiate our Git project.

Running `node index.js` is kind of annoying, so let's set it up so we can just run `superinit` from the CLI.

Open your `package.json` and add the following below the `"main": "index.js"`.

```json
"bin": {
  "superinit": "./index.js"
},
```

Now you'll be able to run `npm install -g`, which will install our CLI tool to the global list of node packages. Once this completes, you'll be able to call `superinit` and your tool should run.
