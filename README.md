# Node.js Lunch and Learn

## Contents

1. [Intro](#intro)
1. [Install nvm](#install-nvm)
1. [Running Node](#running-node)
1. [Using npm](#using-npm)
    * [Starting a New Project](#starting-new-project)
    * [Adding a Dependent Package](#adding-dependency)
    * [Running Scripts](#running-scripts)
    * [Removing a Dependent Package](#removing-dependency)
    * [npm Best Practices](#npm-best-practices)

## <a name="intro"></a>Intro

Node.js is "an open-source, cross-platform JavaScript run-time environment for executing JavaScript code server-side" (see https://en.wikipedia.org/wiki/Node.js).

The Good:
* package management [npm](https://en.wikipedia.org/wiki/Npm_(software))
* easy to use
* good for dev ops
* good for small applications (have it running on my Raspberry Pi at home because its so darn light weight)
* great for front end support build step

The Bad:
* Single threaded - (see [The Way of the Gopher](https://medium.com/digg-data/the-way-of-the-gopher-6693db15ae1f)) - this is a major show stopper for most production applications.

So, why use it?
* Javascript is growing up as a programming language.
* Javascript will continue to dominate the front end for web applications.
* Trend away from js and css within HTML towards a HTML and styles within js approach. Javascript has taken over as the starting point.
* Even if you never spin up a node server, you will need to know Node's package manager (npm) and will use it for front end build processes.
* Node is also very helpful as a light weight scripting language for batch and dev ops applications.

## <a name="install-nvm"></a>Install nvm

As a best practice, projects that use node.js should standardize on a particular version of node in their projects. This can be documented by specifying the "engines" attribute in your package.json file (see https://devcenter.heroku.com/articles/nodejs-support#specifying-a-node-js-version). More on package.json settings when we discuss node's package manager (npm).

You can install node.js directly from https://nodejs.org/en/. However, since projects often standardize on different versions of node, it can be handy to have a tool to switch node versions from the command line when switching projects. That's where [nvm](https://github.com/creationix/nvm) (Node Version Manager) comes in handy.

Therefore, I recommend that developers install nvm first and use nvm to install Node.js.

Windows Users:
* Go to https://github.com/coreybutler/nvm-windows and follow the setup instructions.

Linux / Mac Users:
* Go to https://github.com/creationix/nvm and follow the setup instructions.

Some useful nvm instructions:
```
nvm ls                # - displays a list of installed node versions
nvm ls-remote         # - displays available versions to install
nvm install v7.10.1   # - install a particular version of node
nvm use v8.6          # - switch versions
nvm uninstall v7.10.1 # - uninstalls a particular version of node
```
It is important to note that each version installed has its own copy of any node_modules that have been installed for that version. If you switch versions, you may not have the same global modules installed.

## <a name="running-node"></a>Running Node

```
$ node -v               # - check what version you are using
$ echo "console.log('hello world')" | node
$ node
> a = 1; b = 2; a+b
3
> 
(To exit, press ^C again or type .exit)
> 
$ node [javascript filename.js]
```

To run an application, simply use the node command and pass the javascript file as the main argument.

```
node hello_world.js
node server.js
```

## <a name="using-npm"></a>Using npm

npm is Node's package manager. Go to https://www.npmjs.com/ to discover packages to build awesome Javascript applications with.

### <a name="starting-new-project"></a>Starting a New Project
When starting a project, it can be helpful to create a package.json file, which lists all of the packages that your project is dependent on.

```
$ npm init           # - creates your default package.json
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help json` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (node_lunch_and_learn) 
version: (1.0.0) 
description: Lunch and Learn About Node.js
entry point: (server.js) 
test command: 
git repository: 
keywords: 
author: Dave Stevens
license: (ISC) 
About to write to /projects/node_lunch_and_learn/package.json: ...
```

### <a name="installing-existing-project"></a>Installing an Existing Project

Node uses the package.json file (created in the above step) to keep track of your project's dependencies. One of the first steps you will need to take after cloning an existing node project is to install all of its dependent packages listed in package.json. These packages are placed into a special folder called node_modules. To create this folder and install all dependent packages run the following:

```
npm install    # Intalls all dependencies listed in package.json into the node_modules folder
```

### <a name="adding-dependency"></a>Adding a Dependent Package

If you want to add a dependency out on the npm registry to your project, you can install a package as either a prod dependency (required while the application executes) or as a dev depedency (required to enhance or build the project).

```
npm install --save-prod express # installs the express package as a prod dependency
npm install --save-dev gulp     # installs the gulp package as a dev dependency
```

When you use the --save options, the package is added to the "dependencies" section of package.json. One of the most important settings in your package.json file is the package version. You can edit the package.json file to change what version of a package you want to use. See https://docs.npmjs.com/misc/semver#versions for a guide on how to specify what version of a package your project is dependent on.

### <a name="running-scripts"></a>Running Scripts

The package.json file contains a place for command line scripts where you can define a script to run using a shorthand npm command. For example, a start script can be defined as follows:

```
  "scripts": {
    "start": "node server.js"
  },
```

This can then be run using the following npm command.

```
npm run start
```

### <a name="removing-dependency"></a>Removing a Dependent Package

To remove a dependent package you can run `npm uninstall`. For example, the following will uninstall the express package and also remove the dependency from your package.json file.

```
npm uninstall --save express 
```

Now, if we run `npm run start` we will see that we get an error because a dependency is missing from the node_modules directory.

```
$ npm run start

> node_lunch_and_learn@1.0.0 start /projects/node_lunch_and_learn
> node server.js

module.js:529
    throw err;
    ^

Error: Cannot find module 'express'
```

You can also remove packages by removing them from the list in package.json and then calling `npm prune`.

### <a name="npm-best-practices"></a>npm Best Practices

* Before going into production, be sure to lock down your dependencies with `npm shrinkwrap`. This will create a file called `npm-shrinkwrap.json` which lists the exact package versions of all installed packages in the entire hierarchy. This is important for replicating environments. It is important to note that npm-shrinkwrap.json takes presedence over package-lock.json when doing an `npm install`. Once an `npm shrinkwrap` is executed, you need to intentionally update your packages using `npm update`, in which case you need to repeat the `npm shrinkwrap` command, test and then push your changes to your repo.