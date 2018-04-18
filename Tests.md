When you create a new card, you want to create at least one test to go along with it, that verifies that the card works.
This allows us to make sure that when we change core functionality or modify a given card, it will continue to work the
same as it did before the change. In this way, we can guarantee we aren't regressing when fixing bugs or implementing
new behavior.

## Why do we test?

[Read the wiki!](https://en.wikipedia.org/wiki/Software_testing)

# How to run tests

The most basic way to run the tests is `lein test`. This will compile and run all existing tests in the entire suite. If
you wish to only run the tests of a given kind, `lein test game-test.cards.agendas` (substituting whatever card kind you
wish to test for `agendas`). If you wish to only run a single test, `lein test :only
game-test.cards.agendas/fifteen-minutes` (once again substituting whatever test you wish to test for
`agendas/fifteen-minutes`).

If you want to get fancy with it, [lein-test-refresh][lein-test-refresh] will automatically
rerun the entire testing suite or only changed tests (as determined by the `:test-refresh` map you have in your
profiles.clj). Word of warning, if you change a non-test file, you have to restart test-refresh; but as long as you're
only writing tests, it'll work as expected.

[lein-test-refresh]: https://github.com/jakemcc/lein-test-refresh

# How to write tests

First I'll talk about what one should consider when writing a test, and then we'll step through how clojure's testing
system works and what utilities are available to us in jnet. Afterwards, we'll look at some existing tests and then
write our own! It's gonna be great, so hang in there if you find yourself struggling.

## High level concepts

I'll let you know that entire books have been written on this subject, so if you feel lost or like you're struggling,
you're not alone. Generally, there are a couple guidelines to writing a good test or set of tests for a piece of
functionality but know that this is by no means exhaustive nor is anything beyond the first test required for card
implementation. However, _all tests_ are helpful and positive, so if you wish to, please _please_ go for it and help us
out.

### Guidelines:

* Write for basic functionality: Write the expected use-case and expected outcome as the first test. If you're writing
    a test for a card, write what happens when it's played normally and used normally, without any other cards present.
    Test ability and effect requirements, and verify that it starts with the correct number of counters if it has
    those.
* Write for boundary-checks: If your card has counters, write a test for how it functions when it has a variable number
    of them (`0, 1, starting amount, large number` is a good starting place). If your card draws cards or gains credits,
    verify that you gain the correct amount for a variety of inputs. (For example, Estelle Moon should check that she
    gains and draws the correct amount when she has 0 counters, 1 counter, and maybe 5 counters.)
* Write for complicated interactions: if your card has timing issues, find other cards that interact with the same
    timing issues and write a test to verify that your card behaves correctly in their presence. If your card's effect
    can be prevented or prevents other effects, write a test to make sure it happens as expected. If your card gains or
    loses counters, verify that cards that modify counters of the same kind correctly identify and modify your card. In
    this way, you can better verify that your card doesn't just function how you expect it does, but functions under
    high-complexity situations.

## Short-form description

Clojure's basic testing system works like this: you write top-level `deftest` functions and fill them with asserts
(pretty much only `is`, in our case). You can wrap functionality blocks in `testing` calls, writing a short comment
about what this particular block is testing (as it will appear in the test print-out on a failed assertion). Example:

```clojure
(deftest example-test
  (is (= 4 (+ 2 2)) "Should be 4")
  (testing "multiplication"
    (is (= 6 (* 2 3)))))
```

Jnet's testing system is slightly more complicated, but for the most part works like this: you write each card test in a
`do-game`, first call `new-game` with either `default-corp` and `default-runner` or specifying what cards/ids you want
the test to use. Then you write out the actions the game should perform for the two sides, as if the game is actually
being played (so, corp always goes first, corp has 3 clicks and runner has 4, etc). Everything can be modified, but _by
default_, `do-game` is the same as a regular game you'd play yourself.

However, the functions and macros needed to make that work can take some time to learn and the docs in the code aren't
great, so I've included a short description of the most-used ones at the end of this page.

## Card examples

We'll walk through the thought-process for writing a couple different tests. These will start off simple and grow more
complex. For each, I'll post the whole test up front, and then talk about the decisions made to help get you into the
necessary mindset.

### Hostile Takeover

```clojure
(deftest hostile-takeover
  ;; Hostile Takeover
  (do-game
    (new-game (default-corp [(qty "Hostile Takeover" 1)])
              (default-runner))
    (let [credits (:credit (get-corp))
          bp (:bad-publicity (get-corp))]
      (play-and-score state "Hostile Takeover")
      (is (= (+ 7 credits) (:credit (get-corp)) "Gain 7 credits")
      (is (= (+ 1 bp) (:bad-publicity (get-corp))) "Take 1 bad publicity"))))
```

This one's pretty simple: we're just testing the [happy path][happy-path]. Start a new game, play Hostile Takeover from
hand and score it, and then verify that we gain 7 credits and 1 bad publicity. I use a `let` binding to make sure that
regardless of the implementation of `play-and-score`, I gain the correct amount. If I instead wrote `(= 12 (:credit
(get-corp)))` and later `play-and-score` is changed to use credits, the test would fail even tho the card is correctly
written.

[happy-path]: https://en.wikipedia.org/wiki/Happy_path

WORK IN PROGRESS

## Documentation

### Set up

* `do-game`: This is a macro that binds a bunch of useful functions:
    * `state`: This is the thing the entire game is built on. It's an object that holds all information about the
        'state' of the game at each moment.
    * `get-corp` and `get-runner`: Must be called without any parameters: `(get-corp)` This returns the side object for
        use in other functions.
    * `refresh`: pass in a card, and it'll get the latest "version" of that card from the game state. Necessary when
        modifying counter values, rez status and other such stuff.
    * `prompt-choice` and `prompt-select`: These are used when the game prompts the user to make a decision: either
        a "Yes" or "No" to trigger an optional ability, or select a card from a zone. Each takes a `side`
        parameter to indicate which side is interacting with the prompt, and a parameter to choose or select
        depending on the prompt type.
        * `prompt-choice` is used when the prompt in question gives you a list of items to choose from,
          for example choosing whether to run "R&D" or "HQ" etc. In this case the second parameter will be a
          string matching the string found on the button on the GUI or in the `:choices` map of the prompt.
        * `prompt-select` is used when the prompt gives you a targeting reticule to select a card. In this case
          the second parameter should be a `card` item that you can get with `find-card` or the relevant
          `get-program`, `get-ice` etc.
* `new-game`: This function initializes a new game, setting up the corp and runner and allowing you to determine certain
    pre-game criteria such as mulligans or NEXT Design's ice placement. It has two necessary parameters, `corp` and
    `runner`, which are usually built by `default-corp` and `default-runner` but can also be built by `make-deck`.
* `default-corp` and `default-runner`: These functions can be called with 0 parameters, which will initialize them as
    Custom Biotics with 3 Hedge Funds, and The Professor with 3 Sure Gambles. The reason these ids are chosen is because
    they have no game-relevant abilities. However, they can be called with the 1 parameter, a vector of cards that make
    up the deck. (Reminder, deck-building restrictions have no effect in game and can be ignored.)
* `make-deck`: takes an identity string (full name of a corp or runner), and a vector of cards (can be card objects or
    strings) and returns a player deck (either corp or runner based on the id).
* `qty`: This marvelous 2-function takes in a card name string and a quantity, and returns the card object with that
    quantity. Makes writing decks very easy.

### Gameplay

* `starting-hand`: 3-function that takes state, side, and a vector of cards by string, and moves the cards in hand and
    deck around such that the hand holds the listed cards.
* `take-credits`:  2-function that takes state, side. This is the default way to end a given turn when writing tests.
    It will spend all remaining clicks for the side provided clicking for credits, trigger end of turn, and trigger
    start of turn for the other side.
* `find-card`: Given a string, find the matching card in a sequence (hand, deck, etc). Example: `(find-card "Hedge Fund"
    (:hand (get-corp)))'`
* `play-from-hand`: 4-function of state, side, a card string and a server (for corp) that plays the card from hand.
    Spends clicks and credits necessary to play it.
* `card-ability`: 5-function of state, side, card, ability, and optional targets that uses the ability by position,
    pointed at whichever targets are specified (as needed).
* `get-[...]`: There are several helper functions defined in the test `core` file that let you get cards in the rig or in servers by position. Each takes `state` and `pos`, the second which is a number indicating the index of the installed card (so first installed resource could be got with `(get-resource state 0)`). Some also offer a function just taking in `state` which returns _all_ the relevant cards (e.g. `(get-program state)` to retrieve all installed programs).

Corp:

* `advance`: 3-function of state, card, and optional n for advancing a given card. Spends clicks and credits.
* `score-agenda`: 3-function of state, side, and card string that gains and uses credits and clicks necessary to score
    agenda. Spends no clicks or credits.
* `play-and-score`: 2-function of state and card string that plays an agenda from hand and scores it. Spends 1 click and
    no credits.

Runner:

* `run-on`: 2-function of state and server. Initiates a run on given server. (Syntactic sugar for `core/click-run state
    :runner {:server server}))`.
* `run-continue`: 1-function of state for when the corp is asked if they wish to take an action (in between ice or
    before accessing cards).
* `run-successful`: 1-function of state for triggering successful run events and automatically accessing cards.
* `run-jack-out`: 1-function of state for triggering a jack-out at the earliest possible moment.
* `run-empty-server`: 2-function of state and server that makes a successful run on the server, assuming no ice.
* `run-phase-43`: 1-function of state that sadly is misnamed after last year's run timing changes. This is for asking
    each player for triggered abilities at what used to be phase 4.3.
* `play-run-event`: 3-function of state, card, and server that plays the event aimed at the server, and advances the run
    timing to the replace-access trigger.