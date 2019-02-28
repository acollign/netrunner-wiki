This page contains all the information you need to get started with developing as well as useful tips.

## Language

The main language the whole project is written in is Clojure. Below are a few links to useful learning resources and documentation of Clojure and libraries used:

- [Clojure for the Brave and True](http://www.braveclojure.com/) - a nice online crash course of Clojure, written in accessible language
- [ClojureDocs](https://clojuredocs.org/core-library) - online Clojure documentation
- [Reagent](https://reagent-project.github.io/) - ClojureScript interface to Facebook's [React](https://facebook.github.io/react/index.html). Used in many places on the web presentation side.
- [Hiccup](https://github.com/weavejester/hiccup/wiki/Syntax) and [Ŝablono](https://github.com/r0man/sablono) guides - they describe the HTML templating libraries used in the `.cljs` files.

## IDE

There are several options available, including:

- [IntelliJ IDEA](https://www.jetbrains.com/idea/) with [Cursive](https://cursiveclojure.com/)
- Emacs ([Unix](https://www.gnu.org/software/emacs/) | [Windows](https://ftp.gnu.org/gnu/emacs/windows/) | [MacOS](http://emacsformacosx.com/)) with [CIDER](https://github.com/clojure-emacs/cider)

## Dependencies

You need the following dependencies installed:

- [Java8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) (for MacOS, download the full [JDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html))
- [Node.js](https://nodejs.org/download/), Node Package Manager (make sure `npm` is in your `PATH`)
- [Leiningen (version 2+)](http://leiningen.org/)
- [MongoDB](https://www.mongodb.org/)
- [Bower](http://bower.io/) (`npm install -g bower`)
- [Stylus](https://learnboost.github.io/stylus/) (`npm install -g stylus`)

*[Here's a guide for Windows specifically](https://github.com/mtgred/netrunner/wiki/Setting-up-Jnet-Development-on-Windows)*

## Note on Mac
You can install nearly everything through Homebrew except the NPM modules
`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

Then:
* `brew tap caskroom/versions`
* `brew cask info java8`
* `brew cask install java8`
* `brew install clojure`
* `brew install node`
* `brew install mongo`
* `brew install leiningen`

## Note on Java
You should use Java8/JDK8.  Newer machines tend to come with Java10 which is initially a problem.

There are compile time issues with Java9 and 10 with our Clojure project that can be resolved - however produce impacts to Java8 users ... so you would have to maintain your own project.clj file and not commit it to the main project.  

The issue looks to be addressed in Clojurescript 1.10 but not in Clojure (yet) ... so use JDK8 ;0

## Git

If you want to contribute, you should fork the mtgred/netrunner repository on github. For a review of basic git commands, visit http://rogerdudler.github.io/git-guide/

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

### Suggested new branch workflow:
1. `git checkout master`
1. `git pull upstream master`
1. `git checkout -b features`
1. `echo "cool feature" > feature.txt`
1. `git commit -am "Added cool feature"`
1. `git checkout master`
1. `git pull upstream master`
1. `git checkout feature`
1. `git rebase master`
1. `git push origin feature`


## Installation

Install Clojure dependencies:

```
lein deps
```

---
Install JavaScript dependencies:

```
$ bower install
```
## Get Up and Running
**1. Launch MongoDB** _(possibly with --dbpath option specifying card data directory)_

```
$ mongod
```

On Windows, run `mongod.exe`. See [Install MongoDB on Windows](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/).

**2. Fetch card data from NetrunnerDB**

```
$ lein fetch
```

This data fetch only needs to be performed if it's your first time building the project OR new card data has been made available on NetrunnerDB and you want to update your local data (e.g., a new data pack). 

If you don't want card images to be downloaded (such as on a testing environment), you can run `lein fetch --no-card-images`.

---
**3. Compile and watch client side ClojureScript** _(only necessary if this is your first time building the project OR any .cljs files have been edited/modified and you need to update the UI)_:

```
$ lein cljsbuild once dev
```
---
**4. Compile and watch CSS files** _(this step can be skipped if you have no plans to modify CSS/layout items)_:

```
$ stylus -w src/css -o resources/public/css/
```
---
**5. Compile server side Clojure files**
```
$ lein uberjar
```
---
**6. Launch server:**

EITHER:

1. `java -jar target/netrunner-standalone.jar` -- boot up server in production mode (requires `lein cljsbuild once prod` first)
2. `lein run` -- alternative to above
3. `lein run dev` -- start server in dev mode
3. `lein repl` -- start server in dev mode, and open a REPL prompt. **(RECOMMENDED, SEE BELOW.)**

---
**7. Run browser(s)**

Open one or more browser sessions and visit: http://localhost:1042

### Minimalist Alternative to Steps 5 and 6

Instead of building the production JAR files and running them with Java, a much faster way of launching the game server is to run the REPL (read-eval-print-loop) directly from the command line: 

```
$ lein repl
```

This compiles the project if it's out of date, then launches an interactive shell from which you can type and evaluate Clojure commands. Inside the REPL, do `(future-call -main)` to launch the game server. Once you have a game initiated, you can modify card code and reload it with `(core/reset-card-defs)` and very quickly see changes reflected in an ongoing game by simply trashing/discarding a copy of the card in question and reinstalling it or playing it again to see the new behavior.


## Automated Environment

To get a local copy running with one command, see https://github.com/astrostl/nrdev

## Setting up IntelliJ IDEA 

> How can I use a REPL while running the server?

Create a new Run Configuration and choose `Clojure REPL -> Local`:

![Setting a REPL](https://i.imgur.com/Df7h756.png)

When the REPL started, use `(future-call -main)` to start the game server.

## Using IntelliJ IDEA with Cursive

> I start a REPL from IntelliJ IDEA, but I get the following error: 'No nREPL ack received'.

Cursive uses a default time out of 60 seconds when trying to connect to the REPL. Change `Settings -> Clojure -> REPL startup timeout` to a higher value:

![Increase the timeout](https://i.imgur.com/2OlGHtA.png)

> How can I access the code in the REPL?

Right click the file in the editor, `REPL -> Switch REPL NS to current file` or run `(in-ns 'game.core)` to switch the namespace


> How do I run the tests?

Run `lein test` from the command line.

> I have the REPL up and running, now what?

Go to your browser and start a game. Now, from the REPL prompt, run

```
(def state (:state (second (last @all-games))))
```

You can now access the state of your game as you are playing it by using the variable `@state`. For example, try running `(get-in @state [:corp :hand])` to see the corp's current hand.

> So, I have changed a card, now what?

Run `(reset-card-defs)` to reload all cards (or `(reset-card-defs "X")` where X is the card type filename (such as "agendas")). Note that if the card you changed is already in play, you have to play it again before you see your code changes.

## Using Emacs with Cider

If you want to use Emacs you have to install clojure-mode and cider. Cider allows you to connect Emacs to a REPL with C-c M-j. To reload a function, it's C-c C-c with the cursor inside the function definition. To reload a file (eg. card.clj) it's C-c C-k.

## Videos (CURRENTLY BEHIND A PAYWALL)

These live-coding videos may help you with some of the basics:

* [Brief introduction / Implementing Tenma Line](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)
* [Events system overview / Implementing Utopia Shard](https://www.livecoding.tv/video/jintekinet-events-utopia-shard-12/)
* [Resolving abilities / Implementing Howler](https://www.livecoding.tv/video/jintekinet-resolving-abilities-howler/)
* [Testing framework / Fixing a MaxX bug](https://www.livecoding.tv/nealpro/videos/E7eo3-jintekinet-testing-framework-fixing-a-bug)
* [Synchronized resolution of triggers / Fixing a Bio-Ethics Association bug](https://www.livecoding.tv/nealpro/videos/z8o6Y-jintekinet-resolution-of-simultaneous-triggers)

## Slack channel

The main communication channel between developers, besides GitHub, is the [team's Slack Channel](https://jinteki.slack.com/). To request access, send an email to [mtgred](mailto:mtgred@gmail.com) stating who you are on GitHub and linking an open PR. 

## Good Starting Issues

If you want to look for some of the easier issues to get started, look through our GitHub issues for [issues labeled **easy**](https://github.com/mtgred/netrunner/labels/easy). These usually don't require in-depth codebase knowledge, and changes required to fix them shouldn't involve more than 1-3 source files.

Do note that Pull Requests affecting the user interface will likely take much longer to get accepted.

## Pull requests and branches

There are two main branches in this repository, `master` and `dev`. **TODO**

## Continuous Integration

The project is configured to integrate with [CircleCI](https://circleci.com). Create a CircleCI account and create a new project linked to your Github fork of this project. CircleCI will automatically build and run tests when you push changes to your repo.