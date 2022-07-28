([BHAG](https://en.wikipedia.org/wiki/Big_Hairy_Audacious_Goal) means a Big, Hairy, Audacious Goal: a vision statement or idea, something that medium-to-long term, and that isn't easily accomplished)

Consider this a scratch-pad of ideas I have for jnet. Nothing is permanent, everything is open to revision and redirection, I probably won't have time in my life to do any of these. 

---
2018-11-21

* Finish writing tests for all the cards
  * ~Agendas~
  * ~Assets~
  * ~Events~
  * Ice
  * Identities
  * ~Operations~
  * Programs
  * Resources
  * Upgrades

* Unit test core engine logic
* ~Separate engine into different namespaces~
* ~Move all card defs to their own files~ - Decided against as it's messy and doesn't provide enough utility
* ~Move all card tests to their own files~ - Decided against as it's messy and doesn't provide enough utility
* Integrate with NRDB so that people can log in with their NRDB credentials and have their decks automatically show up
  * ~Import decks from NRDB~

* Serialize game state in such a way that deploying is seamless (games don't disappear when we change servers if we're saving them to the db or disk every action or turn or whatever)

* ~Create admin portal~
  * See full user list, online users, and current games
  * Mute or Ban users, delete user messages
  * Fetch new cards
  * ~Update Announcement Board~
  * ~Send toasts to all active users~

* Refactor front-end files to be readable
  * ~Do what I did to cardbrowser to all of the other cljs files (especially gameboard.cljs (!))~
  * Move isolated logic to new files for clarity and ease

* Move off of Mongo to SQLite or another Relational Database
  * Store all of the card data in the database, so we can run queries with joins and stuff in it, instead of having to pre-process it in the netrunner-data repo

* Convert the engine from a permissive stance to an enforcing stance (cf Jigoku/Ringteki)
  * Instead of allowing players to do p much anything they want whenever they want, limit what's clickable or selectable or moveable based on which part of the game we're in
  * Build a Step/Pipeline system for processing the overall game-flow, restricting a given action to whichever step the game state is in at that time
  * Build a common language of actions such that we don't have to specify every single card in fine detail but can offload that work to the base action
  * Include a "automated/manual" toggle for changing between the two stances, to ease/fix game state mess-ups

---
2018-11-27

* ~Restructure data reloading architecture to save and read a single card file instead of many~

* Create a "Front Page"/Splash screen that's not the chat lobby
  * Classic menu: "Play" "Card Browser" "Deck Builder" "Chat" "Settings" etc
  * Show changelog/updates as an overlay

* edn: Move all card text out of card files and into "oracle" text files
  * Alternatively, put all of the card data back into the set-card files, revert set-card data to printed version (non-errata'd), combine/overwrite when generating "current" version

* Overlay card images in the hover with the oracle text, graphically
  * Allow showing/hiding this in the options
  * Do the same for values? Cost/strength/subtypes/etc

* ~Change subtypes to keyword vectors instead of strings~

---
2019-01-06

* Add a "Learning" tab to the lobby screen.
* Add a tutorial that leads a player step-by-step through a sample game as the runner or the corp, showing which buttons to press, how to fix mistakes when playing, what parts of the engine work or don't, etc.
* Actually implement the deep changes to the core rules that the Nisei Comprehensive Rules brought along (checkpoints, etc).
  * ~Checkpoints~ (half done)
  * Timing windows
  * Additional costs rules

* ~Save a copy of every single game along with the messages sent by the client to the server using a system such as [this one](http://spootnik.org/entries/2016/12/17/building-an-atomic-database-with-clojure).~
  * ~Give users a way to view all of their old games.~ Done with messages ~only~ for all previous games, full replays for last 15 games.

---
2019-03-25

* Abstract additional costs so players are always prompted for them, removing the need to hardcode prompts.
* ~Make run-related functions awaitable.~
* ~Rewrite registering effects to handle cards moving between zones.~
* ~Change the way Events are trashed so they stay on the table until runs and related effects are finished.~
* Change req checking to differentiate between play/trigger requirements and resolution requirements.

---
2021-01-25

* Automate deploys

---
2021-08-31

* Ability to Register as TO (?¿)
* Ability to Create new Lobby and Name it
* Ability to create X numbers of tables
* Ability to send a single message to all tables in the lobby at the same time
* Ability to link players registered in cobra to jinteki
* Ability to send results to cobra automatically from jinteki
* Ability to lock the tournament decks