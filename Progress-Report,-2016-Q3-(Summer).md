Welcome to the Jinteki.net open-source progress report for Q3 2016. In this quarterly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New Contributors
Thanks to the new developers who added to the platform this quarter: carlsondc, Devencire, and Hoclor!

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by queueseven, JoelCFC25, and mtgred! nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### Major System Changes

#### Never Trust the Client

There's a saying in client-server software development: "Never trust the client." When one computer system (the client) sends commands to a central authority computer (the server), a well-designed server will make sure to validate every part of the message received from the client, in recognition of how easy it is for a malicious user to send fake or altered messages from a compromised client. Video games have dealt with this issue for decades; a first-person shooter client will be programmed to not allow a gun to fire if it is out of ammo, but if the server doesn't double-check messages coming from the client, a hacker can send illicit "fire gun" messages to trick the server into letting the player fire even when the client wouldn't allow the action. In Jinteki.net, our clients (your web browser) send commands like "play card X", "select card Y", and "run server Z" to our game logic server.

Until recently, our game logic server assumed that all commands coming from the client were valid, because it's much faster to program a server under this assumption, and there are relatively few incentives for someone to cheat on our service. But the potential was there. A JavaScript hacker could easily change our client-side code to do lots of malicious behavior... for example, sending a command "`play-card {:title "Hedge Fund", :cost 0}`". A server that trusts the client will take the `:cost` of the transmitted card, deduct the player's credits by that amount, and then invoke the card's programmed logic... in this case, creating a Hedge Fund that gained 9cr but cost 0cr to play. Card __behavior__ can't be changed this way, only its __data__, but this allowed easy hacks to change card play costs, trash costs, agenda point values, and more. 

Trusting the client also means sending the entire game state to the client and allowing it to decide what to reveal to the player. If a first-person shooter sends all information about the entire game to a client, it has to trust the client to not reveal positions of non-visible enemies... but a client can easily be compromised to read this information and show it, in what is usually called a "wall hack". Likewise, we historically sent the entire game state to each player in a game, and wrote the client to draw unrezzed corp cards, the opponent's deck, and the opponent's hand face-down. Those cards' information was still in the state, though, and a JavaScript hacker could once again violate the rules of the game by inspecting that state to see the contents of cards in servers, in the opponent's hand, on top of R&D... the potential abuses are endless and game-breaking.

So what do we do? We Never Trust the Client. nealterrell rewrote the game logic server to fix two security issues:

1. Don't trust the card sent by the client as part of a command. Instead load the server-side version of that card and use it. This goes for deckbuilding (don't trust the decklist sent at the start of the game; reload each card in the deck from the database, based on its card number) and for in-game commands, like the Hedge Fund example above, which would load the appropriate data for Hedge Fund straight from the server's copy of the player's hand. 
2. Don't send private information to a player if they don't have a game-rules reason to know it. The runner does not have a right to know any identifiable information about an unrezzed Corp card, so we construct a special state for the Runner that strips out all information about such a "private" card except the card's identifier (a unique integer that is different each game) and its zone (to allow it to be drawn and targeted by the UI). This means constructing three different states for each command handled by the game server (Corp's, Runner's, Spectators'), but fixes any potential abuse from peeking at private information through the game state.

We have no evidence that anyone was cheating prior to these upgrades, but this is the proper way to program a client-server game, so it's about time we got around to it.

#### Simultaneous Triggers

runner-install (Replicator/Bazaar gif) [Don't stare for too long.](https://gfycat.com/WhimsicalBrilliantAcornbarnacle)

successful-run

#### Agenda overhaul (kevkcc #1847)

#### More Prevention Effects

Prevention effects are the only real "interrupt" mechanism in Android: Netrunner. Many Progress Reports have chronicled our implementations of the game's "prevent/avoid" effects, including:

1. Damage prevention (Plascrete Carapace)
2. Tag avoidance (New Angeles City Hall)
3. Trash prevention (Fall Guy)

What's missing? Take a minute...

Perhaps you guessed it:

1. Jack-out prevention. Normally you can just trust the Runner to not hit Jack Out after running through an Inazuma, but Labyrinthine Servers requires the ability to interrupt an active attempt to jack out. kevkcc implemented jack-out prevention just for this amazing card!
2. Exposure prevention. mtgred tasked nealterrell with implementing this feature via Zaibatsu Loyalty and Underway Grid. __WORLDS DECK ALERT???__


#### Full Immersion RecStudio and Corp Hosting

Full Immersion RecStudio was an interesting challenge. An immediate example of a similar Runner card is Leprechaun. We implement Leprechaun with two abilities: one to install and host a card from hand (costing 1 click), and another to host an already-installed card onto Leprechaun (costing 0 clicks). The second ability is necessary for interactions with Clone Chip, Retrieval Run, or any other "install a card from somewhere other than the Grip" ability. Corps have similar abilities via Interns, License Acquisition, and Team Sponsorship, but it was decided that Full Immersion RecStudio __could not__ rely on the same trick as Leprechaun to allow a card installed by one of these sources to be "moved" onto FIR. 

I'll give you thirty seconds to think of an incorrect rules interaction that would occur if FIR behaved the same way that Leprechaun does...

...

...

If you mentioned the words "NEH", "Astrolabe" or "Turtlebacks", then you are correct! If the solution for using Interns to install onto FIR involved using Interns to create a new server and then using FIR to host the already-installed card, then we would have incorrect rules interactions with any card that triggers "whenever the Corp creates a server". NEH and Astrolabe are irreversibly wrong (revealing the next card in the draw pile), so we need a proper solution to allow the Corp to use a card ability to install an Asset or Agenda on Full Immersion RecStudio.

Each time a Corp card is installed without specifying a server (through card effects, not the "Install from hand" action), we show a list of servers where the card can be installed. We now also allow rezzed Corp cards to announce that they can _host_ the card-to-be-installed if it meets certain criteria. This allows FIR to show in the list of target locations when installing an Adonis Campaign via Interns, as such:

![](https://cloud.githubusercontent.com/assets/10083341/18610442/53d8f1a6-7cd1-11e6-9355-8fe006d32379.png)

Fun fact of the day: Runner cards can host Runner cards (Djinn), Runner cards can be hosted on Runner cards (Personal Touch), Runner cards can host Corp cards (Film Critic), and Runner cards can be hosted on Corp cards (Parasite); Corp cards can host Corp cards (Worlds Plaza), Corp cards can be hosted on Corp cards (Oversight AI), and Corp cards can host Runner cards (Magnet)... but there are no Corp cards that can be hosted on (installed on) Runner cards.

Neal's example card in this unused design space:

__Segfault__ 1cr<br>
Operation<br>
Play only if the runner is tagged. Install Segfault on an __icebreaker__ as a hosted condition counter with the text "Host __icebreaker__ has -3 strength. Trash Segfault if the runner is not tagged."

#### Report Error button

#### SSCI and Show Hands to Spectators




### Other Improvements and Automations

* FAQ 3.1 changes to searching.
* Subliminal Messaging (kevkcc)

/counter rework

/move-bottom

NRDB API 2.0

Announcements


#### UI Improvements

Improved messaging when installing corp cards (Devencire, #1692)

Consistent sorting of corp server list (Devencire #1718)

Icons: Security Testing / Patron, Femme (Saintis #1748)

Don't show ID for private games

Forced action menu for ice and agendas outside runs (Saintis #1747)

"Null" message crash

Cards trashed from damage (Saintis)

Adam pull cards from server

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.