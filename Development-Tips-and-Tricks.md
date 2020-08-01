## Development tips

#### Launching the Game Server

Start the REPL with `lein repl`.


#### Server

When you modify a function or a card, simply reset the card definitions in the REPL and it should be reflected in the game engine without having to recreate a new game:

    (core/reset-card-defs)

For cards already installed you have to drag it back to hand and install it again to reflect the new code. No need to refresh the browser, you can keep the game state.

Changes to core game functions will require restarting the REPL.

To inspect the content of Clojure data structures, `prn` is handy. You can add `prn` in your functions or inside the `(req )` macro in card definitions you want to inspect.

Once a game is started, execute:

    (def state (:state (second (first @all-games))))

You can then use `state` to inspect the current game state, the same as if you were in a card our core function. Execute the def again when you make a new game.

As an example, you can then run

    (pprint (core/all-installed state :corp))

to list all installed corp cards.

#### Client

To modify the client side, `Figwheel` is awesome. Run `lein figwheel` and `stylus -w src/css -o resources/public/css/`. When you save a .cljs or .styl file, the Clojurescript or Stylus is automatically compiled and sent to the connected browsers. No need to refresh anything, you keep the game state. When you have an error in a .cljs file, the UI might disappear. Don't panic! As soon as the error is corrected the UI will reappear. No need to reload anything.

#### Tests

To run the unit tests: `lein test`

Tests are in the `game-test` namespace. To run selected unit tests: `lein test game.cards.agendas-test`

To combine test and repl run: `lein clean && lein test && lein repl`. This makes sure no compiled files are left from a branch switch or similar, and only starts the repl if all the tests pass.

#### Fetching cards while NRDB is down (WIP)

John updated the `fetch` script to read from local json files in this branch: https://github.com/jwarwick/netrunner/tree/local_nrdb

Ensure mongod is running, then:
`git clone https://github.com/gereons/netrunner-cards-json.git`

`git remote add jwarwick https://github.com/jwarwick/netrunner.git`

`git fetch jwarwick`

`git checkout jwarwick/local_nrdb`

`lein fetch --local <path to netrunner-cards-json> --no-card-images`

`git checkout <your own branch to begin work again>`