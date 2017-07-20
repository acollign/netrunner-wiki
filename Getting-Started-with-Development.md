This page contains all the information you need to get started with developing as well as useful tips (see also [this issue](https://github.com/mtgred/netrunner/issues/328)).

## Language

The main language the whole project is written in is Clojure. Below are a few links to useful learning resources and documentation of Clojure and libraries used:

- [Clojure for the Brave and True](http://www.braveclojure.com/) - a nice online crash course of Clojure, written in accessible language
- [ClojureDocs](https://clojuredocs.org/core-library) - online Clojure documentation
- [Om](https://github.com/omcljs/om) - ClojureScript interface to Facebook's [React](https://facebook.github.io/react/index.html). Used in many places on the web presentation side.
- [Hiccup](https://github.com/weavejester/hiccup/wiki/Syntax) and [Ŝablono](https://github.com/r0man/sablono) guides - they describe the HTML templating libraries used in the `.cljs` files.

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

*[Here's a guide for Windows specifically](https://github.com/mtgred/netrunner/wiki/Setting-up-Jnet-Development-on-Windows)*

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
## Get Up and Running
**1. Launch MongoDB** _(possibly with --dbpath option specifying card data directory)_ and fetch card data:

```
$ mongod
$ npm run fetch
```

This data fetch only needs to be performed if it's your first time building the project OR new card data has been made available on NetrunnerDB and you want to update your local data (e.g., a new data pack). 

On Windows, run `mongod.exe`. See [Install MongoDB on Windows](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/).

If you get time-out errors (`ETIMEDOUT`) while running `coffee fetch.coffee` the data is still being downloaded if you see the card numbers being output in the terminal window. Just rerun the `fetch.coffee` until all cards are downloaded (no more numbers being printed).

---
**2. [OPTIONAL] Compile and watch client side ClojureScript** _(only necessary if this is your first time building the project OR any .cljs files have been edited/modified and you need to update the UI)_:

```
$ lein cljsbuild auto dev
```
---
**3. [OPTIONAL] Compile and watch CSS files** _(this step can be skipped if you have no plans to modify CSS/layout items)_:

```
$ stylus -w src/css -o resources/public/css/
```
---
**4. Compile server side Clojure files**
```
$ lein uberjar
```
---
**5. Launch game server:**

```
$ java -jar target/netrunner-0.1.0-SNAPSHOT-standalone.jar
```

---
**6. Launch the Node server**

```
$ npm run start
```
---
**7. Run browser(s)**

Open one or more browser sessions and visit: http://localhost:1042

### Minimalist Alternative to Steps 4 and 5

Instead of building the production JAR files and running them with Java, a much faster way of launching the game server is to run the REPL (read-eval-print-loop) directly from the command line: 

```
$ lein repl
```

This compiles the project if it's out of date, then launches an interactive shell from which you can type and evaluate Clojure commands. Inside the REPL, do `(future-call dev)` to launch the game server. Once you have a game initiated, you can modify card code and reload it with `(load-file "src/clj/game/cards.clj")` and very quickly see changes reflected in an ongoing game by simply trashing/discarding a copy of the card in question and reinstalling it or playing it again to see the new behavior.


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


> How do I run the tests?

Run `lein test test.all` from the command line.

> I have the REPL up and running, now what?

You find the game state in `@game-states`. You probably want to run `(def state (second (first @game-states)))` as soon as you started your game so you can easily access its state now via `@state`.

> So, I have changed a card, now what?

Run `(load-file "src/clj/game/cards.clj")` to reload all cards. Note that if the card you changed is already in play, you have to play it again before you see your code changes.

## Using Emacs with Cider

If you want to use Emacs you have to install clojure-mode and cider. Cider allows you to connect Emacs to a REPL with C-c M-j. To reload a function, it's C-c C-c with the cursor inside the function definition. To reload a file (eg. card.clj) it's C-c C-k.

## Videos

These live-coding videos may help you with some of the basics:

* [Brief introduction / Implementing Tenma Line](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)
* [Events system overview / Implementing Utopia Shard](https://www.livecoding.tv/video/jintekinet-events-utopia-shard-12/)
* [Resolving abilities / Implementing Howler](https://www.livecoding.tv/video/jintekinet-resolving-abilities-howler/)
* [Testing framework / Fixing a MaxX bug](https://www.livecoding.tv/nealpro/videos/E7eo3-jintekinet-testing-framework-fixing-a-bug)
* [Synchronized resolution of triggers / Fixing a Bio-Ethics Association bug](https://www.livecoding.tv/nealpro/videos/z8o6Y-jintekinet-resolution-of-simultaneous-triggers)

## Slack channel

The main communication channel between developers, besides GitHub, is the [team's Slack Channel](https://jinteki.slack.com/). To request access, send an email to [mtgred](mailto:mtgred@gmail.com) stating who you are on GitHub and linking two Pull Requests you got accepted into the repository. 

## Good Starting Issues

If you want to look for some of the easier issues to get started, look through our GitHub issues for [issues labeled **easy**](https://github.com/mtgred/netrunner/labels/easy). These usually don't require in-depth codebase knowledge, and changes required to fix them shouldn't involve more than 1-3 source files.

## Pull requests and branches

There are two main branches in this repository, `master` and `dev`. **TODO**