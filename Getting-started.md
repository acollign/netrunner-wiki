## IDE

There are several options avaiable, including:

- [Intellij IDEA](https://www.jetbrains.com/idea/) with [Cursive](https://cursiveclojure.com/)

## Dependencies

You need the following dependencies installed:

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

Install JavaScript dependencies:

```
$ bower install
```

Launch MongoDB and fetch card data:

```
$ mongod
$ cd data
$ coffee fetch.coffee
```

On Windows, run `mongod.exe`. See [Install MongoDB on Windows](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/).

Compile and watch client side Clojurescript files:

```
$ lein cljsbuild auto dev
```

Compile server side Clojure files:

```
$ lein uberjar
```

Launch game server:

```
$ java -jar target/netrunner-0.1.0-SNAPSHOT-standalone.jar
```

Launch the Node server:

```
$ coffee server.coffee
```

## Setting up Intellij IDEA 

comming soon

## Debugging with Intellij IDEA 

comming soon