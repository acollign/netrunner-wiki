Welcome to the Jinteki.net open-source progress report for Q2 2016. In this quarterly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New Contributors
meatcompute, tmoritaa, nikolasavic, IanWhalen

### Dev Meeting & License (May 12th, 2016)
Product Roadmap discussion - 1st Jinteki.net Dev Meeting  
_(Attendees: Minh, Neal, Joel, Filip, Dominic K., Dominic T., Justin, Łukasz, Nikola)_  

Product Discussion Items: 
* Licensing: MIT License was agreed upon, due to its simplicity and lack of terms we can’t enforce anyway. (Minh, Dominic, Łukasz)
* History of Jinteki.net by Minh
* Casual/Competitive (Room-split) discussion: potential cause for drop in usage
* Project leading, pull requests: 
** Dictator Project Lead (agreed-upon approach)
** Minh open for Game Engine changes; Minh has final say on UX changes
* Server Access: 
** Jinteki.net is hosted on a private server, and access will not be shared. 
** Docker possible on another server, for supporting future container-based deployments
* Immediate Product Roadmap plans: 
** Łukasz: Chat experience
** Dominic Kexel: Deck synchronization with NRDB; User Survey
** Minh: User Profiles
* Player Behavior:
** Discussion on what to do with negative players (discuss shadowban, ban, and community moderation features)

Action Points:

1. Add MIT license to the repo
2. Change repository name to *jinteki*
3. Add basic user profile code - just changing e-mail for now.

### Major System Changes
#### Counters Rework
Everything changed when the Corporate Sales Team attacked Titan Transnational.

When times were simpler, our card data only supported two types of "counters": advancement counters, and all other counters. When advancing a card, we placed a `:advance-counter` integer on the card; for agenda counters, power counters, credits (like Bank Job), virus counters, and recurring credits, we placed a `:counter` integer. This worked fine for most interactions, and those that were incorrect (using Helium-3 Deposit to place a virus counter on Hivemind) were seen as unimportant.

Then Corporate Sales Team was released, the first agenda using non-agenda counters that Weyland can score. When Titan Transnational scores an agenda, a `:counter` is placed on that agenda. Corporate Sales Team sees its `:counter`s as credits, so Titan scoring a CST adds an eleventh credit instead of a single agenda counter. Whoops.

Saintis put together a comprehensive counters rework to cleanly differentiate between all types of counters. Each counter now has a different color background, and card effects can better filter counter types when necessary. Helium-3 Deposit requires a `:counter :power` for its effect, while Titan places a `:counter :agenda` on a scored agenda.

<img width="676" alt="counters5" src="https://cloud.githubusercontent.com/assets/13198563/15653229/85c3de6e-2683-11e6-90ba-67875ac49c22.png">

_Virus, recurring, power, current-strength, and credit counters on runner's rig; agenda and credit counters in Corp's scored area; advancement and current-strong counters on Corp's server._

#### Rebirth and server-side card data


#### "Wait-for-completion"
This may get complicated :P.

Consider a scenario in which a Runner with Desperado installed plays Account Siphon. Upon passing the last piece of ice and committing to access, the flow of events in the game engine goes something like this:

1. The run is declared successful, and we trigger an event called `:successful-run`.
2. Desperado receives the event and grants the Runner 1 credit. 
3. Now that all event triggers have resolved, the Runner moves to the access phase. 
4. Runner chooses to either access or do the Account Siphon routine.

If we consider this flow as a series of C-like function calls, we get something like this:

```
runSuccessful = true;
triggerEvent(successfulRun);
-----> gain(runner, credit, 1); // a series of function calls leads to Desperado's function
if (useRunAbility) {
-----> // gain credits and tags in Account Siphon's function
}
else {
   accessPhase();
}
```

Nothing wrong here. The `triggerEvent` call alerts Desperado about the successful run, and then Desperado grants a credit. Because Desperado doesn't need any user input to complete its effect, it can return back to the main routine and continue to the access-related code.

But what if a successful-run trigger requires a decision to be made by the user... say, if Silhouette uses Account Siphon, and chooses to expose a Psychic Field? Then we get a flow like this:

