Welcome to the Jinteki.net open-source progress report for June 2015. In this inaugural report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

## Major system changes

### Network lag

One of the biggest improvements in user experience wasn't related to the game at all, simply an improvement to networking code. At the prompting of eah13, developers looking into server lag noticed a lot of data being sent to maintain the Current Games list. It turned out that each player's entire deck list was being sent to all site visitors every time a game was created, joined, or finished, even though that information is only needed up until a game is started. With network traffic identified as the main performance bottleneck, mtgred implemented a simple fix to not send out deck lists for in-progress games, and that reduced data being sent from the server by ~75 percent! Whereas the server used to become extremely laggy at around 15 active games, this fix has helped us push 25-30 active games without a sign of lag!

![Network traffic improvement](https://cloud.githubusercontent.com/assets/284114/8163966/77fa7b6e-1386-11e5-9899-41a92e5ebe2d.png)

_Can you guess the date that the fix was deployed?_

### Hosting cards

Card hosting has been one of our most-requested features for some time, and June saw the implementation of most card-hosting effects. mtgred finished code for several hosting scenarios:

* Inactive hosting: Personal Workshop and The Supplier. We call this "inactive" hosting because the cards are not installed and cannot be interacted with in most situations.
* Installed hosting: Dinosaurus, Djinn, Leprechaun, London Library, Off-Campus Apartment, Progenitor, Omni-Drive, and Scheherazade. Cards hosted on these are installed and can be used and targeted by abilities and effects. To host a card on these, click the host card and then select the card you want to host from your hand. Improvements will come for interactions with Self-modifying Code, Clone Chip, etc.
* Host modifiers: Parasite, The Personal Touch, Patch, Sub Boost, Oversight AI, Bioroid Efficiency Research, Bishop, Knight, Pawn, Rook. These are installed / hosted onto existing cards and modify them in some way. 

Face-down hosting for Glenn Station and Street Peddler has not been finished.

### Ice dynamic strengths

With the implementation of card hosting came an early attempt at Parasite with an auto-trash effect. This worked well enough for simple ice, but any ice that had modified strengths failed miserably. That necessitated an upgrade to ice strength systems by nealterrell, aimed at tracking every single variable contributing to an ice's actual strength. The end result was a system supporting:

* ice that change their own strength in certain conditions (Wraparound, Turing etc., Curtain Wall, NEXT Bronze)
* ice with counter-based strengths (Ice Wall etc., Quicksand)
* permanent increases to some installed ice (Superior Cyberwalls etc., HB: Stronger Together, Lag Time, Experiential Data, Patch)
* temporary increases (IT Department, Corporate Troubleshooter)
* encounter-based decreases in strength (Scrubbed, Ice Carver, Datasucker, Wyrm!)
* permanent decreases (Parasite, Bishop)

We had to deal with some interesting infinite-recursion scenarios when players Parasited NEXT Bronze or Curtain Wall (someone is patient!), but those got worked out. We now have a complete system for tracking an ice's actual strength which allows proper automation of Parasite and will contribute to future automation of subroutine breaking and resolution.

![Ice strengths.](https://cloud.githubusercontent.com/assets/10083341/8028452/18726be4-0d63-11e5-939a-63ea4b4b8968.png)

_Approaching an outermost Curtain Wall with Scrubbed in play, then an Ice Wall with +1 advancement token._

### Prevention effects

Prevention effects were one of the harder missing features to play around. With a Plascrete Carapace in play, Runners used to take meat damage and then have to "fix" the missing feature by taking the trashed cards back into hand, hoping the Corp was a good sport and didn't read the log about what was trashed. A full implementation of damage and trash prevention systems came in June, helping Runners sleep a little better at night with their Plascrete Carapaces and Sacrificial Constructs. If a card with a prevention effect is active when damage is dealt or a card is targeted for trashing, the Runner receives a prompt to activate any prevention effects, and only afterwards does the effect resolve. The full list of related cards implemented is:

* Damage-related: The Cleaners, Plascrete Carapace, Chrome Parlor, Crash Space, Deus Ex, Feedback Filter, Leverage, Monolith, Muresh Bodysuit, Net Shield, Sacrificial Clone. 
* Trash-related: Fall Guy, Sacrificial Construct, LLDS Energy Regulator.

The system will be adapted to tag-prevention eventually.

![Scorched Earth.](https://cloud.githubusercontent.com/assets/10083341/8462490/24fc77ae-1fe9-11e5-935f-e806a59e5789.PNG)

_Click Plascrete Carapace to prevent the Scorch damage._

### Modifications to install, rez, and trash costs

Kate players will no longer have to manually take 1 credit when installing programs or hardware thanks to a comprehensive system for modifying the install, rez, or trash costs of cards. Corp players can use Industrial Genomics' ability to tax the living hell out of Runners, or get big discounts on their Bioroid ice from Brain-Taping Warehouse. The full card lists:

* Runner install costs: Kate "Mac" McCaffrey, Modded, Career Fair, Scavenge.
* Corp rez costs: Akitaro Watanabe, Braintrust, Xanadu, Reina Roja, Brain-Taping Warehouse, Breaker Bay Grid, Rook, Running Interference, IQ
* Trash costs: Encryption Protocol, Industrial Genomics, Skulljack, Oaktown Grid. 

![Trash costs.](https://cloud.githubusercontent.com/assets/10083341/8462931/eeefc7ee-1feb-11e5-9d27-5d3a7dd0ff84.PNG)

_Probably should have run Archives first._

### Agenda advancement requirements

One of the most iconic cards in the game is also one of the trickiest to implement, but we finally have a system to support SanSan City Grid and other cards that modify agenda advancement requirements. Before this system, Corps had to take extra clicks and credits to get agendas up to scoring range when SanSan was in play, but now all advancement requirements are tracked dynamically, including the effects of Chakana, Medical Breakthrough, NAPD Contract, SanSan City Grid, The Source, and Traffic Jam. Can you hear the train a-comin'?


## SanSan Cycle

mtgred, JoelCFC25, and nealterrell have been hard at work keeping our card base up to date with the newest releases in the SanSan Cycle. 59 of the 80 cards in the cycle have fully-working automations implemented, with another dozen receiving workable versions. The rest are mostly manual/honor-system: cards like Clot, Blacklist, Underway Grid, and others require communication between players until a system is implemented for them. Noteworthy additions to the cardpool in this update include Jinteki Biotech, Hayley Kaplan, Armand "Geist" Walker, Analog Dreamers, and London Library. 

## Other notables

* JoelCFC25 and nealterrell collaborated to implement the Grail ice, allowing Corps to both reveal and resolve Grail ice from their hand.
* mtgred implemented the Caïssa programs with the new hosting system.
* Titan Transnational players will appreciate implementations of Trick of Light, Mark Yale, Constellation Protocol, and upcoming implementations of space-ice discounts.
* Andromeda's favorite pastime of Security Testing is now automated.
* Ash 2X3ZB9CY is on the job waiting to turn Runners away from your sensitive servers. Just be sure to click him once the Runner passes the last ice, before you select No Action!

## Contribute

Jinteki.net is an open-source project of love run and paid for by mtgred and other volunteers. You can contribute by reporting bugs and even submitting fixes and new implementations on our GitHub page.