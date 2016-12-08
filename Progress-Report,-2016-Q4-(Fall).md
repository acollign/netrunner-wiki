Welcome to the Jinteki.net open-source progress report for Q4 2016. In this quarterly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New Contributors
Thanks to the new developers who added to the platform this quarter: @erbridge, @benhosp, @poi2000, @DeepSpawn, @kevinpowe, @dylancwood, @mattchainsaw, @kleer1, @cryogen

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by queueseven, JoelCFC25, and mtgred! nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### Major System Changes

#### Sounds

#### My Account

#### Workflow changes

#### Agenda access rework (Saintis, #1985)

### Other Improvements and Automations

* Adam Directives before game start
* ELP

#### Implementation warnings

#### Medium + Nerve Agent
A Neal and Joel tag-team effort to beautify the Medium and Nerve Agent code, which was a nasty pile of spaghetti-like workarounds. These cards now prompt the user with a default selection of the maximum number of additional accesses and perform their effects at the newly wait-completion enabled `:pre-access` event. Users can now benefit from Nerve Agent counters when non-run access from Gang Sign or Raymond Flint occurs.

#### New commands

#### UI improvements



#### Notable new card implementations
erbridge: Omar Keung, automatic Crypsis counter removal after use
Joel: Raman Rai _(yes, really)_, Credit Crash, Fumiko Yamamori
Neal: Top Hat, Baba Yaga
mattchainsaw: the long-awaited Enhanced Login Protocol!
kevkcc: Frantic Coding

As of time of this writing, we are at [__98.0%__ card automation](https://docs.google.com/spreadsheets/d/1ICv19cNjSaW9C-DoEEGH3iFt09PBTob4CAutGex0gnE/pubhtml) card automation through _Martial Law_!

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.