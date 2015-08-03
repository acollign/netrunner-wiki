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
- [Python 2.7](https://www.python.org/)
- A C++ compiler (g++ on Linux, Visual Studio on Windows)

Optional:

- [Stylus](https://learnboost.github.io/stylus/) (`npm install stylus -g`)
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

## Development tips

#### Server

When you modify a function or a card, simply reload the function or the `cards.clj` file to the REPL and it should be reflected in the game engine without having to recreate a new game:

    (load-file "src/clj/game/cards.clj")

For cards already installed you have to drag it back to hand and install it again to reflect the new code. No need to refresh the browser, you can keep the game state.

To inspect the content of clojure data structure, `prn` is handy. You can add `prn` in your functions or inside the `(req )` macro in card definitions you want to inspect.

#### Client

To modify the client side, `Figwheel` is super awesome. Run `lein figwheel` and `stylus -w src/css -o resources/public/css/`. When you save a cljs or styl file, the Clojurescript or Stylus are automatically compiled and sent to the connected browsers. No need to refresh anything, you keep the game state. When you have an error in a cljs file, the UI might disappear. Don't panic! As soon as the error is corrected the UI will reappear. No need to reload anything.

## Good Starting Issues

Here are some easier / less complicated issues from our Issue Tracker than newer developers can look into in order to better learn the code base with an active example:

* [#428](https://github.com/mtgred/netrunner/issues/428) - Reina Roja's ability is lost if the Corp attempts to rez ice they can't afford.
* [#383](https://github.com/mtgred/netrunner/issues/383) - hosting a 0-cost card onto Personal Workshop should auto-install it.
* [#344](https://github.com/mtgred/netrunner/issues/344) - Order of Sol needs to fire at start of turn if the Runner has 0 credits; currently only fires when dropping to 0 credits.
* [#322](https://github.com/mtgred/netrunner/issues/322) - add a "Thinking..." button to the UI. At the simplest, could simply print "____ is thinking" to `(system-msg)`.
* [#303](https://github.com/mtgred/netrunner/issues/303) - Shi.Kyu does not get added to Runner's score area if access from a remote server.

(Updated 17 June 2015.)