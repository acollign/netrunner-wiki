## Chat
The chat page is where you start on Jinteki.net. General chat is where users discuss cards, site status, and generally connect about playing Netunner. It works like most chat or IRC clients in that you type your message into the box and hit enter to send. There are region specific channels that work well for discussing local events and finding players in your geographic area.

### Chat Tips:
* Ask for help if you are new
* Begin your message with “@username” to indicate that a message is directed at a specific person
* Keep an eye out for posts from mtgred and other contributors about card status updates
* If you encounter a bug, scroll up in chat to see if it's already been discussed, or check [recently merged pull requests](https://github.com/mtgred/netrunner/pulls?q=is%3Apr+is%3Aclosed) or [the history for the src/ directory](https://github.com/mtgred/netrunner/commits/master/src/clj/game) to see if it's already been fixed but hasn't yet been deployed, which is often the case
* You can easily focus the chat box by pressing <kbd>Access key</kbd> + <kbd>L</kbd> (Take a look [here](https://en.wikipedia.org/wiki/Access_key) to find out which key your access key is, usually <kbd>Alt</kbd>, <kbd>Ctrl</kbd>, or <kbd>Alt</kbd> + <kbd>Shift</kbd>).

## Deckbuilder
The deck builder is an excellent way to quickly create decks either from scratch or plain text. Begin by selecting the ID, naming your deck, and either copy/paste a plain text list exported from another deckbuilding site (e.g., NetrunnerDB, MeteorDecks, Acoo) or add cards using the Add cards box. Be sure to press the Save button when you are done making changes!

Be aware that some card names with special characters do not copy and paste well or work with the Add cards dialog that attempts to autocomplete names. Déjà Vu and R&D Interface are the best examples, but there may be others. Add these by typing a quantity and a unique portion of card name directly into the Decklist text box--e.g., typing "3 vu" will add 3 Déjà Vu, and "3 R&D" will add 3 R&D Interface.

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
- `/close-prompt` -> close an active prompt and show the next waiting prompt, or the core click actions
- `/counter n` -> set counters on a card to `n` (player's own cards only)
- `/adv-counter n` -> set advancement counters on a card to `n` (player's own cards only)
- `/card-info` -> display debug info about a card (player's own cards only)
- `/move-bottom` -> move a card from your hand to the bottom of the deck (player's own cards only)
- `/rez` -> Rez a card ignoring all costs (Corp only)
- `/rez-all` -> Rez all installed cards, e.g. at game end to show what you had (Corp only)
- `/undo-click` -> Sets the game state back to your prior click
- `/undo-turn` -> Sets the game state back to before your turn started (requires both players to type)

Although the log will flag the usage of these commands with an orange icon to alert the other player, please use these judiciously and with the consent of your opponent. 

### Server Restarts
The game server periodically gets restarted to deploy code for new features or bug fixes. If you find erratic behavior or features not working as they normally do, make sure to do a forced refresh of the site (<kbd>Ctrl</kbd>-<kbd>F5</kbd> in your browser on PCs, hold <kbd>Shift</kbd> while refreshing on Mac browsers) to retrieve the latest client-side code.

### Gameplay Limitations
While the system is fairly mature, it is still in development and some kinds of effects aren't yet supported. Hovering over a card that has implementation details will display an orange tooltip in the upper right corner next to the zoomed image of the card.

##### Hosting
Cards capable of hosting other cards have both "Click to host from Grip" and "Host an installed card" abilities on them to account for Runner installs done via paid abilities (e.g., Clone Chip, Self-modifying Code, Personal Workshop, Street Peddler). Please communicate with your opponent when using the ability to host something that's already installed so they understand what is happening and why. 

##### Ordering Simultaneous Effects
When multiple conditional abilities are pending at the same time, both players will get the opportunity to select the order their own abilities resolve. This is done by opening a prompt that lists the card title of pending ability. Click on the card title button you wish to resolve, the ability will be fully resolved, and then the original prompt will re-open with that ability removed from the list. Once all abilities of the active player have been resolved, the same process happens with the inactive player and their pending abilities. At the time of writing (November 2020), this has been fully implemented for all relevant card abilities.

##### Runs
In an effort to limit the number of clicks and waiting for each player, the steps and phases of a run have been trimmed and merged. The "jnet" version of a run is listed below, with changes noted below each step:

1) Initiation

When a run is initiated, the existence of ice will be determined and the run will automatically progress to either "Approach Ice" or "Approach Server" as appropriate. The run cannot be paused here so anything you need to manually adjust before approach must be done before starting the run.

2) Approach Ice

After resolving all "Approach Ice" abilities, there is a single window for handling paid abilities, rezzing, and jacking out. Once both players have pressed "continue", the run automatically moves into either the "Encounter Ice" phase or the "Pass Ice" phase.

3) Encounter Ice

After resolving all "Encounter Ice" abilities, there is a window for handling paid abilities. The Corp player must click "fire unbroken subs" to resolve all unbroken subs. Once both players have pressed "continue", the run automatically moves into the "Pass Ice" phase.

4) Pass Ice

After resolving all "Pass Ice" abilities, the run automatically moves into either the "Approach Ice" or "Approach Server" phase, as appropriate.

5) Approach Server

After resolving all "Approach Server" abilities, there is a single window for handling paid abilities, rezzing, and jacking out. Provided the Runner presses "Access server", the Corp can press either "No further actions" or "Action before access". If they press the former, the run moves into the "Access Cards" phase. If they press the latter, they are given a final chance to rez cards and use paid abilities. This is to help enforce moving into the second paid ability window after the runner has declined to jack out. If the Runner wishes to also use paid abilities here, they should click the "Indicate Action" button near the chat window.

### Gameplay Tips:
* Be sure to check available resources, especially credits and MU, before trying to install
* Clicking outside of a pop up menu will usually dismiss it
* All recurring credits, credits hosted on cards (Cold Read, etc), and cards that given at-payment discounts (Patchwork, etc) are implemented by opening a prompt that says "Select a credit-providing card (0 of X credits)". When this happens, click on the card with credits you wish to use, and the credits will be automatically deducted from the card. If you don't want to pay for the whole cost with hosted credits, you can click "Done" early to pay the rest of the cost from your credit pool.

## Etiquette
The Netrunner community has always been an outstanding place to game and Jinteki.net is no exception. Here are a few suggestions to make the online play experience more fun for everyone involved.

* Greet your opponent before starting the game (glhf is traditional)
* Say a farewell before leaving the game (GG is always appreciated)
* Leave a note in general chat for your opponent if you disconnected or had to leave
* Indicate when you are taking a long pause to think so your opponent doesn’t think you disconnected
* Check with your opponent before firing subroutines that alter the game state (trash & damage ones especially)
* Inform your opponent of your reasoning before adjusting resources manually
* ***Please*** **avoid complaining about opponents in general chat**
* Remember that the site is a free, fan-made endeavor and act accordingly