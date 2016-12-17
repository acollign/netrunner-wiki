Welcome to the Jinteki.net open-source progress report for Q4 2016. In this quarterly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New Contributors
Thanks to the new developers who added to the platform this quarter: benhosp, DeepSpawn, dylancwood, erbridge, kevinpowe, kleer1, mattchainsaw, poi2000

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by queueseven, JoelCFC25, and mtgred! nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### Major System Changes

#### Sounds

You've been asking for over a year, and we can finally deliver. As of December 11, sounds have come to Jinteki.net! krystman and his "sound guy" were very kind to donate the first iteration of Jinteki sounds to plug into Zaroth's sound framework from our April Fools event.  We are collecting feedback [here](https://github.com/mtgred/netrunner/issues/968), so please leave your thoughts after playing a few games with the new effects. If the sounds aren't to your liking, you can turn them off in your My Account settings (see below).

Sounds are currently tied to these in-game events:

* Score agenda
* Steal agenda
* Click-advance
* Click-draw
* Click-credit
* Click-remove tag
* Click-run
* Game ends
* Corp install
* Runner install
* Play Event/Operation
* Rez ice
* Rez non-ice
* Run successful
* Purge viruses

#### My Account

Alongside sounds, our early December deploy included a new My Account page for logged-in users to adjust a few _minor_ account settings. nealterrell resurrected domtancredi's older work on a profile page to implement this modest group of settings. For now, My Account will let you:

* Change your avatar via a link to Gravatar
* Enable or disable sounds (saved as a local browser setting via JavaScript localStorage) and adjust their volume
* Choose a background image for the gameboard from a few presets
* Choose to show or hide your opponents' alt-art card images (new users, or streamers catering to new users, should turn off alt-art)

We also implemented an alt-art editor for users who have donated to the site. In the past, donors would see all their cards replaced by alt-art images (when they existed), even if they preferred the original Cypsis art. A new editor in My Account lets these kind supporters of our site choose which of their cards show as original artwork and which show as alternate arts. We've even included the option to choose images from the World Champion 2015 full-bleed artwork cards!

![Alt-art editor](https://cloud.githubusercontent.com/assets/10083341/21082515/c888c5aa-bf91-11e6-981c-ade400649784.png)

_Alternate and World Champion 2015 artwork for Haas-Bioroid: Engineering the Future_

#### Agenda access rework (Saintis, #1985)

#### Workflow changes

A lot of work this quarter went into features that you'll never notice on our production server. Zaroth, erbridge, and domtancredi collaborated heavily to configure a developer's test server, where new features and ideas can be tried in a production-like environment prior to merging into our master codebase. (You'd be shocked how many submissions work fine on local developer machines but fail when deployed to live.) We use a [Puppet](https://github.com/zaroth/betajin-pupfiles) configuration maintained by Zaroth which downloads and installs all the packages needed to run a Jinteki instance; this configuration is deployed to a machine commissioned by domtancredi at [our dev server URL](http://jinteki.zaroth.net). Contributors can submit code to our "dev" branch on GitHub, and once submitted a project collaborator can deploy the submitted code to the dev server with a simple shell script.

The dev server itself runs a separate database from the live site, but otherwise is perfectly fine to play on. We only recommend using it if the main site is down for some reason, as like all "in development" projects, the code running on dev is more likely to be unstable or incomplete. 

### Other Improvements and Automations

* Adam Directives before game start
* ELP
* Edward Kim and trashable operations (benhosp)
* Omni-drive memory refund (poi2000)

#### Implementation warnings

#### Medium + Nerve Agent
A Neal and Joel tag-team effort to beautify the Medium and Nerve Agent code, which was a nasty pile of spaghetti-like workarounds. These cards now prompt the user with a default selection of the maximum number of additional accesses (did you know you can choose less than the maximum amount to access?) and perform their effects during a new "pre-access" event in the game engine. Users can now benefit from Nerve Agent counters when non-run access from Gang Sign or Raymond Flint occurs.

#### UI improvements



#### Notable new card implementations
* **erbridge:** Omar Keung
* **JoelCFC25:** Raman Rai _(yes, really)_, Credit Crash, Fumiko Yamamori
* **nealterrell:** Top Hat, Baba Yaga
* **mattchainsaw:** the long-awaited Enhanced Login Protocol!
* **kevkcc:** Frantic Coding

As of time of this writing, we are at [__98.4%__ card automation](https://docs.google.com/spreadsheets/d/1ICv19cNjSaW9C-DoEEGH3iFt09PBTob4CAutGex0gnE/pubhtml) card automation through _Martial Law_!

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.