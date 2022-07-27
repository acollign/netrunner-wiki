This page contains all the information you need to get started with developing as well as useful tips.

## Language

The main language the whole project is written in is Clojure. Below are a few links to useful learning resources and documentation of Clojure and libraries used:

- [Clojure for the Brave and True](http://www.braveclojure.com/) - a nice online crash course of Clojure, written in accessible language
- [ClojureDocs](https://clojuredocs.org/core-library) - online Clojure documentation
- [Reagent](https://reagent-project.github.io/) - ClojureScript interface to Facebook's [React](https://facebook.github.io/react/index.html). Used in many places on the web presentation side.
- [Hiccup](https://github.com/weavejester/hiccup/wiki/Syntax) - describes the HTML templating libraries used in the `.cljs` files.

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
git switch -c your_new_branch_name
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


## Installation and Running the App

Install JavaScript dependencies:

```
$ npm ci
```

### Launch MongoDB

Possibly with --dbpath option specifying card data directory:

```
$ mongod
```

On Windows, run `mongod.exe`. See [Install MongoDB on Windows](http://docs.mongodb.org/manual/tutorial/install-mongodb-on-windows/).

### Fetch card data from NetrunnerDB

```
$ lein fetch
```

This data fetch only needs to be performed if it's your first time building the project OR new card data has been made available on NetrunnerDB and you want to update your local data (e.g., a new data pack). 

If you don't want card images to be downloaded (such as on a testing environment), you can run `lein fetch --no-card-images`. If you have the card data saved locally, use `lein fetch --local ../path/to/data`.

### Compile client side ClojureScript

Only necessary if this is your first time building the project OR any .cljs files have been edited/modified and you need to update the UI:

```
$ npm run cljs:build
```

### Compile CSS files

This step can be skipped if you have no plans to modify CSS/layout items.

```
$ npm run css:build
```

### Compile and launch server

Start server in dev mode, and open a REPL prompt:

```
lein repl
```

### Visit App in Browser

Open one or more browser sessions and visit: http://localhost:1042

## Notes on REPL-driven development

Using `lein repl` compiles the project if it's out of date, then launches an interactive shell from which you can type and evaluate Clojure commands. Once you have a game initiated, you can modify card code and execute the `defcard` form to load the changed code. To see changes reflected in an ongoing game, simply trash/discard a copy of the card in question and reinstalling it or playing it again to see the new behavior.

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
(def state (:state (second (last (:lobbies @web.app-state/app-state)))))
```

You can now access the state of your game as you are playing it by using the variable `@state`. For example, try running `(get-in @state [:corp :hand])` to see the corp's current hand, or `(->> (get-in @state [:corp :hand]) (map :printed-title))` to filter for the card titles.

> So, I have changed a card, now what?

Evaluate `defcard` with your new card definition in the REPL. For example, to make Akamatsu give 10 MU, evaluate
```
(defcard "Akamatsu Mem Chip"
  {:constant-effects [(mu+ 10)]})
```
Note that if the card you changed is already installed, you have to play it again before the changes take effect.

## Using Emacs with Cider

If you want to use Emacs you have to install clojure-mode and cider. Cider allows you to connect Emacs to a REPL with C-c M-j. To reload a function, it's C-c C-c with the cursor inside the function definition. To reload a file (eg. card.clj) it's C-c C-k.

## Slack channel

The main communication channel between developers, besides GitHub, is the [team's Slack Channel](https://jinteki.slack.com/). To request access, send an email to [NoahTheDuke](mailto:noah.bogart@hey.com) stating who you are on GitHub and linking an open PR. 

## Good Starting Issues

If you want to look for some of the easier issues to get started, look through our GitHub issues for [issues labeled **easy**](https://github.com/mtgred/netrunner/labels/easy). These usually don't require in-depth codebase knowledge, and changes required to fix them shouldn't involve more than 1-3 source files.

## Continuous Integration

The project is configured to integrate with [CircleCI](https://circleci.com). Create a CircleCI account and create a new project linked to your Github fork of this project. CircleCI will automatically build and run tests when you push changes to your repo.