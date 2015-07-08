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
- [Coffeescript](http://coffeescript.org/)
- [Bower](http://bower.io/)

## Installation

Install Node.js dependencies:

```
$ npm install
```

> `npm install` throws errors when trying to compile modules on Windows

You have to specify the `--msvs_version` flag. So, if you use Visual Studio 2013, run

```
npm install --msvs_version=2013 
```

> I'm still getting errors like `error: ‘NewSymbol’ is not a member of ‘v8::String’` when `npm` tries to compile `gyp`

**soon**

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

## Debugging with IntelliJ IDEA 

> I start a REPL from IntelliJ IDEA, but I get the following error: 'No nREPL ack received'.

Cursive uses a default time out of 60 seconds when trying to connect to the REPL. Change `Settings -> Clojure -> REPL startup timeout` to a higher value:

![Increase the timeout](https://i.imgur.com/2OlGHtA.png)

> How can I access the code in the REPL?

Right click the file in the editor, `REPL -> Switch REPL NS to current file`