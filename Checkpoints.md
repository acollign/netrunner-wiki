Introduced in CR 1.0, "A ​CHECKPOINT​ is a timing point wherein cards or counters that have entered an illegal state are corrected, expired effects are removed, and other important conditions are checked." I'd like to introduce something similar to jnet, where we can automatically update the game state in a consistent fashion.

For example, Ice and Icebreaker strength are handled by `(update-all-ice state side)` and `(update-all-icebreaker state side)`, which iterate over all of their respective cards and update their the `:current-strength` value on the card object. However, we have to call both of those a lot to make sure that given values are being updated properly.

Likewise, various abilities create both static/constant effects and event handlers, which are only destroyed manually. We have to call both `(unregister-floating-events state side :x)` and `(unregister-floating-effects state side :x)` whenever we pass specific timing structure points, or else they will continue to pollute the game state (affecting things like card strength).

Emulating/building on the ideas in the CR about checkpoints, I propose that we convert _many_ of our existing systems to use the effects and events systems, and then create a new function that performs these automatic checks for us. Then, instead of having to manually write any of the above, we place calls to this new function inside of `trigger-event` and `resolve-ability` in the right places to call things for us, so that a given card will always be up-to-date.

This increases the computational workload of a given ability/call, but jnet is _extremely_ light computationally, and I'm not worried about that aspect at all.

---

Things to check:

- [x] Ice Strength
- [x] Icebreaker Strength
- [ ] Agenda points (per card and per player)
- [ ] Memory
- [ ] Hand size
- [ ] Run endings
- [ ] Tags
- [ ] Bad Publicity
- [ ] Advancement Counters (Satellite Grid)