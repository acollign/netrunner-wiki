Welcome to the Jinteki.net open-source progress report for August 2015. In this report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform, plus preview some improvements we have in the works.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

## New contributors

We'll get to their submissions below, but let's take a moment to thank the new developers who completed a variety of improvements to the platform this month. In alphabetical order, huge thanks to AndrewHynes, dtelad11, domtancredi, JHonaker, Makai, and mharris717. JoeyKL also returned to implement some D&D cards!

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by DominicKexel, JoelCFC25, and mtgred! nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei.

## Major system changes

### Spectating!

Our valiant leader mtgred implemented a simple spectating system this month, allowing Jinteki.net users to watch others play their Netrunner games. Yes, it's been bumpy -- with players "warping" between different games, spectators messing with the game state, and a game-death bug leading to 140+ "active" games that almost killed the server... but the system is almost complete, and serves as a simple way for players of all skill levels to watch and learn from others, or for tournament officials to spectate matches.

### Manual access order

In the past, when a run was successful on a server with more than one card installed in it, or on a central server with an upgrade, Runners had to be satisfied with accessing those cards in whatever order the game server decided to allow. Lacking an interface for choosing which cards to access in which order (including such edge cases as "1 card from HQ, upgrade in HQ, another card in HQ") meant that complicated interactions like trashing Oaktown Grid before another asset, or accessing the game-winning agenda in Archives before the Shock!, could only be handled manually. nealterrell implemented a system for manual selection of access order, aimed at being as unobtrusive as possible any time the access order is obvious, e.g., when only accessing one card, or if all but 1 card has already been accessed. This was a big change for users, but necessary for strict enforcement of Netrunner rules, and brings us much closer to our goal of full game rule automation.

### Apex and Adam

queueseven brings us the new Runner hotness of Adam and Apex, two of the three Runner mini-factions from Data and Destiny. (The third, Sunny, is trivially implemented.) Major systems work went into implementing these Runners, so give him a shoutout in chat if you enjoy watching the world burn with Apocalypse or smashing into HQ to Neutralize All Threats.

### UI improvements

queueseven further demonstrates his programming versatility by introducing two UI improvements: end of turn summaries, and tag / MU highlights. We've all been Scorched from forgetting about a tag, but queue's new BLUE WARNING EXCLAMATION should help save a few absent-minded Runners from their grisly fate. His second improvement logs your credits and cards in hand at the start and end of every turn, making it easier to undo mistakes and keep track of your and your opponent's spending and drawing. (Users of OCTGN will note that platform's inspiration for these UI additions!)

