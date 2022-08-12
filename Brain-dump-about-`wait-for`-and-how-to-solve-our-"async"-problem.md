Our wait-for macro is a big wrapper around the very common and very fucking annoying callback hell style of async from javascript:

```clojure
(wait-for (damage state side :net 4)
  (let [total (:amount async-result)]
    (wait-for (gain-credit state side total)
      (system-msg state side (str "Corp gained " (quantify "credit" total)))
      (effect-completed state side eid))))
```

looks vaguely like this in javascript:

```javascript
damage(state, side, "net", 4, (total) => {
    gainCredits(state, side, total, (asyncResult) => {
        systemMessage(state, side, `Corp gained ${quantify("credit", total)}`);
    })
})
```
Ours is somehow worse because even tho i added destructuring to wait-for many months ago, i didn't switch anything over and i don't even know if i like it lol

Internally, we're keeping a pointer to these callback functions on state, and as things are finished, we use the provided `eid` to find and call the callback function, which continues the process on its merry way. That means that 1) we're lugging around a lot of anonymous functions/closures, which I'm sure has a memory cost and 2) all of our code has the awful [Callback Hell](http://callbackhell.com/) look, where it's constantly growing to the right and we have to keep track of that stupid ass eid object everywhere.

how to combat this, tho? what's the solution? we can't/shouldn't try to use core.async because this isn't the workflow they're trying to handle

something like async/await from javascript doesn't really work here either because again, that's not what we're dealing with. game state needs to be updated with information, like that someone has gotten a prompt

my "solution" last year was based heavily on ringteki and the way it's built: have this complex pipeline thing that takes in step objects with "execute" functions, and if the execute function returns false, then don't drop the step and wait until you get input from a player, at which point you call the function again with the new input and see what you get back

that's sort of cool but is fundamentally not how jnet is built

and trying to hack it into our existing system is tough/impossible

on the other other hand, we have the prompt queue, and we do a pretty good job of opening prompts for when the player needs to do stuff

what is the actual goal here tho? if i somehow hack this all up and repurpose everything so we can use the prompt queue as an adhoc queue of "stuff to do", and then somehow stop relying on eid as a marker for when things are complete, then what? there's still gonna be callback hell, because our system fundamentally is not built to be returning anything from resolve-ability or the like (edited) 

thankfully, we already make sure to put so-called async functions in the tail position. i'm maniacal about it, lol, it's the single biggest issue with dropped and incorrect effects (edited) 

but even so, we'd then have to start juggling shit like card-init not being able to call resolve-effect or whatever it does when a card has a "rez" ability (i don't remember exactly how that works)