1. Successful run triggers.
2. Silhouette asks the runner to select a card to expose, who selects an unrezzed Psychic Field.
3. Psychic Field starts a psi-game. Once both psi-amounts are chosen, the effect is resolved.
4. Now that Silhouette's ability is resolved, any other successful-run triggers can also resolve.
5. We can now return to the access phase.

It might not seem important, but the fact that Silhouette requires user interaction is impactful on the C-like code above. We handle user interaction through "prompts" (in other UI systems, these would be called "dialogs"), and do to the nature of our browser-based interface, all our prompts are "modeless" -- creating a prompt doesn't prevent any code that follows the prompt from executing. So if we substitute the Silhouette code into our example above, we start to see a problem:

```
triggerEvent(successfulRun);
-----> showPrompt("Select a card to expose"); // in Silhouette
// THE NEXT LINE OF CODE RUNS IMMEDIATELY!!!
if (useRunAbility) {
-----> // gain credits and tags in Account Siphon's function
}
// ...
```

The engine moves the run to the access/replace-access phase before Silhouette's ability has fully resolved, because the creation of Silhouette's prompt does not actually "pause" execution of the run code. In the Psychic Field example, this means that Account Siphon's effect triggers while Silhouette's prompt is still open. If Silhouette then exposes Psychic Field, the Corp will have 5cr fewer to spend on the psi game, which is not correct. This exact situation was one of our [oldest bugs](https://github.com/mtgred/netrunner/issues/155).

So how to fix this? There needs to be a way for a card effect to "pause" until some other engine or card ability completely resolves, including all prompts and optional side-effects. Such a "when-completed" function would ideally plug directly into existing game logic and not require any changes to the flow of core functions, other than specifying "when this completes, do this afterwards."

nealterrell's `when-completed` framework does exactly this: with some tricky use of function closures and compile-time macros, we now have the ability to code card effects that __must__ wait for other effects to resolve before they continue. Take a simplified version of Subcontract's code as an example:

```clojure
;; "target" is the first operation selected by Subcontract
(when-completed (play-card target)
                (resolve-ability subcontract))
```

`when-completed` resolves the function `play-card`, but beforehand registers a handler that says "when this instance of `play-card` is finished, then resolve the Subcontract ability one more time." Simple operations like Hedge Fund will "finish" immediately after they are played, but complicated operations involving prompts (like SEA Source) won't "finish" until they fully resolve. Since Subcontract won't prompt for the second operation until the first is "finished", we can avoid awkward situations where a rushing Corp player tries to resolve a second operation while the first is still being responded to by the runner, say, by preventing a first Scorched Earth's damage with Plascrete Carapace.

We are slowly applying this framework to different parts of the game engine, but have already used it to fix or improve:

* Accelerated Diagnostics, which should no longer suffer from confusing interleaved prompts.
* Using Mumbad City Hall to play Heritage Committee, so the shuffle from Mumbad happens after adding a card to R&D via Heritage.
* Damage routines, which will not "complete" until prevention effects have resolved.
* On-access routines, allowing Psychic Field and Fetal AI to resolve before other events in the run sequence trigger.
* Successful-run effects, as above.
* Entire runs will be "finished" when the run ends, allowing Out of the Ashes to wait until one run finishes before asking if another Out of the Ashes should be triggered from the heap.

#### Server Performance
Just kidding. We are quite happy with the server's performance since the last Progress Report and haven't needed to upgrade anything.


### Survey Results

![](http://i.imgur.com/g1R2lNH.png)

![](https://i.imgur.com/MyPFvtN.png)

![](http://i.imgur.com/JKvUR9D.png)

![](http://i.imgur.com/Tn7Fmmd.png)

![](http://i.imgur.com/qONXXhA.png)

![](http://i.imgur.com/nEYhJ7B.png)

![](http://i.imgur.com/F8YQqw9.png)

### Other Improvements and Automations
Let spectators leave the game, active player indicator, Trigger active player's events first (queueseven)
"Gained subroutines" messages, face up/down archives indicators (Saintis)


#### New Card Implementations
Tori Hanzo, Titanium Ribs, Draft IDs (Joel)

Caprice (Saintis)

Partial or better status for 87.7% of cycle, 97.8% of catalog