Hello /r/netrunner! We are the Jinteki.net developer team. Ask us anything!

Frequent contributors:

* /u/mtgred ([GitHub](https://github.com/mtgred/)), London, UK (formerly Brussels, Belgium). Professional software developer. Project founder and leader, system architecture, server maintenance. 
* /u/JoelCFC25 ([GitHub](https://github.com/JoelCFC25)), Minneapolis, USA. GIS analyst working in local government. Card implementations, community management, quality assurance. Patron of the unloved card, master of the 20-fix pull request.
* /u/nealpro ([nealterrell](https://github.com/nealterrell)), Los Angeles, USA. University instructor, computer science. Core engine development, system architecture, card implementations. Likes infinite recursion a little too much.
* /u/Saintis ([GitHub](https://github.com/Saintis)), Cambridge, UK (originally Sweden). Studying for a Master in Engineering. User interface, code maintenance and refactoring. Master of D.R.Y. 
* /u/htoraz ([Zaroth](https://github.com/zaroth)), Warsaw, Poland. User interface, build configuration and maintenance, card implementations. Don't let him catch you favoring `git merge` over `git rebase`.
* /u/erbridge ([GitHub](https://github.com/erbridge)), London, UK. Professional software developer. User interface, server configuration, core engine development. Likes to flex (layout). "What is  this 'reddit' thing?"
* /u/neuro8 ([domtancredi](https://github.com/domtancredi)), New York City, USA. CEO/CTO Dom & Tom. Tech Lead/Professional web app dev. User Interface. Favorite saying: "I think it's a z-index issue..."

Other notables:

* /u/queueseven ([GitHub](https://github.com/queueseven)), Germany.
* /u/kevkcc ([GitHub](https://github.com/kevkcc)), Vancouver, Canada. Software Engineer. Communications student.  Card implementations, minor engine development, unit test champion. Simply a fan of the game.

### General architecture

We use three primary technologies to run Jinteki.net:

1. A web server in [Node.js](http://nodejs.org) via [CoffeeScript](http://coffeescript.org/). Handles all normal web requests and provides API endpoints for card data and other sources for the UI. All commands a user performs in their browser are routed via a WebSocket through the web server; many are simply handled ("post a chat message", "save a deck"), others are passed to the game server ("user X plays card Y").

2. A game server in [Clojure](http://clojure.org). Game commands are passed from the web server to the game server over a [ZeroMQ](http://zeromq.org/) socket. The game server applies each command to a given game's state, which is kept in internal memory on the game server. A new state is returned to the web server, which sends it back over a WebSocket to the waiting client browsers.

3. A user interface in [ClojureScript](https://clojurescript.org/) via [Om/React](https://github.com/omcljs/om). Parts of the web UI is actually in [Jade/Pug](https://github.com/pugjs/pug) for general templating and layout, but the important UI bits are in ClojureScript. This includes the chat/game lobby UI and the game board itself. Actions in the ClojureScript UI send commands to the web server, which then get routed to the game server.

So when you click to play a card from your hand...

* ClojureScript handles the click event in your browser, determines it was over a card in your hand, and sends a "play card X" command to the web server.
* The web server sees that the command is a game state command, and forwards it to the game server.
* The game server validates the command, finds the logic for the referenced card, and executes the game logic for playing that card. A new game state is returned to the web server.
* The web server returns the new game state to the browser.
* ClojureScript renders the new game state, showing the effect of playing your card.