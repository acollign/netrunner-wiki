Here's _@lostgeek_ again with a new progress report about the changes of the last two months. This update includes another big change to the game: Subroutine tracking is now implemented! This means that the game engine now keeps track of which subroutines have and have not been broken.

# Big Changes
* **New Feature** ([#4326](https://github.com/mtgred/netrunner/pull/4326)): Stacking of similar servers **(@lostgeek)**
TODO: screenshots
* **New Feature** ([#4265](https://github.com/mtgred/netrunner/pull/4265), [#4348](https://github.com/mtgred/netrunner/pull/4348), [#4369](https://github.com/mtgred/netrunner/pull/4369)): Implement Subroutine Tracking **(@NoahTheDuke)**
TODO: screenshots

# Notable Changes
* **New feature** ([#4309](https://github.com/mtgred/netrunner/pull/4309)): Better damage prevention messages **(@lostgeek)**
TODO: screenshots
* **Bugfix** ([#4298](https://github.com/mtgred/netrunner/pull/4298), [#4323](https://github.com/mtgred/netrunner/pull/4323)): Chat log scrolling should now work on all zoom levels and consume a lot less CPU power **(@NoahTheDuke)**

# Small Changes
* **Bugfix** ([#4372](https://github.com/mtgred/netrunner/pull/4372)): Make Khusyuk mandatory **(@NoahTheDuke)**
* **Bugfix** ([#4370](https://github.com/mtgred/netrunner/pull/4370)): Fixing hostage bug (#4364) **(@mason-bially)**
* **Refactoring** ([#4368](https://github.com/mtgred/netrunner/pull/4368)): Refactored Constellation Protocol **(@lostgeek)**
* **Bugfix** ([#4363](https://github.com/mtgred/netrunner/pull/4363)): Fixed Cold Site Server/Reduced Service/Ruhr Valley bug **(@NoahTheDuke)**
* **Bugfix** ([#4359](https://github.com/mtgred/netrunner/pull/4359)): Added corp/runner-deck/hand/discard-change events, fixed Respirocytes **(@NoahTheDuke)**
* **Bugfix** ([#4358](https://github.com/mtgred/netrunner/pull/4358)): Made card names in button prompts hoverable to get further information **(@NoahTheDuke)**
* **Bugfix** ([#4357](https://github.com/mtgred/netrunner/pull/4357)): Added fake prompt to Hayley to hide information about similar cards in grip **(@NoahTheDuke)**
* **Bugfix** ([#4356](https://github.com/mtgred/netrunner/pull/4356)): Hide the card name when moving from grip to stack **(@NoahTheDuke)**
* **Bugfix** ([#4355](https://github.com/mtgred/netrunner/pull/4355)): Fixed Warroid Tracker interaction with other triggers **(@NoahTheDuke)**
* **Refactoring** ([#4354](https://github.com/mtgred/netrunner/pull/4354)): Refactor Mr Li and Muertos Gang Member **(@NoahTheDuke)**
* **Bugfix** ([#4353](https://github.com/mtgred/netrunner/pull/4353)): Fixed Apocalypse-Calvin B4L3Y-Jinja City Grid interaction **(@NoahTheDuke)**
* **Bugfix** ([#4351](https://github.com/mtgred/netrunner/pull/4351)): Fixed Maw interaction with cards like News Team **(@NoahTheDuke)**
* **Bugfix** ([#4350](https://github.com/mtgred/netrunner/pull/4350)): Fixed hanging prompt in Wyldside **(@NoahTheDuke)**
* **Bugfix** ([#4349](https://github.com/mtgred/netrunner/pull/4349)): Fixed Off-Campus Apartment/The Class Act interaction **(@NoahTheDuke)**
* **Bugfix** ([#4338](https://github.com/mtgred/netrunner/pull/4338)): Fixed Hernando Cortez/Building Blocks interaction **(@NoahTheDuke)**
* **Bugfix** ([#4336](https://github.com/mtgred/netrunner/pull/4336)): Simple hack/fix for Bankroll with Jak Sinclair **(@NoahTheDuke)**
* **Bugfix** ([#4337](https://github.com/mtgred/netrunner/pull/4337)): Fixed Neutralize All Threats to reveal card **(@NoahTheDuke)**
* **Refactoring** ([#4339](https://github.com/mtgred/netrunner/pull/4339)): Refactored Warroid Tracker **(@NoahTheDuke)**
* **Bugfix** ([#4340](https://github.com/mtgred/netrunner/pull/4340)): Fixed Guru Davinder vs Obokata **(@NoahTheDuke)**
* **Bugfix** ([#4341](https://github.com/mtgred/netrunner/pull/4341)): Fixed Hostile Infrastructure/Storgotic interaction **(@NoahTheDuke)**
* **Bugfix** ([#4342](https://github.com/mtgred/netrunner/pull/4342)): Fixed Harishchandra reveal on manual changing of number of tags **(@NoahTheDuke)**
* **Bugfix** ([#4343](https://github.com/mtgred/netrunner/pull/4343)): Fixed ordering of Amani Senai with other effects like Team Sponsorship **(@NoahTheDuke)**
* **Bugfix** ([#4344](https://github.com/mtgred/netrunner/pull/4344)): Fixed ordering of Security Testing with other effects like Paragon **(@NoahTheDuke)**
* **Bugfix** ([#4345](https://github.com/mtgred/netrunner/pull/4345)): Made Gnat and Earthrise interactive so you can order their effects **(@NoahTheDuke)**
* **Bugfix** ([#4335](https://github.com/mtgred/netrunner/pull/4335)): CtM no longer produces a hanging prompt on autoresolve, Executive Boot Camp now works with pay-credits prompt **(@lostgeek)**
* **Refactoring** ([#4330](https://github.com/mtgred/netrunner/pull/4330)): Major refactor of costs, fix cost strings to only print "Pay" when necessary **(@NoahTheDuke)**
* **Bugfix** ([#4327](https://github.com/mtgred/netrunner/pull/4327)): Patchwork can not be used on the card to be installed/played anymore **(@lostgeek)**
* **Bugfix** ([#4325](https://github.com/mtgred/netrunner/pull/4325)): Liberated Account caused game crash **(@lostgeek)**
* **New feature** ([#4319](https://github.com/mtgred/netrunner/pull/4319)): Fencer Fueno now integrated in pay-credits prompt **(@lostgeek)**
* **Bugfix** ([#4328](https://github.com/mtgred/netrunner/pull/4328), [#4305](https://github.com/mtgred/netrunner/pull/4305)): Ghost Runner and Miss Bones now trash when empty **(@lostgeek, @NoahTheDuke)**
* **New feature** ([#4311](https://github.com/mtgred/netrunner/pull/4311)): Brahman is fully implemented **(@lostgeek)**
* **Bugfix** ([#4290](https://github.com/mtgred/netrunner/pull/4290)): Bugfixes around pay-credits prompts **(@lostgeek)**
* **Bugfix** ([#4299](https://github.com/mtgred/netrunner/pull/4299)): Trickster Taka does not trigger outside of runs anymore **(@NoahTheDuke)**
* **Bugfix** ([#4300](https://github.com/mtgred/netrunner/pull/4300)): Chisel now works on negative strength **(@NoahTheDuke)**
* **New feature** ([#4301](https://github.com/mtgred/netrunner/pull/4301)): Change Paragon messages to send properly on decline **(@NoahTheDuke)**
* **Bugfix** ([#4304](https://github.com/mtgred/netrunner/pull/4304)): Titan now puts agenda token on Broad Daylight **(@lostgeek)**
* **Bugfix** ([#4315](https://github.com/mtgred/netrunner/pull/4315)): Itinerant Protesters was crashing the game **(@NoahTheDuke)**

# Backend changes
* **Bugfix** ([#4360](https://github.com/mtgred/netrunner/pull/4360)): Allow triggering nil events **(@NoahTheDuke)**
* **Bugfix** ([#4285](https://github.com/mtgred/netrunner/pull/4285)): Remove has? **(@NoahTheDuke)**
* **Bugfix** ([#4287](https://github.com/mtgred/netrunner/pull/4287)): Namespace prompts and toasts **(@NoahTheDuke)**
* **Bugfix** ([#4286](https://github.com/mtgred/netrunner/pull/4286)): Namespace card defs **(@NoahTheDuke)**
* **Bugfix** ([#4283](https://github.com/mtgred/netrunner/pull/4283)): Namespace card properties, refactor engine to use card property utilities **(@NoahTheDuke)**
* **Bugfix** ([#4317](https://github.com/mtgred/netrunner/pull/4317)): Bugfix for small but totally engine-breaking bug **(@lostgeek)**
* **Bugfix** ([#4288](https://github.com/mtgred/netrunner/pull/4288)): Create Records for state, corp and runner, and various related sub-maps **(@NoahTheDuke)**
* **Bugfix** ([#4324](https://github.com/mtgred/netrunner/pull/4324)): Finish removing :end-turn ability effects **(@NoahTheDuke)**
* **Bugfix** ([#4349](https://github.com/mtgred/netrunner/pull/4349)): Change end-turn to always get latest version of a card **(@NoahTheDuke)**
* **Bugfix** ([#4359](https://github.com/mtgred/netrunner/pull/4359)): Add corp/runner-deck/hand/discard-change events **(@NoahTheDuke)**
* **Bugfix** ([#4360](https://github.com/mtgred/netrunner/pull/4360)): Allow triggering nil events **(@NoahTheDuke)**
* **Bugfix** ([#4373](https://github.com/mtgred/netrunner/pull/4373)): Clean up move-zone **(@NoahTheDuke)**
