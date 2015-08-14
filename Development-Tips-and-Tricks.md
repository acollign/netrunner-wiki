## Development tips

#### Server

When you modify a function or a card, simply reload the function or the `cards.clj` file to the REPL and it should be reflected in the game engine without having to recreate a new game:

    (load-file "src/clj/game/cards.clj")

For cards already installed you have to drag it back to hand and install it again to reflect the new code. No need to refresh the browser, you can keep the game state.

To inspect the content of clojure data structure, `prn` is handy. You can add `prn` in your functions or inside the `(req )` macro in card definitions you want to inspect.

Once a game is started, execute `(def state (second (first @game-states)))`. You can then use `state` to inspect the current game state, the same as if you were in a card our core function. Execute the def again when you make a new game.
#### Client

To modify the client side, `Figwheel` is super awesome. Run `lein figwheel` and `stylus -w src/css -o resources/public/css/`. When you save a cljs or styl file, the Clojurescript or Stylus are automatically compiled and sent to the connected browsers. No need to refresh anything, you keep the game state. When you have an error in a cljs file, the UI might disappear. Don't panic! As soon as the error is corrected the UI will reappear. No need to reload anything.
