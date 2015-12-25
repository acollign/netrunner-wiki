This page contains all the information you need to get started with developing as well as useful tips (see also [this issue](https://github.com/mtgred/netrunner/issues/328)).

## IDE

There are several options available, including:

- [IntelliJ IDEA](https://www.jetbrains.com/idea/) with [Cursive](https://cursiveclojure.com/)
- Emacs ([Unix](https://www.gnu.org/software/emacs/) | [Windows](https://ftp.gnu.org/gnu/emacs/windows/) | [MacOS](http://emacsformacosx.com/)) with [CIDER](https://github.com/clojure-emacs/cider)

## Dependencies

You need the following dependencies installed:

- [Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (for MacOS, download the full [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html))
- [Node.js](https://nodejs.org/download/), Node Package Manager (make sure `npm` is in your `PATH`)
- [Leiningen (version 2+)](http://leiningen.org/)
- [MongoDB](https://www.mongodb.org/)
- [Coffeescript](http://coffeescript.org/) (`npm install -g coffee-script`)
- [Bower](http://bower.io/) (`npm install -g bower`)
- [Stylus](https://learnboost.github.io/stylus/) (`npm install -g stylus`)
- [Python 2.7](https://www.python.org/)
- [ZeroMQ](http://zeromq.org/)
- A C++ compiler (g++ on Linux, Visual Studio on Windows)

## Git

If you want to contribute, you should fork the mtgred/netrunner repository on github. 

After cloning your own fork on your machine, configure mtgred/netrunner as a remote.

```
$ git remote add upstream https://github.com/mtgred/netrunner.git
```

When you run `git remote -v`, you now should see something like this:

```
origin  https://github.com/yourgithubname/netrunner (fetch)
origin  https://github.com/yourgithubname/netrunner (push)
upstream        https://github.com/mtgred/netrunner.git (fetch)
upstream        https://github.com/mtgred/netrunner.git (push)
```

To keep your own repository up-to-date, refer to [Syncing a fork](https://help.github.com/articles/syncing-a-fork/).

If you want to start coding a feature or fix a bug, simply use

```
git checkout -b your_new_branch_name upstream/master
```

to create a local copy of the current `master` branch named `your_new_branch_name` that can easily be merged with a pull request.

Push it to your own repo with `git push origin your_new_branch_name` and create a pull request via the github website.

### .gitignore

Adding IDE-specific files to .gitignore should happen in a global `gitignore` file, e.g. in `~/.gitignore`:

```
# IDEA IntelliJ files
.idea
*.iml
```

And then inform git of this file via:

```
git config --global core.excludesfile '~/.gitignore'
```

## Installation

Install Node.js dependencies:

```
$ npm install
```

> `npm install` throws errors when trying to compile modules on Windows :-(

You have to specify the `--msvs_version` flag. So, if you use Visual Studio 2013, run
```
npm install --msvs_version=2013 
```
> I'm still getting errors like `error: ‘NewSymbol’ is not a member of ‘v8::String’` when `npm` tries to compile `gyp` on Windows :-(

There's a problem with the engine.io node module that depends on an old version of the ws module. See [this](https://github.com/Automattic/engine.io-client/issues/376) and [this](https://github.com/Automattic/socket.io/issues/2057) issue for more information.

> `npm install` throws errors on MacOS that complain about `zmq`

To be able to install and find `libzmq`, first install [Homebrew](http://brew.sh), then use it to install `zeromq` and `pkg-config`: 


```
$ brew install zeromq
$ brew install pkg-config
```

> `npm install` fails on Linux: npm WARN This failure might be due to the use of legacy binary "node"

Install the nodejs-legacy package:

```
sudo apt-get install nodejs-legacy
```

---
Install JavaScript dependencies:

```
$ bower install
```
---
Launch MongoDB _(possibly with --dbpath option specifying card data directory)_ and fetch card data:

```
$ mongod
$ cd data
$ coffee fetch.coffee
```

On Windows, run `mongod.exe`. See [Install MongoDB on Windows](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/).

---
Compile and watch client side Clojurescript files:

```
$ lein cljsbuild auto dev
```

Compile and watch CSS files:

```
$ stylus -w src/css -o resources/public/css/
```
---
Compile server side Clojure files:

```
$ lein uberjar
```
---
Launch game server:

```
$ java -jar target/netrunner-0.1.0-SNAPSHOT-standalone.jar
```
---
Launch the Node server:

```
$ coffee server.coffee
```

## Automated Environment

To get a local copy running with one command, see https://github.com/astrostl/nrdev

## Setting up IntelliJ IDEA 

> How can I use a REPL while running the server?

Create a new Run Configuration and choose `Clojure REPL -> Local`:

![Setting a REPL](https://i.imgur.com/Df7h756.png)

When the REPL started, use `(future-call dev)` to start the game server.

## Using IntelliJ IDEA with Cursive

> I start a REPL from IntelliJ IDEA, but I get the following error: 'No nREPL ack received'.

Cursive uses a default time out of 60 seconds when trying to connect to the REPL. Change `Settings -> Clojure -> REPL startup timeout` to a higher value:

![Increase the timeout](https://i.imgur.com/2OlGHtA.png)

> How can I access the code in the REPL?

Right click the file in the editor, `REPL -> Switch REPL NS to current file` or run `(in-ns 'game.core)` to switch the namespace

## Using Emacs with Cider

If you want to use Emacs you have to install clojure-mode and cider. Cider allows you to connect Emacs to a REPL with C-c M-j. To reload a function, it's C-c C-c with the cursor inside the function definition. To reload a file (eg. card.clj) it's C-c C-k.

## Videos

These live-coding videos may help you with some of the basics:

* [Brief introduction / Implementing Tenma Line](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)
* [Events system overview / Implementing Utopia Shard](https://www.livecoding.tv/video/jintekinet-events-utopia-shard-12/)
* [Resolving abilities / Implementing Howler](https://www.livecoding.tv/video/jintekinet-resolving-abilities-howler/)

## Good Starting Issues

Here are some easier / less complicated issues from our Issue Tracker than newer developers can look into in order to better learn the code base with an active example:

* [#1013](https://github.com/mtgred/netrunner/issues/1013) - Paige Piper prints the wrong card name when choosing to remove cards from Stack and shouldn't be preventable
* [#977](https://github.com/mtgred/netrunner/issues/977) - Chameleon won't return to the Grip if hosted on something _after_ being installed
* [#700](https://github.com/mtgred/netrunner/issues/700) - Negative max hand size for the Runner at the end of their turn doesn't trigger a Corp win
* [#523](https://github.com/mtgred/netrunner/issues/523) - Hosting cards doesn't check for uniqueness
* [#322](https://github.com/mtgred/netrunner/issues/322) - add a "Thinking..." button to the UI. At the simplest, could simply print "____ is thinking" to `(system-msg)`.

_Updated 23 December 2015_