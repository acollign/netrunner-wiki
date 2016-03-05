Welcome to the Jinteki.net open-source progress report for Q1 2016. In this quarterly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New Contributors

Thanks to the new developers who added to the platform this quarter: bmatsuo, haplesshero13, jaerme, kevkcc, proland, Saintis, and zaroth!

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by queueseven, JoelCFC25, and mtgred! Nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### Major System Changes

#### Server Performance

The five of you who read these reports thoroughly are probably sick of hearing about bandwidth improvements... but all the amazing feature additions over the last 6 months have exploded Jinteki.net's user base, and we need constant improvements to server performance to keep up with the demand. You may remember server announcements in January discouraging people from playing when there were 80+ games active; you may also remember those announcements disappearing shortly thereafter. That lag spike was, once again, a product of sending too much data. But with all the upgrades detailed in [last quarter's progress report](https://github.com/mtgred/netrunner/wiki/Progress-Report,-2015-Q4-(Fall)#server-performance), where would we turn to squeeze out more performance?

The answer was to apply the same "diff" communication structure (detailed last time) to our game lobby list. A game lobby of 80 active games comes out to 80Kb+ of JSON data; with 160 active players, 20 spectators, and 70 lurkers, we're looking at pushing 250 * 80Kb = 20Mb of game lobby data per second. (That's 1.2 Gb per minute, 72 Gb per hour, completely ignoring the actual game data.) With a "diff" approach, we only push game data games that have been created, updated, or closed in the last 1 second. A game update or closing clocks in around 500 bytes; a new game, around 10Kb. 

#### Start of turn effects and run Step 4.3

