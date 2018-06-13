Suppose we want to code the following ridiculous card:

Corp Operation: Gain 2cr, then trace2: if successful, give the runner 1 tag, then deal 1 meat damage if the runner is tagged, or gain 2 clicks if the runner is not tagged. 

Note that the card has two effects in sequence:

1. Gain 2cr.
2. Trace.

And that the trace has two effects in sequence:

1. Give the runner 1 tag.
2. Either deal 1 meat damage or gain 2 clicks depending on the runner's tag state.

Let's implement this card in increments, explaining async effects and when to use them along the way. Things start easily enough.

```
{:effect (effect (gain-credits 2))}
```

To initiate the trace, we have to rely on `resolve-ability` to "chain" trigger another ability on top of this one (since an ability can involve an `:effect` or `:trace` but not both). We'll use a `let` to reduce the horizontal nesting of the code, and only code the "give a tag" portion of this effect for the moment.

```clojure
(let [my-trace {:trace {:base 2
                        :successful {:effect (effect (tag-runner 1))}}}]
{:effect (effect (gain-credits 2)
                 (resolve-ability my-trace card nil))})
```

Next we'll implement the "if tagged, do damage", leaving a placeholder for the "else" effect later. Using an `if` inside an `:effect` means we now have to use `(req` instead of `(effect`.

```clojure
(let [my-trace {:trace {:base 2
                        :successful {:effect (req (tag-runner state side 1)
                                                  (if tagged
                                                    (damage state side 1 {:card card})
                                                    *placeholder*))}}}]
{:effect (effect (gain-credits 2)
                 (resolve-ability my-trace card nil))})
```

There are two important problems with this code:

1. `tagged` is a value that is bound whenever we enter a `(req` function; because values in Clojure are immutable, `tagged` reflects the tag state of the runner _before tag-runner executes_. So we need to look directly into the `state` map to find out if the runner is tagged, instead of using the helper value.
2. `tag-runner` is an _async action_, and may not actually be finished by the time we execute the `if`.

### Async actions

An async action is any card ability, event, or core function that requires user input to complete, _or_ trigger such abilities, events, or functions. What's notable about these actions is that they are not necessarily finished by the time Clojure returns from the corresponding function call back to the call site. For example above, `tag-runner` might show a prompt to the runner if they can prevent the tag; if this happens, then the Clojure function `tag-runner` will return back to our card ability and proceed to the next line (the `if`) _while the runner's prevention prompt is still open and waiting_. We don't want this to happen. We want to wait for that async action to finish before proceeding with the next action in the ability.

### Using wait-for

We can wait for `tag-runner` to complete by using `wait-for`. This macro takes two forms: an async action to invoke, and a statement(s) to execute once that action completes. We can use this to fix both issues above:

```clojure
(let [damage-or-gain {:effect (req (if tagged
                                     (damage state side 1 {:card card})
                                     (gain state :corp :click 2)))}

      my-trace {:trace {:base 2
                        :successful {:effect (req (wait-for
                                                    (tag-runner state side 1)
                                                    (resolve-ability state side damage-or-gain card nil)))}}}]
  {:effect (effect (gain-credits 2)
                   (resolve-ability my-trace card nil))})
```

We can use `tagged` because `damage-or-gain` won't be invoked until `tag-runner` completes, so `tagged` will be accurately calculated. `wait-for` ensures that `tag-runner` finishes before we resolve the chained `damage-or-gain` ability.

### Using `:async`

Because the root effect of this card (with the `gain`) "continues" into another ability, the effect itself is not actually complete until the chained ability resolves. Whenever this situation comes up, we must mark the root ability with the `:async true` key, which informs the engine of this situation. This must also be done any time an effect triggers an async action. The root ability continues into another ability; `my-trace` invokes an async action; `damage-or-gain` also invokes an async action (`if` might call `damage` which is async) -- all these abilities need `:async true`.

