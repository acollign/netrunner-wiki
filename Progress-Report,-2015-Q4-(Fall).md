Welcome to the Jinteki.net open-source progress report Fall 2015. In this quarterly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New contributors

Thanks to the new developers who added to the platform this quarter: Clamatius, dersam, and GordonsBeard!

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by queueseven, JoelCFC25, and mtgred! nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### Major system changes

#### Server performance

We have documented several performance upgrades in past progress reports; before getting into our latest improvements, let's appreciate how far the platform has come. Early adopters (Q1 2015) of Jinteki.net will remember a server that struggled to run more than 10 games at once, with chat room arguments whenever someone dared start a game past that performance threshold. Server slowness at the time wasn't because of slow algorithms, bad data structure choices, or poor language performance. We were just pushing too much data.

Actions and responses are sent to and from the game server in JSON by your web browser. In this format -- which includes a lot of data you don't "see" in a written decklist, like card costs, influence amounts, faction ownership etc. -- a typical Netrunner deck can be represented in about 20Kb of data, and the "state" of a full game can run to over 70Kb for long games. If you aren't careful (which we weren't, being more concerned with finishing the game engine itself), then data itself becomes the bottleneck of the application; frequent game updates being sent to dozens of clients without any thought to optimization overloads the server's network card, causing "lag" unlike that seen by your desktop PC, whose struggles with an application or game is typically tied to CPU, memory, or GPU factors.

This year we have detailed several improvements to server performance strictly by reducing the amount of data we send:

1. In June we stopped sending decklists for games that are in progress, as that information is only needed when the game is being set up and players are selecting decks.
2. In August we fixed issues with hosting that caused exponential increases to game states when hosting many cards on one host. Off-Campus Apartment was all the rage, and loading one Apartment brought the entire server to a halt with every network push.
3. In September we implemented something called WebSocket permessage-deflate compression, which in effect "zips" game data as it is sent from the server to your client. This trades a little bit of CPU power on both ends for a significant (~80%) decrease in data sizes. It took a few days for the code to get fully distributed, and we had some hiccups along the way, but the compression's effect on performance was immediately noticed.
4. Later in September we made a simple optimization to only send the list of active games at most once per second, rather than on every game update (player joins, leaves, selects a deck, creates a new game, etc.). This is something we only needed because the site was getting more popular!
5. The most recent upgrade was in October, when nealterrell implemented a "diff" mechanism for game states. When the site was young we could get away with sending a game's entire state to each player after every action; the simplicity of this approach allows easy early development, but as games became more complicated (and MUCH more numerous) these large state structures became a serious network hog.
    Think about it... when someone types "Hello" in a game chat, do you _really_ need to send a network update containing the ENTIRE game state to each player? No -- this state contains information that hasn't changed, like what's in each player's hand, when what we're really interested in is what information HAS changed. In this spirit, we implemented what all well-designed scalable networked programs use to communicate state changes: "diffs". 
    A diff is a __difference__ between an old state and a new state. When you click a card in hand to play it, your browser sends a "play" command to the server. The server calculates the effects of that command by applying game rules programmed for the card you played. The effects are applied to the current game state, after which we compare the new state to the old and calculate a list of things that have changed. This calculation produces a "diff"; we send that diff, rather than the entire new state, to each client. Upon receiving the diff, your client applies the diff to your local copy of the game state, producing the exact same state that was produced on the server. Both client and server are back in sync with identical state knowledge without having to send the entire state structure on each game udpate.

The net effect of sending diffs is immediately obvious. Without diffs, each of these actions sends the entire game state (30-70Kb depending on what's been played). With diffs, we see sizes like these:

* Drawing a card: 10Kb (depends on how many cards are left in deck/hand)
* Playing an event: 0.5Kb
* Installing a card: 1.5Kb
* End turn: 0.5Kb
* New chat message: 0.4Kb
* Each step of a run: 0.5Kb
* Accessing a card: 1Kb

etc. Quite a change!

The big picture is that we used to struggle with 10 games, but now regularly see 50-60 games in progress without any server lag. We still see lag on occasion, but it's usually related to poor card implementations, rather than the architecture itself. Game on!

![image](https://cloud.githubusercontent.com/assets/10083341/11727938/ce81de28-9f3b-11e5-8a54-6c8721992951.png)

_June: remove decks from game list. October: caching issues with WebSocket compression. Late October: state diffs._

#### Targeting changes

JoelCFC25 and nealterrell collaborated to clean up a lot of our prompt menu actions. Our platform offers two types of user "prompts": one prompt type, a "choice" prompt, shows a list of buttons with names on them, and the user chooses one; the second "selection" prompt requires the user to click a card matching some requirement. Inconsistencies in which prompt is used for which effect has lead to a lot of confusion... why does Modded use a Choice prompt to select a card name, but Career Fair use a Selection prompt to target the card in your hand? It makes no sense.

We decided on these guidelines for the two prompts:

* If you can see the card you want to affect (because it is installed, in your hand, or in your trash), then use a Selection prompt. A targeting cursor appears, making it clear the user needs to click on their desired target. To cancel this operation, click "Done" instead of a card.
* If you can't see the card you want to affect (it is in your deck), use a Choice prompt. If the operation is cancellable, a "Cancel" button will allow you to back out of the operation... say, if you accidentally clicked your Self-modifying Code. Any time the Choices come from your deck, they will be sorted alphabetically to not give away information about card order illegally.
* If your effect does not involve another card but simply a list of options, use a Choice prompt.

We hope our users find this more consistent and appreciate the ability to cancel the majority of prompt interactions.

![image](https://cloud.githubusercontent.com/assets/10083341/11728223/8266fad0-9f3d-11e5-9859-ce1fab085ba7.png)

_Targeting Gordian Blade with Modded._

#### De-automations

This might sound like an odd feature to report, but we have moved recently towards de-automating some tricky cards. The cards still function, they just typically require input from the user (usually by clicking the card) to activate, instead of triggering automatically. These cards were -- for many reasons -- difficult to fully integrate with every edge case and interaction, so we decided to put the emphasis on the player to activate them. Each prints a log message indicating how the player can act:

* Laramy Fisk's identity ability (too difficult to integrate the optional ability into the access routines)
* Team Sponsorship (multiple Sponsorships cause confusing prompt overlaps, easier if you click to activate them individually)
* Medium / Nerve Agent (with no interaction, accesses the full number of cards; if you want fewer than max [as is allowed], click Medium when the run is successful.)
* Leela's identity ability (too many ordering concerns, click it when you're ready)
* Gang Sign (multiple Signs were causing issues when automated)
* Comet (too many varied effects from Events)
* Turntable (click it after stealing an agenda)

#### Win conditions and statistics

mtgred recently started saving games to our database to collect statistics on win rates and card usage. We are trying to better enforce win conditions to support this, rather than leaving it up to the players to realize when the game is over. A "Concede" button has been added next to "Leave game", and we request players to use this button when appropriate so we can keep our statistics accurate. We don't have a web interface to view stats yet, but mtgred occasionally shares numbers with us, including this post from Reddit:

* Haas-Bioroid: Engineering the Future, count: 1881, won: 1088, lost: 793, winrate: 0.5784157363104732
* Jinteki: Personal Evolution, count: 1466, won: 821, lost: 645, winrate: 0.5600272851296043
* Near-Earth Hub: Broadcast Center, count: 1031, won: 701, lost: 330, winrate: 0.6799224054316197
* Blue Sun: Powering the Future, count: 936, won: 540, lost: 396, winrate: 0.5769230769230769
* Spark Agency: Worldswide Reach, count: 927, won: 530, lost: 397, winrate: 0.5717367853290184
* Haarpsichord Studios: Entertainment Unleashed, count: 578, won: 395, lost: 183, winrate: 0.6833910034602076
* SYNC: Everything, Everywhere, count: 569, won: 340, lost: 229, winrate: 0.5975395430579965
* Argus Security: Protection Guaranteed, count: 556, won: 292, lost: 264, winrate: 0.5251798561151079
* Jinteki: Replicating Perfection, count: 503, won: 287, lost: 216, winrate: 0.5705765407554672
* Gagarin Deep Space: Expanding the Horizon, count: 485, won: 234, lost: 251, winrate: 0.4824742268041237
* Industrial Genomics: Growing Solutions, count: 476, won: 270, lost: 206, winrate: 0.5672268907563025
* New Angeles Sol: Your News, count: 435, won: 214, lost: 221, winrate: 0.49195402298850577
* Titan Transnational: Investing In Your Future, count: 392, won: 183, lost: 209, winrate: 0.46683673469387754
* Weyland Consortium: Building a Better World, count: 372, won: 165, lost: 207, winrate: 0.4435483870967742
* Cybernetics Division: Humanity Upgraded, count: 354, won: 126, lost: 228, winrate: 0.3559322033898305
* NBN: Making News, count: 262, won: 147, lost: 115, winrate: 0.5610687022900763
* Jinteki Biotech: Life Imagined, count: 249, won: 154, lost: 95, winrate: 0.6184738955823293
* Harmony Medtech: Biomedical Pioneer, count: 222, won: 100, lost: 122, winrate: 0.45045045045045046
* Chronos Protocol: Selective Mind-mapping, count: 188, won: 64, lost: 124, winrate: 0.3404255319148936
* GRNDL: Power Unleashed, count: 176, won: 90, lost: 86, winrate: 0.5113636363636364
* The Foundry: Refining the Process, count: 176, won: 65, lost: 111, winrate: 0.3693181818181818
* Haas-Bioroid: Stronger Together, count: 159, won: 56, lost: 103, winrate: 0.3522012578616352
* NEXT Design: Guarding the Net, count: 138, won: 60, lost: 78, winrate: 0.43478260869565216
* Tennin Institute: The Secrets Within, count: 129, won: 54, lost: 75, winrate: 0.4186046511627907
* Weyland Consortium: Because We Built It, count: 102, won: 28, lost: 74, winrate: 0.27450980392156865
* Cerebral Imaging: Infinite Frontiers, count: 98, won: 29, lost: 69, winrate: 0.29591836734693877
* NBN: The World is Yours*, count: 93, won: 54, lost: 39, winrate: 0.5806451612903226
* Nisei Division: The Next Generation, count: 82, won: 49, lost: 33, winrate: 0.5975609756097561
* Custom Biotics: Engineered for Success, count: 56, won: 19, lost: 37, winrate: 0.3392857142857143
* Haarpsichord Studios, count: 33, won: 15, lost: 18, winrate: 0.45454545454545453

#### Other improvements and automations

1. Ever rez an ice not realizing the runner was using Inside Job? There goes that surprise! domtancredi and mtgred collaborated to add a run's "source" card to the orange run arrow. 
2. Apocalypse finally forced us to make Architect truly untrashable. Thanks dersam!
3. Chronos Protocol might be the most difficult identity implementation in the game, but justinliew conquered it like a champ!
4. JoelCFC25 continues his reign as Jinteki.net's most active bug fixer and implementer of unwanted cards. Seriously, the guy's a machine.

### New card implementations

As of this writing, we are at __96.6%__ card automation through _Data and Destiny_!

### Coming soon...

1. __REPLAYS__: step through every action during a past game.

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred (Worlds 2014 runner-up!) and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.