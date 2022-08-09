**note** I wrote this on slack and have copied it directly here with minimal editing due to time constraints. 

Constant effects are an attempt to create a "declarative" system to jnet. They are roughly modeled after Static Abilities in the various CRs. They are stored on the state under `:effects` I think, and their general shape is, to borrow Malli's schema syntax (which I should really adopt in the engine):

```clojure
(def Effect
  [:map 
   [:type keyword?]
   [:req {:optional true} fn?]
   [:value [:or fn? integer?]]
   [:duration keyword?]
   [:card [:maybe Card]]
   [:uuid uuid?]])
```

I say "roughly modeled" because they're not just Static Abilities. Static Abilities are like "+1 [mu]" or Puffer's "This program gets +1 strength and costs +1 [mu] for each hosted power counter." and they don't have durations

They're also intended to cover Lingering Effects, which are created by abilities that outlive the source. For example, Pelangi's paid ability says "Hosted virus counter: Choose an ice subtype. The ice you are encountering gains that subtype for the remainder of the encounter." Even if Pelangi is trashed or uninstalled or flipped facedown during the encounter, the ice will still have the chosen subtype.

Lingering Effects do have durations

So we have this :effects entry on state and it holds effect maps with :duration :constant as well as :duration :whatever. The :duration :constant effects are intended to model Static Abilities and are only ever created from calling register-constant-effects in initialization logic (card-init, hosting, enabling an identity (after it's been disabled), etc). The :duration :whatever effects are intended to model Lingering Effects, and can be created whenever using the poorly-named register-floating-effect function.

I'll note that I've also been using the effect system as a way to track/catalog various flag-like abilities in the game. Some of these are explicitly Static Abilities like the constant effect :cannot-win-on-points in The Black File's ability map. Some of these are less explicit, like Eden Fragment's "Ignore the install cost of the first piece of ice you install each turn." Is that a Static Ability? Maybe, I have no idea. But using the effect system for it is really nice

The main way that these are used is +mu, +link, ice/program strength raising and lowering, and program's mu cost

you the dev interact with them by calling the various functions in the game.core.effects namespace. The whole system was initially built to deal with ice strength and breaker strength (see [the 5k diff PR](https://github.com/mtgred/netrunner/pull/4434) for more details lol), as previously we had a terrible system for handling changed values (won't even begin to try to describe it), but since then it's grown as I've gotten more comfortable with the concept

the non-register/unregister functions are pretty simple: gather-effects returns all effects of a given :type, sorted by active player. get-effect-maps returns all effects of the given :type that have truthy :reqs. get-effects returns the :values of a get-effect-maps call. sum-effects sums the numeric values from get-effects. and any-effects checks to see if there is at least 1 get-effects value that matches the given predicate