```clojure
(let [damage-or-gain {:async true
                      :effect (req (if tagged
                                     (damage state side 1 {:card card})
                                     (gain state :corp :click 2)))}
      my-trace {:trace {:base 2
                        :successful {:async true
                                     :effect (req (wait-for
                                                    (tag-runner state side 1)
                                                    (resolve-ability state side damage-or-gain card nil)))}}}]
  {:async true
   :effect (effect (gain-credits 2)
                   (resolve-ability my-trace card nil))})
```

### Triggering `effect-completed`

Any ability that marks itself `:async` is responsible for ensuring that the function `effect-completed` is triggered when the ability has fully resolved itself, including any chained effects or async actions. This card is "complete" in one of three different ways:

1. The trace fails. (automatically handled by the engine)
2. The trace succeeds, damage is dealt, and the damage routine completes.
3. The trace succeeds, and clicks are gained.

The way this card "flows" from one ability to the next is convenient. We note that the root card effect is complete once the `my-trace` ability that gets invoked with `resolve-ability` is complete. To communicate this, we can change `resolve-ability` to `continue-ability`; when used in an ability that is async, this will effectively give the ID of the root ability to the continued ability, so once the continued ability is complete, the root effect is complete as well. 

We can use this function as such:

```clojure
(let [damage-or-gain {:async  true
                      :effect (req (if tagged
                                    (damage state side 1 {:card card})
                                    (gain state :corp ::click 2)))}
      my-trace {:trace {:base 2
                        :successful {:async  true
                                     :effect (req (wait-for
                                                    (tag-runner state side 1)
                                                    (continue-ability state side damage-or-gain card nil)))}}}]
  {:async true
   :effect (effect (gain-credits 2)
                   (continue-ability my-trace card nil))})
```

We're almost done. Our "final" ability (`damage-or-gain`), that the entire card flow ends up at, simply has to trigger `effect-completed` to mark the entire chain as complete. But we can't just call that function at the end of the `(req` in `damage-or-gain`, because that ability isn't over if we go into the true branch until `damage` is finished; but if we go to the false branch, the ability is over immediately after `gain`. So we use two separate tricks to resolve this.

1. In the false branch, call `(effect-completed state side eid)` immediately after `gain`. `gain` is not an async action, so we don't need to "wait" for it to complete... once we get to the next line following the `gain`, we'll know that it finished. The manual `effect-completed` call uses the `eid` "passed on" to the `damage-or-gain` ability by the `continue-ability` in `my-trace`, which received the same `eid` from the root ability's `continue-ability` call; thus, we are really signaling that the original root ability of the card is finished when we call `effect-completed`.

2. In the true branch, we can either use `wait-for` to wait for `damage` to finish and then trigger `effect-completed` manually, as in...

    ```clojure
    (if tagged
      (wait-for (damage state ... )
                (effect-completed state side eid))
    ```

    or, since this need comes up so frequently, we can actually pass the `eid` inherited by `damage-or-gain` to the `damage` function to use as its own `eid`. If we do this, `damage` will trigger the completion of _our_ `eid` when `damage` is complete, which again will signal that the original root ability of the card is finished. This is the preferred approach -- it effectively says "I am done when this async action is done, because my final effect is to cause this action."

The final code (minus any log messages) for our card is then:

```clojure
(let [damage-or-gain {:async true
                      :effect (req (if tagged
                                    (damage state side eid 1 {:card card})
                                    (do (gain state :corp :click 2)
                                        (effect-completed state side eid))))}
      my-trace {:trace {:base 2
                        :successful {:async true
                                     :effect (req (wait-for
                                                    (tag-runner state side 1)
                                                    (continue-ability state side damage-or-gain card nil)))}}}]
  {:async true
   :effect (effect (gain-credits 2)
                   (continue-ability my-trace card nil))})
```

(note the `eid` passed to `damage`, and the `effect-completed` beneath the `do`.