A long-standing problem (#334) was the inability for the game to properly reflect the 4.3 run window for the Corp to rez cards after the Runner had committed to access but before the run was successful. Slowing down every run by adding an additional step was unacceptable from a usability point of view, but Nealterrell found a way to do it without adding much tedium.

While he was at it, he also reworked the way start-of-turn effects are handled--now you can properly bounce your Adonis Campaign with Blue Sun after taking credits or lose 1 credit from Drug Dealer before gaining 2 from Daily Casts. The actual logic behind handling the start-of-turn effects can get a little complicated now--to learn more, see the documentation in [#1218](https://github.com/mtgred/netrunner/pull/1218).

#### Toasts

There was a need to discreetly convey information to a single player without spamming the game log and alerting the opponent to things that should remain hidden. After some digging, we found that using [JS Toastr](https://github.com/CodeSeven/toastr) library is probably our best bet. Saintis did the work necessary to expose the Toastr API to our game backend and _voila_, info galore:

![tennin toast](https://cloud.githubusercontent.com/assets/13198563/12204765/85b373de-b636-11e5-8b46-2b4bcf98a57a.png)
![pay-toast](https://cloud.githubusercontent.com/assets/13198563/12204678/eec72bf0-b635-11e5-81bb-4da12ab227d0.png)
![teamsponsorship](https://cloud.githubusercontent.com/assets/13198563/12204871/4b4ba03a-b637-11e5-8932-e288dafc9dc8.png)

#### Deckbuilder improvements - MWL and deck legality

The new FFG Organized Play season brought us a number of curveballs when it came to deckbuilding: the NAPD Most Wanted List, alliances, and future spoilers. Zaroth stayed on the top of things and kept the deckbuilder up to date. Now you can see at a glance a deck's legality and the exact reasons why it gets its status:

![status-tooltip](https://cloud.githubusercontent.com/assets/840021/13109413/511d56dc-d579-11e5-9762-2259eada4bee.png)

More details on legality labels can be found on [the help page](http://www.jinteki.net/help#napdmwl).

#### Lobby Improvements

**TODO** - Zaroth faction icons [#1237](https://github.com/mtgred/netrunner/pull/1237), mention that full ID name is on icon hover


![5759a19e95878a41](https://cloud.githubusercontent.com/assets/840021/13052139/6f5c8776-d3fd-11e5-8f08-4574ce77b77b.png)

**TODO** - Zaroth competitive casual rooms [#1215](https://github.com/mtgred/netrunner/pull/1215), [help page](http://www.jinteki.net/help#competitive)

#### Waiting prompts

Since the start, there were problems with handling order of effects having the same trigger, or one player rushing through the actions, not waiting for the reaction trigger effects. Thanks to Nealterrell's PR [#1059](https://github.com/mtgred/netrunner/pull/1059), some of such interactions got way smoother now:

![image](https://cloud.githubusercontent.com/assets/10083341/12056074/ce762310-aee7-11e5-8fb4-78d3f83e3d24.png)

#### Fix for spectators moving cards

However we were able to be lax on security matters beforehand, as our community grew, we got our first griefers causing headache by joining games and using an exploit to ruin the game state. Thankfully we had Saintis who quickly stepped in with fixes in [#1048](https://github.com/mtgred/netrunner/pull/1048) and the most pressing issue with spectators ruining games got fixed. However, there is still a lot of work to be done in this area, so any contributors knowledgeable on web security are very welcome!

#### Other Improvements and Automations

##### Refactorings and Testing Framework

No codebase can survive in a good state without some maintenance. Thanks to some code reorganization by Nealterrell and Saintis, the code is now easier to browse and modify and is much more fully documented. There have also been a number of convenient functions added to the unit testing framework to speed the process of writing tests. The release lull in between Data & Destiny and Kala Ghoda became an opportunity to flesh out the testing framework with dozens more card tests. 

##### Improved Touchscreen Support

A long-standing PR by queueseven adding some basic drag&drop touch support was refreshed in [#1218](https://github.com/mtgred/netrunner/pull/1218). Although still not ideal, things should work better on tablets now.

##### Game List scroll to top (a most elusive bug)

This one was a team effort--finding the reason why the lobby game list kept scrolling to top wasn't an easy task. However, it finally got fixed by domtancredi in #1147 and #1168.

##### Help Page

To save ourselves the headache of answering the same questions in chat over and over, Zaroth put together a terrific [Help page](http://www.jinteki.net/help) with the most frequently asked questions. Users still don't always think to look at it, but it's still nice having more thorough documentation on the site!

##### New Console Commands

`/psi`, `/close-prompt`, `/trace`, `/rez-all` and more... Full list can be found on [the help page](http://www.jinteki.net/help#commands).

##### ICE subtype changes (Kit, Morph)

JoelCFC25 completed most of the remaining tasks for full ICE subtype handling in [#1030](https://github.com/mtgred/netrunner/pull/1030) with the implementation of the flip-flopping subtypes of the Weyland Morph ICE. Tinkering, Paintbrush, Sub Boost, Rielle "Kit" Peddler, and Chimera also received improvements related to subtypes and handling ICE strength.

##### Negative hand size flatlines the Runner

Saintis changed the runner end of turn check to include the hand size in [#1053](https://github.com/mtgred/netrunner/pull/1053). If the runner's hand size is negative they are now properly flatlined.

##### Split hand size and hand size modifiers

To properly implement Cerebral Imaging and Theophilius Bagbiter Saintis split the previous max-hand-size variable into base hand size and hand size modifier ([#1133](https://github.com/mtgred/netrunner/pull/1133)). This means that a runner with Bagbiter and Public Sympathy installed will always have a hand size of their credits plus 2. 

##### Longstanding Issues

* Apocalypse: correctly restores MU and no longer triggers many leave-play effects.
* Chameleon: will return to hand if hosted on another card, thanks to kevkvcc.
* Executive assets: JoelCFC25 tracked down a longtime bug with Executives ending up in the Runner's score area when voluntarily trashed by the Corp... blame your local Film Critic!
* Femme Fatale: Saintis added a visually icon to ICE chosen for bypass with Femme Fatale. Similarly, we added labels to 
Cyber-Cypher's server selection.
* Gagarin Deep Space: Runners can decline to access a single-card remote against Gagarin.
* Hostile Infrastructure: deals one large chunk of damage during interaction with Apocalypse and Singularity, instead of many 1-damage chunks.
* Leela Patel: two key issues fixed by nealterrell. If a card is added to HQ by Leela in the middle of multi-access on HQ, subsequent access attempts can potentially choose that returned card. A specific interaction with Gang Sign was also fixed, so that Leela can in some situations "store" a use of her ability until Gang Sign finishes resolving.
* Sneakdoor Beta: several key interactions have been fixed, including with Crisium Grid (on Archives, stops the switch to HQ; on HQ, prevents Desperado/other successful run triggers); Security Testing (grants credits if on HQ); Ash 2X3ZB9CY on HQ (trigger Ash before the runner hits Successful Run); and Nerve Agent.
* Sundew: you can stop manually adjusting your credits... Saintis fixed Sundew's credit-gain from runs initiated on its server.


##### New Card Implementations

Notable new automations:

* Identities: NEXT Design _(JoelCFC25)_, Chronos Protocol _(justinliew)_, __Replicating Perfection__ _(Saintis)_
* Agendas: 15 Minutes _(zaroth)_, Eden Fragment _(bmatsuo)_
* Tricky implementations: Off the Grid _(Saintis)_, Gene Conditioning Shoppe _(proland)_, Deep Red _(JoelCFC25)_, Genetics Pavilion _(JoelCFC25)_
* Wait... what's that card do: Port Anson Grid _(JoelCFC25)_, Toshiyuki Sakai _(JoelCFC25)_
* Kala Ghoda cards (18/20)

As of time of this writing, we are at [__97.1%__ card automation](https://docs.google.com/spreadsheets/d/1ICv19cNjSaW9C-DoEEGH3iFt09PBTob4CAutGex0gnE/pubhtml) through _Kala Ghoda_!

### Coming Soon...

**TODO**

1. __REPLAYS__: step through every action during a past game.
2. Mumbad cycle???

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred (Worlds 2014 runner-up!) and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.