Welcome to the Jinteki.net open-source progress report for July 2015. In this report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform, plus preview some improvements we have in the works.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

## New contributors

We'll get to their submissions below, but let's take a moment to thank the new developers who completed a variety of improvements to the platform this month. In alphabetical order, huge thanks to DominicKexel, gaverhae, justinliew, lanaganojunior, and Leffelin!

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by DominicKexel, JoelCFC25, and mtgred!

## Major system changes

### Hosting complete

mtgred polished off our hosting system this month, including face-down hosting for Street Peddler, and the ability to host cards that are already installed (which should only be used in conjunction with cards like Self-modifying Code or Clone Chip, which automatically install the targeted card in the Rig). We are now 100% complete for host cards! 

### Auto-increasing icebreaker strengths

With [this challenge](https://github.com/mtgred/netrunner/issues/259) issued, nealterrell extended his June contributions to better track the "pump" strengths of icebreakers. The previous system worked well enough for simple breakers but struggled to capture the subtle behaviors of cards like Snowball, Shiv, and Net-Ready Eyes. Our new system categorizes icebreaker strength increases in three ways: base strength modifiers (The Personal Touch, Dinosaurus, Shiv/Spike/Crowbar, Study Guide, Atman, Sage); per-run modifiers (Net-Ready Eyes, Gordian Blade/Gordian Blade/Pipeline/Inti); and per-encounter modifiers (most "+X strength" abilities on icebreakers). 

![Dinosaurus](https://cloud.githubusercontent.com/assets/10083341/8998223/6a6b693c-36df-11e5-9a19-84f80cab6877.PNG)

_Is it crowded in here?_

With ice and icebreaker strengths being comprehensively tracked, development shifted to an automated "increase strength to match this ice" button for pumpable icebreakers. Thanks to some voodoo magic (and some self-modifying code...), nealterrell and JoelCFC25 ported the entire catalog of icebreakers to a new system supporting the auto-increase of icebreaker strength. Any time you encounter an ice that your breaker can interact with, you will see a "Match strength with ____" button; click this, and the breaker will automatically apply its "increase strength" routine until it matches the target ice's strength. Corroder used to take __22 mouse clicks__ to break Curtain Wall; now it takes 2 to match strength and 3 to break!

![Match strength with Curtain Wall](https://cloud.githubusercontent.com/assets/10083341/8505138/b154cbce-2193-11e5-9dcd-b419abbbbf52.PNG)

_Weyland players consider this a dramatic nerf to Curtain Wall, whose carpal tunnel-inducing effect was perhaps the faction's greatest asset._

### Run credits


### Card hover preview

Newer players will appreciate DominicKexel's contribution of a simple card name highlight interface, allowing users to see an image of a card by hovering its name in chat and most other text areas. Simply hover any orange card name you see to pull up an image of that card. It took a few iterations to get right: Grimoire showed Grim, a user named "TheCodeTroll" showed artwork for "Troll" when hovered, etc... but the latest update is running smooth as butter and is a great addition to the usability of our interface.

![Card image highlights](https://cloud.githubusercontent.com/assets/10083341/8998141/8bf21f70-36de-11e5-85d1-46cf714f3eb8.png)

_You can't see it, but I'm hovering my house over "Merlin" in the chat box._

### Steal costs for agendas

NAPD Contract and Fetal AI had hard-coded mechanisms for adding additional costs to their steal cost; this month, nealterrell extended that mechanism to enable all cards that add additional costs to stealing agendas. 

![Red Herrings example](https://cloud.githubusercontent.com/assets/10083341/8890876/0c2fc678-32c6-11e5-82f9-c6abfeb265ba.PNG)

_Utopia Fragment, NAPD Contract, Predictive Algorithm, Red Herrings, Strongbox, The Source. This should be illegal._

## Other notables

1. Rez-cost modifications for IQ and "space ice" (Asteroid Belt, Nebula, Wormhole, Orion).
2. Better support for "the first time this turn" requirements, leading to more correct implementations of Near Earth Hub, Autoscripter, and Symmetrical Visage.
3. Last month we implemented Wyrm... this month, JoelCFC25 continues the "just in case _anyone_ uses these" mission with Hellion Alpha Test, Shiro, Monolith, and Helium-3 Deposit.
4. Joel also worked on fixing interactions between HQ Interface and Gang Sign / Raymond Flint. Multiple Gang Signs still give problems, but a single Sign will stack correctly with HQI.