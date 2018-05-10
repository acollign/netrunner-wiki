Welcome to the Jinteki.net open-source progress report for Q1 2018. It has been a long time (18 months since the last one).  

Myself (danhut) was not around on the project back then, and we have some new blood who want to take this over so I thought this an opportune time since I worked throughout 2017 on this site, along with the ever present JoelCFC25 and from mid-year JWarwick.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### Contributors
I think I joined the project in its darkest days... in late December 2016.  Maybe it was dumb cards like Sifr and Temmy-J which contributed to this.  Much of the work in keeping up with cards in 2017 can be attributed to JoelCFC25, with myself chipping in as best as possible while I got to understand how the hell it all worked.  JWarwick joined I think mid-year and started kicking goals on the web page & some larger system issues.

This year has been exciting so far with NealTerrell seeming to be back - who is the all-time guru of everything on this site and programming in general.  And Saintis also seems fired up too.  Great stuff.

And we have awesome new blood who seem very keen - welcome NoahTheDuke and nicohasa.  Great to have you and looking forward to working more with you.  Excellent contributions so far.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### What happened in 2017 and early 2018?

Well a load of stuff.  

#### A Ton of Cards got implemented

#### Bug Fixes
This game is hard.  The interactions and logic is mind blowing in real life.  Now try coding it?  We try our best!!  But you can help by filing bugs that explain what the problem is and how to reproduce it.  

#### Deck Builder Renovations
* April 2017 - Re-write of Deck Builder to handle MWL
* May 2017 - Upgrade Deck Builder to support Cache Refresh
* March 2018  - UI usability tweaks in Deckbuilder
* April 2018 - added support for the community variant Modded.  Which @NealTerrell really loves the name of.  Really.

#### Game Play Tweaks
* May 2017 - Upgrade card selection prompt to enforce a minimum number of choices
* Sept 2017 - Opponent is typing indicator added into UI
* Oct 2017 - Enable mute/unmute spectators in game
* April 2018 - Undo functions added to game.  Even though I added this myself I think it feels magical! /undo-click and /undo-turn are your new friends!

#### Other web page things
* Sept 2017 - Upgrade Card Browser to include Alternate Art cards
* Sept 2017 - Upgrade Chat to have card names linked / display card pictures
* Sept 2017 - Add Block Users feature to ignore annoying players
* Oct 2017 - Implemented Statistics System
* Oct 2017 - Upgrade Card Browser to include card text
* Feb 2018 - Pre-Built Decks added for new players

#### Logic and engine upgrades
* April 2017 - Upgrade Payment system to handle additional costs - such as for Obokata Protocol, Donut Taganes,  and Student Loans
* May 2017 - FAQ 4.0 changes
* October 2017 - Support for Core 2.0
* Dec 2017 - FAQ 4.1 changes
* Feb 2018 - Re-write of code to handle central server access
* Mar 2018 - payment system upgraded to handle async operations requiring user input.  Needed to fix a bunch of cards.
* April 2018 - Upgrade Payment system to block payment of certain costs (eg. don't permit paying Net damage with Guru Davinder installed)

#### Huge Project mostly by Neal
The project used to be written in Clojure for the back-end, Clojurescript for the front-end - and then this awful coffeescript thing for the web server.  This was a problem as no one understood the language.  The amazing NealTerrell re-wrote it all in native Clojure so the rest of us (sort of) understand it now.

#### Ongoing: War with Titanium Ribs and Maw.  One day maybe... these ... will ... fix or rotate first?

### Future plans?
There are a bunch of ideas here many of which will help us maintain the site easier and better.  

At least what is in my mind...
* Rework delayed-completion system where we need user input when firing card effects.  Right now this is hard for the developers to work with.  My opinion ;)
* Rework the browser lock system to better wait for responses from server.  Right now this can result in a card like Cache paying out more than it should.
* Deck Builder integration with NRDB (@John Warwick).
* Add some short-cut buttons to UI (I am thinking... Please pause... etc)
* Maybe re-write UI engine.  We use a library called OM which is no longer maintained.  Not a huge problem but I think we re-render the UI too often in too many places.  It would look the same as now anyway.
* Add support for friends and private chat
* Fix Titanium Ribs and Maw(sif...)

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.