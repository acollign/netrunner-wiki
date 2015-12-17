## Chat
The chat page is where you start on Jinteki.net. General chat is where users discuss cards, site status, and generally connect about playing Netunner. It works like most chat or IRC clients in that you type your message into the box and hit enter to send. There are region specific channels that work well for discussing local events and finding players in your geographic area.

### Chat Tips:
* Ask for help if you are new
* Begin your message with “@username” to indicate that a message is directed at a specific person
* Keep an eye out for posts from mtgred and other contributors about card status updates
* If you encounter a bug, scroll up in chat to see if it's already been discussed, or check [recently merged pull requests](https://github.com/mtgred/netrunner/pulls?q=is%3Apr+is%3Aclosed) or [the history for the src/ directory](https://github.com/mtgred/netrunner/commits/master/src/clj/game) to see if it's already been fixed but hasn't yet been deployed, which is often the case
* You can easily focus the chat box by pressing <kbd>Access key</kbd> + <kbd>L</kbd> (Take a look [here](https://en.wikipedia.org/wiki/Access_key) to find out which key your access key is, usually <kbd>Alt</kbd>, <kbd>Ctrl</kbd>, or <kbd>Alt</kbd> + <kbd>Shift</kbd>).

## Deckbuilder
The deck builder is an excellent way to quickly create decks either from scratch or plain text. Begin by selecting the ID, naming your deck, and either copy/pasting the list or adding cards using the Add cards box. Be sure to press the Save button when you are done making changes! Be aware that some card names with special characters do not copy and paste well or work with the Add cards dialog that attempts to autocomplete names. Déjà Vu and R&D Interface are the best examples, but there may be others. Add these by typing a quantity and a unique portion of card name directly into the Decklist text box--e.g., typing "3 vu" will add 3 Déjà Vu, and "3 R&D" will add 3 R&D Interface.

### Deckbuilder Tips:
* Export your deck list from your site of choice as plain text and copy it right into the deck field
* Decks are sorted by most recently changed, so keep your favorites fresh
* Be sure to delete the deck summary added by most sites, as this will confuse the deck builder

### Datapack Availability
Card data is retrieved from [NetrunnerDB](http://www.netrunnerdb.com), so when new datapacks go live there, the deckbuilder here will be able to display them and you can add them to decks. 

## Gameplay
Jinteki.net gameplay is quite intuitive. Generally clicking on a card will do what you want it to do or give you the options you need. If something doesn’t work or you make a mistake, just adjust resources (credits, clicks, etc.) appropriately and fix the game state. Most actions that can’t be accomplished by clicking on a card can be done by dragging the card image to the appropriate area. If you find a situation that doesn’t fit within the game engine, explain it to your opponent in chat and track it that way.
### Console Commands
To speed up the process of fixing the game state when something goes awry, use the following commands by entering them into your game's chat:

- `/draw n` -> Draw `n` cards
- `/credit n` -> Set your credits to `n`
- `/click n` -> Set your clicks to `n`
- `/memory n` -> Set your memory to `n`
- `/tag n` -> Set your tags to `n`
- `/bp n` -> Set your bad publicity to `n`
- `/link n` -> Set your link to `n`
- `/handsize n` -> Set your handsize to `n`
- `/take-meat n` -> Take `n` meat damage (Runner only)
- `/take-net n` -> Take `n` net damage (Runner only) 
- `/take-brain n` -> Take `n` brain damage (Runner only)
- `/discard #n` -> Discard card number `n` from your hand  
- `/deck #n` -> Put card number `n` from your hand on top of your deck
- `/end-run` -> End the run (Corp only)
- `/jack-out` -> Jack out (Runner only)
- `/trace n` -> Start trace with base strength `n` (Corp only)
- `/psi` -> Start a Psi game (Corp only)
- `/counter n` -> set counters on a card to `n` (player's own cards only)
- `/adv-counter n` -> set advancement counters on a card to `n` (player's own cards only)
- `/card-info` -> display debug info about a card (player's own cards only)

Although the log will flag the usage of these commands with an orange icon to alert the other player, please use these judiciously and with the consent of your opponent. 

### Server Restarts
The game server periodically gets restarted to deploy code for new features or bug fixes. If you find erratic behavior or features not working as they normally do, make sure to do a forced refresh of the site (<kbd>Ctrl</kbd>-<kbd>F5</kbd> in your browser) to retrieve the latest client-side code. 
### Gameplay Limitations
While the system is fairly mature, it is still in development and some kinds of effects aren't yet supported. Using cards that rely on them can be difficult, require manual adjustments, and/or some understanding on the part of your opponent. Examples include: 

* **Additional costs to run, access, etc:** Ruhr Valley, Midway Station Grid, Enhanced Login Protocol, Donut Taganes, *et al*
* **Other unique complicated effects:** Tori Hanzō, Satellite Grid, Surveillance Sweep, Genetics Pavilion, Gene Conditioning Shoppe, RSVP, *et al*

##### Hosting
Cards capable of hosting other cards have both "Click to host from Grip" and "Host an installed card" abilities on them to account for Runner installs done via paid abilities (e.g., Clone Chip, Self-modifying Code, Personal Workshop). Please communicate with your opponent when using the ability to host something that's already installed so they are clear about what is happening and why. 

##### Ordering Simultaneous Effects
At this time there is no way for players to choose the correct sequence of multiple effects that occur in the same window unless the cards have been altered to make them manually triggered. This can yield incorrect outcomes. For example, accessing a Snare! with Dedicated Response Team can fail to apply the DRT damage if the Runner trashes the Snare! before the Corp has decided to pay to trigger the Snare!

Some cards have been changed to prompt the user in the game log when they become activated so the user can resolve them in the order desired. Examples include: Leela Patel, Comet, Gang Sign, Team Sponsorship, and Turntable. 
 
### Gameplay Tips:
* Be sure to check available resources, especially credits and MU, before trying to install
* Clicking the side of a pop up menu will usually dismiss it
* Try to take recurring credits before doing the action for which they are used

## Etiquette
The Netrunner community has always been an outstanding place to game and Jinteki.net is no exception. Here are a few suggestions to make the online play experience more fun for everyone involved.

* Greet your opponent before starting the game (glhf is traditional)
* Say a farewell before leaving the game (GG is always appreciated)
* Leave a note in general chat for your opponent if you disconnected or had to leave
* Indicate when you are taking a long pause to think so your opponent doesn’t think you disconnected
* Allow your opponent to jack out instead of firing an end the run, unless it would alter the game state
* Check with your opponent before firing subroutines that alter the game state (trash & damage ones especially)
* Inform your opponent of your reasoning before adjusting resources manually
* ***Please*** **avoid complaining about opponents in general chat**
* Add a description like, “PRIVATE” to your game if it’s not for anyone to join
* Remember that the site is a free, fan-made endeavor and act accordingly