![End of turn log](https://cloud.githubusercontent.com/assets/3126597/9522095/f0e175de-4cd2-11e5-8565-847557c014b6.png)

_Extra credit if you know why programmers use "foo" and "bar" as example names._

### Hosting... complete?

Last month we boldly claimed that hosting effects had been completed, so naturally we had ran into our two biggest hosting issues ever in August. The first involved nested hosting: if you had Off-Campus Apartment hosting The Supplier hosting a resource, then attempting to install the resource off The Supplier would create a duplicate card on your rig, allowing you to install the resource again the next turn. What a deal! Similar issues occurred with nested hosting of icebreakers (not saving updates to their strengths) and with Film Critic on Off-Campus hosting an agenda. (Once the Critic is done with the film, another just like it shows up in her office. I feel like there's a Hollywood joke here somewhere...) nealterrell had to ascend to a new plane of recursive consciousness to solve this problem, but that is his penance for claiming the system was complete in July.

![darwin](https://cloud.githubusercontent.com/assets/10083341/9596235/ea9a07ac-5026-11e5-870f-3b7f599fefeb.PNG)

_Can you go deeper?_

To understand the second issue, imagine working on the following task: you will be writing down a list of the whole numbers from 1 through 10, but each time you write down the next number in the sequence, adjacent to that number you will write a set of parentheses containing all the numbers currently in the list. You start by writing the number 1 with an empty set of parentheses, since there are no numbers currently in the list. Next you write a 2, with "(1)" following it, since 1 is already in the list. Then you write a 3, with "(1 2)" following. By the time you write down the 10, you have a list that looks like 

1 () 2 (1) 3 (1 2) 4 (1 2 3) 5 (1 2 3 4) 6 (1 2 3 4 5) 7 (1 2 3 4 5 6) 8 (1 2 3 4 5 6 7) 9 (1 2 3 4 5 6 7 8) 10 (1 2 3 4 5 6 7 8 9)

and you have written 55 individual numbers when you intended to only write 10. This is in essence what was happening with our hosting code: every time a new card X was hosted on card Y, X saved a copy of all the cards currently hosted on Y. With only a few cards hosted, this amounted to a few kilobytes of data sent with each game update... hardly catastrophic. But as players started cramming their Apartments with 7+ of their closest Connections, the amount of data being transmitted by the server grew exponentially, and games came crawling to a hault. queueseven diagnosed the issue and nealterrell implemented the fix, along with other improvements to the networking code as a way of apologizing to the server for the wasted data.

### Notable new automations

1. Wreck those perfectly-constructed glacier servers with Escher.
2. STOP those dirty attempts at wrecking your servers with Crisium Grid.
3. Someone, please, turn off that damn Noise with Cerebral Static.
4. SO MANY cards from Old Hollywood, Universe of Tomorrow, and Data and Destiny, courtesy of mtgred, JoelCFC25, JoeyKL, and queueseven. Seriously, didn't anyone think of the poor coders before releasing all three packs at once?
5. Get your Forgers, City Halls and Decoys ready, because tag avoidance is now here.

### How to play...

How to play some of our new cards:

1. Nasir Meidan: when you are ready to lose all your credits, click Nasir to set credits to 0 and then automatically gain the rez cost of the ice you are approaching.
2. Crisium Grid: rez prior to hitting No Action when the runner has passed the last ice.
3. Film Critic: click when you get the "You accessed ____" prompt. This is also when you can click Imp to trash the accessed card.
4. Jackson Howard: new implementation, click Archives to open and then select 3 cards to shuffle. If you want to shuffle less than 3, click Done when ready.
5. Utopia/Eden/Hades Shards: when you get the Successful Run prompt, click the Shard from hand to install it for 0 cost and end the run.
6. Adam: include 1 copy of each directive in your deck; they will be installed at the start of the game automatically. Those 3 cards are not part of your deck for minimum-count purposes. You may include more copies if you want them in your deck.
7. Apex: install things, destroy things.
8. Cerebral Static: does not stop Runner from clicking his/her identity to activate it; only stops automated effects from triggering. Likewise for Employee Strike (D&D).
9. Executive Boot Camp: click to trigger before clicking Start Turn, as with Blue Sun.

### Joel's "Don't You Forget About Me" Corner

You forgot about these cards, but they haven't forgotten about you. JoelCFC25, nealterrell, and queueseven bring you this month's collection of "down but not out" card implementations: Bad Times, Exploratory Romp, Project Ares, Sunset, Tenma Line, Howler, and Data Hound.

Minh: spectating! OH & UoT, run credits cleanup

Joel: several Universe of Tomorrow/Data & Destiny cards, bring a few cards to full implementation (Muertos Gang Member, Docklands Crackdown, Executive Boot Camp), fixed cards (Exile, Order of Sol), and of course breaking new ground with more terrible cards that no one uses (Bad Times, Exploratory Romp, Project Ares, Sunset, Nasir Meidan)

MisterMentat (John Honaker): Fixing card names being shown when moved back onto the stack (i.e. Rolodex), fixed nested prompts with choices (May abilities that selected cards), fixed cards that fired on first time events (Hayley, Comet), reworked `:optional` abilities that caused me to briefly break (and then fix) trashing cards and scoring costs. Started working on everyone's favorite identity Jinteki: Replicating Perfection and the associated refactoring of `:register` keys to `:restrictions`.

Justin Liew: Working through all the implications of recurring credits. I think I have a solid design that balances manual payment but enforces proper context/usage. Hopefully will have more to report next month!

Dominic (queueseven): Fixed/improved some cards (i.e. Paintbrush, Datahound, Hostage, Sherlock 1.0 and some others), implemented a bunch of cards (Amazon Industrial Zone, Recruiting Trip, Paige Piper, all Apex cards, Adam, Adam's directives and some others), implemented cloud ice-breakers, improved the message log to show the number of cards in hand and credits when a turn starts or ends, visual indicator when your memory is < 0 and when the runner is tagged. Set up a Trello board to better coordinate our work.