Welcome to the Jinteki.net open-source progress report for Q2 2016. In this quarterly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New Contributors
Thanks to the new developers who added to the platform this quarter: IanWhalen, ilyanep, meatcompute, nikolasavic, and tmoritaa!

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by queueseven, JoelCFC25, and mtgred! nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### Developer Meeting (12 May 2016)
The Jinteki.net dev team met on Google Hangouts for the first time in May, to discuss the current state of the project, outstanding issues, and a roadmap for the future. Here are some brief notes from the meeting, courtesy of domtancredi:

Product Roadmap discussion - 1st Jinteki.net Dev Meeting  
_(Attendees: Minh (mtgred), Neal (nealterrell), Joel (JoelCFC25), Filip (Saintis), Dominic K. (queueseven), Dominic T. (domtancredi), Justin (justinliew), Łukasz (zaroth), Nikola (nikolasavic))_  

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

1. MIT license added to the project -- done.
2. Player survey to guide future development -- done.
3. Add basic user profile code - just changing e-mail for now -- in progress.

### April Fools
The Weyland Consortium graciously took over Jinteki.net for a day of meat-damage madness on 1 April 2016, when the site was transformed into Weyland.meat to celebrate April Fools Day. zaroth and nealterrell collaborated to bring a whole bunch of silliness to the J.net community while JoelCFC25 was grumpily vacationing in Florida. In case you missed it, here are some highlights:

* Sounds! zaroth collected a list of sound clips from movies, video games, and other sources to attach sounds to dozens or hundreds of in-game actions. Our favorites? [Fight Club](https://www.youtube.com/watch?v=E1d5VvCa8Fo) for Scorched Earth; "Our hero!" Jackson Howard; ["1.21 gigawatts?!?"](https://www.youtube.com/watch?v=mjCRUvX2D0E) for Marcus Batty; and Guile's Theme for Stimhack.
* Usernames randomized to one of several patterns: 
  * Bioroid, with a random pattern of capital letters/numbers after the username
  * Bioroid designation, with a 1.0/2.0/3.0 designation after the username
  * "l33t sp34k"
  * "NetrunnerDB Decklist of the Week Winner" suffix
  * Chairman _username_
  * Runner subtype, with a random Natural/Digital/G-mod/etc. after the username
  * Unfortunately, modulo arithmetic is too difficult for nealterrell, and so anyone who was randomly chosen to have a Bioroid designation always received 3.0, and anyone with a Runner subtype always received "Digital". 
* Randomized Weyland-themed card images as avatars (mostly meat damage cards)
* A fake "Admin" page showing site administrators and other VIPs manipulating game engine settings like "Agenda density", "Rage quit simulator", community stats, and more.

We hope you enjoyed this labor of love :).

### Major System Changes
#### Counters Rework
Everything changed when the Corporate Sales Team attacked Titan Transnational.

When times were simpler, our card data only supported two types of "counters": advancement counters, and all other counters. When advancing a card, we placed a `:advance-counter` integer on the card; for agenda counters, power counters, credits (like Bank Job), virus counters, and recurring credits, we placed a `:counter` integer. This worked fine for most interactions, and those that were incorrect (using Helium-3 Deposit to place a virus counter on Hivemind) were seen as unimportant.

Then Corporate Sales Team was released, the first agenda using non-agenda counters that Weyland can score. When Titan Transnational scores an agenda, a `:counter` is placed on that agenda. Corporate Sales Team sees its `:counter`s as credits, so Titan scoring a CST adds an eleventh credit instead of a single agenda counter. Whoops.

Saintis put together a comprehensive counters rework to cleanly differentiate between all types of counters. Each counter now has a different color background, and card effects can better filter counter types when necessary. Helium-3 Deposit requires a `:counter :power` for its effect, while Titan places a `:counter :agenda` on a scored agenda.

<img width="676" alt="counters5" src="https://cloud.githubusercontent.com/assets/13198563/15653229/85c3de6e-2683-11e6-90ba-67875ac49c22.png">

_Virus, recurring-credit, current-strength, power, and credit counters on runner's rig; agenda and credit counters in Corp's scored area; advancement and current-strength counters on Corp's server._

#### Rebirth and Server-Side Card Data
When a game begins, a list of cards in each player's deck is sent from the game lobby server to the game logic server, which is responsible for running the actual game with input from the players. The logic server uses the decks to load behavioral definitions for each card in the current game; these definitions are expressed in code (as "the card named Hedge Fund has an effect of granting 9 credits"), as opposed to the cards themselves being data ("the card named Hedge Fund is a neutral corp card costing 5 credits"), loaded from our database of cards, itself imported from NetrunnerDB's API. Importantly, the logic server does not have the ability to load card _data_ on the fly; it only has the data for the cards that are in each player's deck.

This makes Rebirth a problem. Rebirth needs to load card _data_ for all cards in Android: Netrunner, so it can filter those cards into a list of identities of the same faction as the player. We could simply hard-code that list of identities in the behavioral code for Rebirth, but that list would have to change with every new identity and would be easy to overlook in the long term. The better long-term solution is to give the logic server a list of all card data when the server begins... so that's what we did.

nealterrell extended the game lobby server to load all cards from the database (the game logic server does not know of this database, and does not need to) and send those cards as a one-time message when the logic server boots. The logic server retains that list and makes it available to all card behaviors through a variable called `@all-cards`. Rebirth can now present a list of targets in a prompt as:

