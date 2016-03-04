Welcome to the Jinteki.net open-source progress report Fall 2015. In this quarterly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New contributors

Thanks to the new developers who added to the platform this quarter: Saintis, Zaroth, Kevkcc and Bmatsuo!

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by queueseven, JoelCFC25, and mtgred! Nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### Major system changes

#### Server performance

**TODO** neal's game list diffs. need a bandwidth graph.

#### Start of turn effects and step 4.3

A long-standing problem (#334) was inability for the game to properly reflect the 4.3 run window for Corp to rez cards just before access. Slowing down every run by adding an additional step was unacceptable from the usability point of view, however Nealterrell found a way to add it without adding much tedium.

While at it, he also reworked the way start-of-turn effects are handled - now you can properly bounce your Adonis Campaign with Blue Sun after taking credits or lose a credit from Drug Dealer before taking two from Daily Casts. The actual logic behind handling the start-of-turn effects can get a little complicated now - to learn more, see [#1218](https://github.com/mtgred/netrunner/pull/1218).

#### Toasts

There was a need to convey info to player without spamming the game log, discreetly, without notifying the other player. After some digging, we found that using [JS Toastr](https://github.com/CodeSeven/toastr) library is probably our best bet. Saintis did the work necessary to expose the toastr API to our game backend and voila, info galore:

![tennin toast](https://cloud.githubusercontent.com/assets/13198563/12204765/85b373de-b636-11e5-8b46-2b4bcf98a57a.png)
![pay-toast](https://cloud.githubusercontent.com/assets/13198563/12204678/eec72bf0-b635-11e5-81bb-4da12ab227d0.png)
![teamsponsorship](https://cloud.githubusercontent.com/assets/13198563/12204871/4b4ba03a-b637-11e5-8932-e288dafc9dc8.png)

#### Deckbuilder improvements - MWL and deck legality

New season brought us a number of curveballs when comes to deckbuilding: NAPD Most Wanted List, alliances and future spoilers. Zaroth stayed on the top of things, though and kept the deckbuilder up to date. Now you can see at a glance whether a deck is tournament legal and exactly why is that:

![status-tooltip](https://cloud.githubusercontent.com/assets/840021/13109413/511d56dc-d579-11e5-9762-2259eada4bee.png)

More details on legality labels can be found on [the help page](http://www.jinteki.net/help#napdmwl).

#### Lobby improvements

**TODO** - Zaroth faction icons [#1237](https://github.com/mtgred/netrunner/pull/1237), mention that full ID name is on icon hover


![5759a19e95878a41](https://cloud.githubusercontent.com/assets/840021/13052139/6f5c8776-d3fd-11e5-8f08-4574ce77b77b.png)

**TODO** - Zaroth competitive casual rooms [#1215](https://github.com/mtgred/netrunner/pull/1215), [help page](http://www.jinteki.net/help#competitive)

#### Waiting prompts

Since the start, there were problems with handling order of effects having the same trigger, or one player rushing through the actions, not waiting for the reaction trigger effects. Thanks to Nealterrell's PR [#1059](https://github.com/mtgred/netrunner/pull/1059), some of such interactions got way smoother now:

![image](https://cloud.githubusercontent.com/assets/10083341/12056074/ce762310-aee7-11e5-8fb4-78d3f83e3d24.png)

#### Fix for spectators moving cards

However we were able to be lax on security matters beforehand, as our community grew, we got our first griefers causing headache by joining games and using an exploit to ruin the game state. Thankfully we had Saintis who quickly stepped in with fixes in [#1048](https://github.com/mtgred/netrunner/pull/1048) and the most pressing issue with spectators ruining games got fixed. However, there is still a lot of work to be done in this area, so any contributors knowledgeable on web security are very welcome!

#### Other improvements and automations

##### Refactorings

No codebase can survive in a good state without some maintenance. Thanks to some code reorganization by Nealterrell and Saintis code is now easier to browse and modify.

##### Improved touch screen support

A long-standing PR by queueseven adding some basic drag&drop touch support was refreshed in [#1218](https://github.com/mtgred/netrunner/pull/1218). Although still not ideal, things should work better on tablets now.

##### Game list scroll to top - hunt of a most elusive bug

This one was a team effort - finding the reason why the lobby game list kept scrolling to top wasn't an easy task. However, it finally got fixed by domtancredit in #1147 and #1168.

##### Help page

To save ourselves the headache from answering the same questions in chat over, and over, Zaroth put together a neat [Help page](http://www.jinteki.net/help) with the most frequently asked questions. It didn't necessarily work, but still, some documentation on site is nice to have :)

##### New /commands

`/psi`, `/close-prompt`, `/trace`, `/rez-all` and more... Full list can be found on [the help page](http://www.jinteki.net/help#commands).

##### Ice subtype changes (Kit, morph)

**TODO** - JoelCFC25 [#1030](https://github.com/mtgred/netrunner/pull/1030)

##### Negative hand-size now properly flatlines runner

**TODO** - Saintis [#1053](https://github.com/mtgred/netrunner/pull/1053)

##### Finally fixed Sundew

**TODO** - Saintis [#1144](https://github.com/mtgred/netrunner/pull/1144)

##### Splits hand-size and hand-size modifiers to properly handle CI and Bagbiter

**TODO** - Saintis [#1133](https://github.com/mtgred/netrunner/pull/1133)

##### New card implementations

Mostly brought to you by JoelCFC25, the binder cards wizard:

* NEXT Design
* Chronos Protocol
* Replicating Perfection
* Kala Ghoda cards (18/20)

As of time of this writing, we are at [__97.0%__ card automation](https://docs.google.com/spreadsheets/d/1ICv19cNjSaW9C-DoEEGH3iFt09PBTob4CAutGex0gnE/pubhtml) through _Kala Ghoda_!

### Coming soon...

**TODO**

1. __REPLAYS__: step through every action during a past game.
2. Mumbad cycle???

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred (Worlds 2014 runner-up!) and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.