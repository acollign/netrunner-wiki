This is mostly written to refresh my memory.  In applying a fix to Find the Truth whereby we needed to wait for its effect to complete before allowing other effects, I had to make sure its reveal effect was completed before other things in the engine could fire.

I think this might be of interest as the effects fires a prompt which requires user interaction - and thus fits the delayed completion criteria... but as you will see the passing around the event data happens automatically in the engine without extra code or knowledge from the developer (thankfully!).  Elsewhere we use the `continue-ability` macro to pass the `eid` (Event ID) around.

First - important to note that the event that triggers Find the Truth is `:successful-run `and this event itself is wait-completed via the `when-completed` macro.  So any cards responding to this event, will have to complete their functions before anything else happens in the game.  

Here is the core from register-successful-run which is handling this:
```
(when-completed (trigger-event-simult state side :successful-run nil (first (get-in @state [:run :server])))
```

This means we don't want the Find the Truth to be fire and forget.  We need to make sure it is all done before progressing.

Here is the relevant part of the first re-write which fires on the :successful-run events happening:
```
{:interactive (req true)
 :optional {:delayed-completion true
            :req (req (= 1 (count (get-in @state [:runner :register :successful-run]))))
            :prompt "Use Find the Truth to look at the top card of R&D?"
            :yes-ability {:delayed-completion true
                          :prompt (req (str "The top card of R&D is " (:title (first (:deck corp)))))
                          :msg "look at the top card of R&D"
                          :choices ["OK"]
                          :effect (effect (effect-completed eid))}
            :no-ability {:effect (effect (effect-completed eid))}}}
```

So what is going on here?

Firstly `:interactive (req true)` is the first key.  This tells the game engine that we might require a user to determine which order to apply card effects in if there are multiple firing from the same event

The second top level key is `:optional`.  In the function `resolve-ability` which is called when the card is invoked, an `eid `is assigned for the card resolution which is passed to `resolve-ability-eid`.  This function checks for the `:optional` key using `check-optional` function which receives the assigned `eid`.

As you can see so far we are passing the `eid` around in the prompt function without needing any extra code.

The check-optional code looks like this assuming it finds this `:optional` key

```
(if (can-trigger? state side optional card targets)
      (optional-ability state (or (:player optional) side) eid card (:prompt optional) optional targets)
      (effect-completed state side eid card))
```

So if the effect cannot be triggered the `effect-completed `code is invoked and this `eid` is done and the engine can keep moving.  However if not, the function `optional-ability` is called again with the original `eid` passed in.

`optional-ability` asks the user if they want to invoke the card effect and has two branches based on the user selecting yes or no.  Let's cover the NO version first.  The function checks if we have defined a :no-ability key and if so invokes that code, and if not it completes the effect with this code here:

```
(if-let [no-ability (:no-ability ability)]
  (resolve-ability state side (assoc no-ability :eid eid) card targets)
  (effect-completed state side eid card))
```

So my original re-write are the card had this extra code that would have got called anyway so I could delete it:

`:no-ability {:effect (effect (effect-completed eid)`

How about the yes-ability?  Well, this ability must exist in the first place, and we also check that the player can actually pay for it... if they can then we invoke the `:yes-ability` effect.  Important to note the original `eid` is again automatically passed in:
`(resolve-ability state side (assoc yes-ability :eid eid) card targets)`

What was the yes-ability code block in my card:

```
{:delayed-completion true
 :prompt (req (str "The top card of R&D is " (:title (first (:deck corp)))))
 :msg "look at the top card of R&D"
 :choices ["OK"]
 :effect (effect (effect-completed eid))}
```

This code block again invokes a prompt with a message telling the player what the top card of R&D is.  We only gives the player one choice OK which is used to clear the prompt.  No matter what the choice we then invoke the effect which is to run the `effect-completed` function for this `eid`.

And then we are all done!

So what did I learn / re-remember / questions
1. We don't need a :no-ability if we just want the effect to complete when the user hits no.  Some other cards have this wrong already.  No harm - just extra code.

2. In two locations I added the `:delayed-completion` key.  Looking around some more in the code this was useless and does nothing in this instance.  When each pass through the function `resolve-ability-eid` is done we call a function called `complete-ability` whose job it is to complete the effect.  This has some funky logic in it which is the main place where we check for the `:delayed-completion` key and other keys like so:

```
  (when (or (and **(not choices)** **(not optional)** (not psi) (not trace) (not delayed-completion))
            (and (or optional psi choices trace) (false? delayed-completion)))
    (effect-completed state side eid card)))
```

What we can see here is that for a code block with the :optional key we will not complete the effect (unless `:delayed-completion false `is set), nor will we when there are :choices in the ability which is the `:yes-effect `for Find the Truth.  

3.  I think the :req check for seeing is this is the first successful run this turn should also be cleaned up to use other smaller existing functions. 

4. Finally the yes-ability :prompt got changed to threading macro which makes it easier to understand... less brackets nesting

So the second attempt for the re-write would be:
```
{:interactive (req true)
 :optional {:req (req (first-event? state side :successful-run))
            :prompt "Use Find the Truth to look at the top card of R&D?"
            :yes-ability {:prompt (req (->> corp :deck first :title (str "The top card of R&D is ")))
                          :msg "look at the top card of R&D"
                          :choices ["OK"]
                          :effect (effect (effect-completed eid))}}}
```

Finally here is the relevant flow of function calls which should help:
1. register-successful-run
2. trigger-event-simult for event :successful-run  - noting Find the Truth's :interactive key would be used if the runner needed to be offered a choice of card effect ordering
3. Find the Truth is invoked using` resolve-ability` and a new `eid` is created
4. This calls resolve-ability-eid
5. resolve-ability-eid 
TBD



