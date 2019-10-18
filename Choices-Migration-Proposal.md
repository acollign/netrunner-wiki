We have a bunch of different ways to make decisions in our engine, which we lump together into `:choices`. This is unwieldy and ugly. Instead, we should pull each of these out into a discrete method of targeting or selection or prompts, and then create sane defaults for each to reduce clutter and noise in the ability definitions. (Note, a lot of this comes from working with the ringteki codebase, so if you work with that, some of this will make a lot of sense.)

The different kinds of choices in Netrunner are:

1) Selecting a card in the play area: ["Choose a piece of ice"](https://netrunnerdb.com/en/card/02058)
2) Selecting a card in a player's hand or discard: ["You may choose 2 cards in your heap"](https://netrunnerdb.com/en/card/13001)
3) Searching for a card in a player's deck: ["Search R&D for an asset"](https://netrunnerdb.com/en/card/06088)
4) Choosing between multiple options: ["he or she must either take 1 tag or end the run."](https://netrunnerdb.com/en/card/21057)
5) Sequentially choosing between multiple options ("modal"): ["Resolve two of the following in any order"](https://netrunnerdb.com/en/card/11008)
6) Spending a variable number of credits: ["You and the Runner secretly spend 0 [credits], 1 [credits], or 2 [credits]."](https://netrunnerdb.com/en/card/05013)
7) Choosing a number: ["choose a number greater than 0"](https://netrunnerdb.com/en/card/26021)
8) Choosing a card title: ["Name a card."](https://netrunnerdb.com/en/card/06026)
9) Paying credits to trigger an optional ability: ["If you pay 4 [credits] when..."](https://netrunnerdb.com/en/card/25091)
10) Selecting a card that has been revealed or looked at (but hasn't changed locations): ["look at the top X cards of your stack. Add 1 of those cards to the bottom of your stack."](https://netrunnerdb.com/en/card/26018)
11) Rearranging cards: ["you may look at the top 5 cards of R&D and arrange them in any order"](https://netrunnerdb.com/en/card/20039)

The big question becomes, how do we want to make these easier to write and maintain? In my mind, each of these should be a distinct prompt, as they have different needs. Some of these could even been split into multiple sub-types, allowing for useful defaults and lowering confusion.