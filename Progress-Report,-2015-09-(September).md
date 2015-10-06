Welcome to the Jinteki.net open-source progress report for September 2015 (plus a few days of October). In this monthly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New contributors

Thanks to the new developers who added to the platform this month: sodwyer, chua-mbt, rluba, and Objelisks!

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by queueseven, JoelCFC25, and mtgred! nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### Major system changes

#### News Ticker
Thanks to domtancredi, hopefully you've noticed the news ticker above the general chat. We will be using it more to broadcast major changes to the system. He also added a form to let users generate a new password if they've forgotten theirs. 

#### User interface
queueseven was hard at work on more UI enhancements. Crucially, he killed the nasty bug that prevented using cards in remote servers with 3 or more items! 

![](https://cloud.githubusercontent.com/assets/3126597/10014032/c3d48510-6116-11e5-8f38-21df4d19f734.png)

Players can now more easily track what happened with each action because spent costs are displayed in the log:   

![](https://cloud.githubusercontent.com/assets/3126597/9705929/505b01d8-54d5-11e5-9914-b47f62382143.png)

His other great new UI addition is a set of console commands that can be entered in chat to manipulate the game state in various ways that used to be very time/click intensive. 

- `/draw n` -> Draw `n` cards
- `/credit n` -> Set your credits to `n`
- `/click n` -> Set your clicks to `n`
- `/memory n` -> Set your memory to `n`
- `/tag n` -> Set your tags to `n`
- `/bp n` -> Set your bad publicity to `n`
- `/link n` -> Set your link to `n`
- `/handsize n` -> Set your handsize to `n`
- `/take-meat n` -> Take `n` meat damage (Runner only)
- `/take-net n` -> Take `n` meat damage (Runner only) 
- `/take-brain n` -> Take `n` meat damage (Runner only)
- `/discard #n` -> Discard card number `n` from your hand  
- `/deck #n` -> Put card number `n` from your hand on top of your deck

When a user performs one of these commands, it will be highlighted with an orange icon in chat to alert your opponent. Please use these judiciously and communicate with your opponent about what you're doing and why! Note that the `/take-net n` command can be used as a workaround in many cases for the as-yet-unimplemented Chronos Protocol identity.

![bild 3](https://cloud.githubusercontent.com/assets/3126597/9814746/be64c844-5890-11e5-9088-ab3c6fb4082f.png)

#### Networking improvements
Many users struggled through the great mid-September lag-athon of '15, as mtgred jetted off to Asia for 3 weeks to compete in the Monopoly world championships and have a holiday. The deployment of a new [socket.io](http://socket.io/) version caused some headaches--despite the benefits of the upgrade (websocket compression that greatly reduces the volume of data transmitted to clients), cached old versions on many users' systems hindered the rollout and caused a lot of disconnect/reconnect frustrations. There is still some [updating of the game lobby in progress](https://github.com/mtgred/netrunner/pull/786), which is hoped to also improve overall performance.

#### Unit testing framework
Neal [rolled out unit testing](https://github.com/mtgred/netrunner/pull/736) this month. Using Clojure's `clojure.testing` namespace, small unit tests can be written to ensure the validity of card operations, to validate things like "Using Sure Gamble should net 4 credits", or "Using Account Siphon on a Corp with 5 credits should leave them at 0 credits and give the Runner 10 credits and 2 tags".

Example test for Magnum Opus:

```clojure
(deftest magnum-opus-click
  "Magnum Opus - Gain 2 cr"
  (do-game
    (new-game (default-corp)
              (default-runner [(qty "Magnum Opus" 1)]))
    (take-credits state :corp)
    (play-from-hand state :runner "Magnum Opus")
    (is (= 2 (:memory (get-runner))))
    (is (= 0 (:credit (get-runner))))
    (let [mopus (get-in @state [:runner :rig :program 0])]
      (card-ability state :runner mopus 0)
      (is (= 2 (:credit (get-runner))) "Gain 2cr"))))
```

* `do-game` is a macro to give you a `state` variable as in the core.
* `new-game` starts a new game and returns its state, taking as arguments a deck to use for Corp and Runner.
* `(default-corp)` returns a map for a "default" Corp of HB: Custom Biotics (no effect on game) with 3x Hedge Fund.
* `(default-runner)` returns The Professor with 3x Sure Gamble... unless you provide a vector of cards to use as the deck.
* `(qty _name_ _amount_)` loads a card with a given name and puts that many into a vector for the deck.
* After `new-game`, the game is started and on the Corp's first click after mandatory draw
* `take-credits` ends a player's turns by taking a specified number of credits (or max if unspecified) until their turn ends, then starts the next turn.
* `play-from-hand` finds a card with a given name in the player's hand and calls `play` on it (what the UI calls to play a card when clicked)
* `is` enforces a condition as being true, and fails the test if it is false. Here, we make sure that Magnum Opus leaves 2 unused memory and 0 credits.
* We retrieve the installed version of Opus directly from the state.
* `card-ability` triggers an ability of a card based on its index, counting from 0 for the first ability. The first ability of Magnum Opus is the Click: Gain 2 credits.
* We make sure the Runner now has 2 credits.

### New card implementations
September saw a bumper crop of new card implementations, particularly from Joel, who used coding as a form of greatly-needed stress relief in his downtime. 

##### queueseven
Mutate, Surfer, Blacklist, flippable identities (Jinteki Biotech, SYNC), Brain Chip, Independent Thinking, Wireless Net Pavilion, fixed run redirecting cards (Bullfrog, Sneakdoor Beta, Susanoo-no-Mikoto) to update their knowledge of the ice protecting the new server being run. 

##### JoelCFC25
**_New:_** New Angeles Sol: Your News, Security Chip, Cyber Threat, Social Engineering, Shipment from MirrorMorph, Copycat, False Echo, Showing Off, Itinerant Protesters, An Offer You Can't Refuse, Director Haas' Pet Project, The Twins, Starlight Crusade Funding, Heinlein Grid, Worlds Plaza, Awakening Center, handling of subtypes (Rebranding Team, Tinkering, Paintbrush, Sub Boost, Rielle "Kit" Peddler)  
**_Enhanced:_** fixed Shards to count as successful runs when installing for free from Grip, fully automated Pawn, Bishop rehosting restrictions, recursive installs with Accelerated Beta Test so it will work with HB: The Foundry, discounted program installs from Monolith, dynamically changing strength for IQ

##### Neal
Gagarin: Deep Space, lots of bug fixes (Faust, Chakana, nested hosting, Lag Time, Team Sponsorship, _et al_).

##### domtancredi
Quantum Predictive Model, Research Grant

##### JHonaker
The All-Seeing I

As of this writing, we are at __94.2%__ card automation through _Data and Destiny_!

### Coming soon...

* Blocking prompts that can wait for user input (we hope!)
* Improved performance by sending only game state diffs to clients
* Better automation of recurring credits
* New console command to simplify runs on heavily-iced servers
* The name of run events will be shown on the orange run arrow

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred (Worlds 2014 runner-up!) and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.