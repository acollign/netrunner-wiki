ROUGH IDEAS

A card definition is no longer an ability map plus a bunch of other stuff; it's just the other stuff. Cards generally define five categories of things:

1. What to do when certain actions happen to me. This is currently split between `:effect` (when the card is played/installed/rezzed/scored) and various other keys like `:trashed` and `:move-effect`.
2. What to do when certain actions happen to other cards / triggers. This is in `:events` and likely stays that way.
3. Flags for misc interactions.
4. "Data" for counters / MU / recurring credits / other misc state that gets used on install, rez, or at other random times.
5. Abilities/subroutines.

#2 and #3 and #5 should stay as is. #4 should probably be merged into #1, and all those interactions placed inside a new map. I don't know a good name for that map; perhaps `:actions`? Something that invokes an image of "these things happen TO/ON ME".

To differentiate triggers in #1 from triggers in #2, I propose we use ":on-X" as a key corresponding to event X.

A card def then looks something like

```clojure
"Card Name"
{:flags { ... }
 :actions {:on-install { :req :effect etc }
           :on-leave-play { }
           etc}
 :events {:runner-install { }
          :pre-damage { }
          etc}
 :abilities [ ... ]
 :subroutines [ ... ]
}

```

Examples

```clojure
"Adonis Campaign"
{:actions {:on-rez {:effect (effect (add-counter :credit 12))}}
 :events {:corp-turn-begins gain-credits-ability}
 :derezzed-events {:runner-turn-ends corp-rez-toast}
 :abilities [gain-credits-ability]}
```

```clojure
"Muertos Gang Member"
{:actions {:on-install select-a-card-to-derez
           :on-uninstall corp-select-a-card-to-rez}
 :abilities [ trash-to-draw ]}
```

```clojure
"Zona Sul Shipping" (currently broken interaction with Apocalypse?)
{:events {:runner-turn-begins add-counter}
 :actions {:on-activate initialize-watch-for-tagged
           :on-deactivate remove-watch-for-tagged}
 :abilities [ trash-and-gain-credits ]}
```