```clojure
:prompt "Choose an identity to become"
:choice (filter is-swappable? @all-cards)
```

where `is-swappable?` tests a card to see if it is of the same faction as the player's selected identity. Simple!

It's more complicated to hook up the identity so its abilities start working, and there were a lot of edge cases to work through, but mharris717 rose to the challenge and finished an excellent implementation of the card. A few identities were naively implemented and need to be updated now that an identity can become "active" in the middle of a game, but we'll tackle those as they come up. Gabe, for example, assumes that the first run on HQ that he "sees" is the first of the turn, but now that Gabe can become active during a turn in which the runner already ran HQ, his behavior will have to be updated to examine previous actions that turn.

![Rebirth](https://cloud.githubusercontent.com/assets/64296/15781281/5cd1127c-2973-11e6-9288-a0c5956a2942.png)

_Rebirthing from Kate._

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

#### Manual Ordering of Simultaneous Triggers
Take a deep breath... we're almost done.

With the ability to wait for an effect to finish completely, we can now turn our attention to the manual resolution of simultaneous triggers. We have been missing this feature for a very long time, and while JoelCFC25 has become adept at hacking creative solutions to around this problem, the time has come to code the game rules for real, and allow each player to choose the order that their cards respond to event triggers in the game. 

As an example of why this is important, consider the following scenario from one of our oldest Issues, courtesy of Zulyen:
> "Runner got 2 Agendas in his/her score area and only two cards in hand which one is "I've Had Worst". Jinteki PE scores a Philotic Entanglement.
>
> 2 net damages occurs because of the Agenda and 1 net damage occurs because of the ability of PE in the same time. The corp can choose in which order.
>
> 0% of winning chance by starting with the Philotic.
>
> 50% of winning chance by starting with the PE."

nealterrell addressed this issue by working up a "choose your own trigger" UI for resolving multiple handlers of the same trigger. The system is (relatively) intelligent: you will only be asked to order the simultaneous abilities if there is some known important reason for doing so; otherwise they will automatically resolve. If you have 3 Fan Sites installed, then you won't need to manually trigger them... but if you have Human First and Gang Sign installed, you may want to resolve Human First before doing your Gang Sign access in case there's an NAPD Contract in HQ. 

This system currently only applies to agenda-stolen and agenda-scored triggers, which is where we find most of the shenanigans needing this ability, but we will be expanding to other triggers soon. For now, you can look forward to:

1. Triggering Jinteki: PE before Philotic Entanglement to increase the chances of a kill
2. Triggering Turntable after adding "The Price of Freedom" to your score area when stealing an agenda
3. Proper nesting of cascading triggers, like Leela and Gang Sign interactions


#### Server Performance
Just kidding. We are quite happy with the server's performance since the last Progress Report and haven't needed to upgrade anything.


### Survey Results
Over 600 people answered our Players' Survey in May, and here are the results! Anonymized responses can be found in [this Google Sheets document](https://docs.google.com/spreadsheets/d/1Zvm0H1CeRQKAb_vSZT08DHDimtfXrKzsPbD3_bs8qSI/edit?usp=sharing), or view the charts below!

#### Charts
![](http://i.imgur.com/g1R2lNH.png)

![](https://i.imgur.com/MyPFvtN.png)

![](http://i.imgur.com/JKvUR9D.png)

![](http://i.imgur.com/Tn7Fmmd.png)

![](http://i.imgur.com/qONXXhA.png)

![](http://i.imgur.com/nEYhJ7B.png)

![](http://i.imgur.com/F8YQqw9.png)

#### Selected comments
* "The user interface is just too painful for me to play online. Call me when it's at the Hearthstone level." -- Don't sit by the phone :(.
* "Makes me want to learn Clojure to contribute but then I just end up running." -- Don't be afraid! Remember: to iterate is human, to recurse is divine.
* "Better control over simultaneous effects, such as getting to choose the order in which start of turn triggers fire. Example - being able to install Earthrise Hotel with The Supplier and get the 2 card draw for that turn." -- We have this, it's just that your example doesn't work according to the game rules, as Earthrise Hotel was not installed when the turn began and is not eligible to trigger its effect even though it was brought into play during The Supplier's effect.
* "I can't emphasize how much I want a friends list..." -- We're working on it!

### Other Improvements and Automations
#### UI improvements
* Spectators can leave the game after both players have left (queueseven)
* Newly installed cards shown with an orange highlight until next turn begins (mtgred)
* The active player's stats now highlight in orange (queueseven)
* Archives now shows how many cards are face-up vs. face-down (Saintis)
* ICE that gain subroutines (Komainu) can print how many subs they have (Saintis)

#### New commands
* `/rfg`: remove a card from the game

#### Notable new card implementations
* Tori Hanzo, Titanium Ribs, Draft IDs, Corporate Scandal, lots of Mumbad Cycle cards (JoelCFC25)
* Caprice Nisei automation (Saintis)
* The Price of Freedom, Political Dealings, Lateral Growth, Political Graffiti, Bhagat, Patron (queueseven)
* Voting Machine Initiative, Out of the Ashes, Information Sifting (nealterrell)
* Salsette Slums (ilyanep)

As of time of this writing, we are at [__97.8%__ card automation](https://docs.google.com/spreadsheets/d/1ICv19cNjSaW9C-DoEEGH3iFt09PBTob4CAutGex0gnE/pubhtml) through _Salsette Island_!

### Coming Soon...

* More Mumbad Cycle!
* Manual resolution of simultaneous triggers

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.