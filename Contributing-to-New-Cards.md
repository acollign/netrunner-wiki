
#### Basic Steps to Implementing a New Card
1. If this is your first time, ask around what might be best for you to try implementing either on the pack issue (below) or in the slack channel.
1. Put your name next to the card in the master card implementation issue https://github.com/mtgred/netrunner/issues/3253
1. Make a feature branch from your master branch
1. Find similarly working card(s) for code comparison
1. Implement changes in */src/clj/game/cards by adding a new card entry and editing it.
1. Test, iterate
1. Push fork to github (`origin`)
1. Make a pull request (PR) to the mtgred/netrunner `master`
1. Put the PR# in the issue in place of your name
1. Wait for @nealterrell to explain how to fix all the `delayed-completion` stuff
1. Fix those bugs, push to origin again (which will get picked up by the PR)
1. Once the PR gets approved and merged into `master`, click the checkbox in the Issue

#### Implementing Early or when NRDB is down
If NRDB is down or NRDB doesn't have the card you are working on yet, you'll need to lein fetch the cards locally https://github.com/mtgred/netrunner/wiki/Development-Tips-and-Tricks#fetching-cards-while-nrdb-is-down-wip

#### Things to Notice (WIP)
Cards which do not yet have their art hovered over in-game might display one thing on the game board and another from within the chat.

Example:
An installed piece of ice

Hovered over "test spends 6 [credits] to rez Endless EULA."
