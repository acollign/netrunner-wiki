([BHAG](https://en.wikipedia.org/wiki/Big_Hairy_Audacious_Goal) means a Big, Hairy, Audacious Goal: a vision statement or idea, something that medium-to-long term, and that isn't easily accomplished)

Consider this a scratch-pad of ideas I have for jnet. Nothing is permanent, everything is open to revision and redirection, I probably won't have time in my life to do any of these. 

* Finish writing tests for all the cards
* Unit test core engine logic
* Separate engine into different namespaces
* Move all card defs to their own files
* Move all card tests to their own files
* Serialize game state in such a way that deploying is seamless (games don't disappear when we change servers if we're saving them to the db or disk every action or turn or whatever)

* Refactor front-end files to be readable
  * Do what I did to cardbrowser to all of the other cljs files (especially **gameboard.cljs**)
  * Move isolated logic to new files for clarity and ease

* Move off of Mongo to SQLite or another Relational Database
  * Store all of the card data in the database, so we can run queries with joins and stuff in it, instead of having to pre-process it in the netrunner-data repo

* Convert the engine from a permissive stance to an enforcing stance (cf Jigoku/Rinteki)
  * Instead of allowing players to do p much anything they want whenever they want, limit what's clickable or selectable or moveable based on which part of the game we're in
  * Build a Step/Pipeline system for processing the overall game-flow, restricting a given action to whichever step the game state is in at that time
  * Build a common language of actions such that we don't have to specify every single card in fine detail but can offload that work to the base action
  * Include a "automated/manual" toggle for changing between the two stances, to ease/fix game state mess-ups