## Development tips

#### Launching the Game Server

Start the REPL with `lein repl`.


#### Server

When you modify a card, use your repl to evaluate the `defcard` block. Because it's a multi-method, all future uses of `card-def` in the engine will use the updated code. For cards already installed you have to drag it back to hand and install it again to reflect the new code. No need to refresh the browser, you can keep the game state.

To inspect the content of Clojure data structures, `println` is handy. You can add `println` in your functions or inside the `(req)` macro in card definitions you want to inspect.

Once a game is started, execute:

    (def state (:state (second (last (:lobbies @web.app-state/app-state)))))

You can then use `state` to inspect the current game state, the same as if you were in a card our core function. Execute the def again when you make a new game.

As an example, you can then run

    (pprint (game.core/all-installed state :corp))

to list all installed corp cards.

#### Client

To watch for CSS changes, run `npm run css:watch`. Also, make sure to run `npm run cljs:watch`. This will start shadow-cljs, which will watch and recompile changes to your `cljs` files. Those changes and css changes will then be automatically injected into your browser, so you should not have to reload anything manually.


#### Tests

To run the unit tests: `lein test` To run a selected unit test: `lein test game.cards.agendas-test`

To combine test and repl run: `lein clean && lein test && lein repl`. This makes sure no compiled files are left from a branch switch or similar, and only starts the repl if all the tests pass.