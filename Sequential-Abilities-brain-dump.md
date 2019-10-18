Follow-up to [this page](https://github.com/mtgred/netrunner/wiki/Making-wait-completion-easier---ramble,-might-become-an-idea-that-something-could-be-done-with-(WIP)). Most of this is straight from slack, so as to not forget.

---

NoahTheDuke:

I know how we can do the “sequence of awaitable actions” thing

Write a function that takes a sequence of functions or maps, wait-for iterate over them, if it’s a function, wrap it in an {:effect function}, if it’s a map do nothing. Wait-for (resolve-ability new-map), through the whole list.

So for simple stuff you just say [(effect (damage :runner eid :net 1)) (effect (draw :corp 1 nil))] and it’ll do both async

For more complicated stuff, put a whole map in there or put logic inside, or put a normal function and call effect-completed at the end

Cuz it’ll assume each map is async

Having written that out, I feel like it won’t quite work for most of our deeply nested abilities, cuz we’re almost always nesting targeted abilities, and this misses out on passing that information around

---

with regards to the targets issue, an idea i had is to create a context atom in the enclosing scope of the :sequence thing, and then we could just swap! information in as necessary

```clojure
   "Casting Call"
   {:sequence
    (sequence-wrapper
      [{:choices {:req #(and (agenda? %)
                             (in-hand? %))}
        :effect (req (swap! context assoc :agenda target))}
       {:prompt (str "Choose a server to install " (:title (:agenda @context)))
        :choices (installable-servers state (:agenda @context))
        :effect (req (corp-install state side (:agenda @context) target {:install-state :face-up})
        ; find where the agenda ended up and host on it
        (let [agenda (some #(when (same-card? % (:agenda @context)) %)
                              (all-installed state :corp))]
          (host state side agenda (assoc card
                                         :seen true
                                         :installed true))
          (system-msg state side (str "hosts Casting Call on " (:title agenda)))))}])
   ...}
```
---

like `(complete-with-result state side target)` and then the sequence-wrapper would do something like `(wait-for (resolve-ability ...) (sequence-wrapper-impl state side (rest abilities) async-result))`

---

when we call `resolve-ability` on a `:choices` ability, we don't have access to the `target` outside of it. `resolve-prompt` or `select` is called with the clicked-on card (our target), and that's passed into the `resolve-ability`, which then passes it to the `do-effect` which actually calls the `:effect` portion of our code, and then it just... drops it all into the trash lol

so for any `:choices` prompt, we'd have to say `{:choices {:req #(...)} :effect (effect (complete-with-result eid target))}` and then in the caller say `(let [target1 async-result] (sequence-wrapper-impl`, like you've posted above

but that still wouldn't quite be available, hmmmm