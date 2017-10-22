The entire logic for both user and deck stats:

- On game start the games-commenced counter is incremented by 1
- On game being won data is added to game state to indicate who and which deck won
- If a player leaves a game without conceding, the other player is flagged as "remaining"
- A "remaining" player may leave the game without negative consequences
- If the dropped player rejoins, the "remaining" flag is cleared
- When the final person leaves the game (player or spectator), the stats are updated again
- If there is a winner, a completion and a win is logged to that user and deck
- If there is a loser, a completion and a lose is logged to that user and deck
- If there is no winner (a player left without conceding), a completion is logged to the "remaining" player

Importantly, the first player to leave without conceding gets an incomplete game, not the "remaining" player

For Deck Stats a player can clear everything - Games / Completions / Wins

For User Stats a player can clear Wins & Losses. Never Games Commenced vs Games Completed.

The User Stats Games Commenced vs. Complete is exposed in the player lobby to encourage game-completion.