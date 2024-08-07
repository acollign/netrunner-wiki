A bit over one year after the last progress report, here's the Jinteki.net open-source progress report for Q2 2019. I'm @lostgeek and have started working on the project since the implementation of Downfall and will try to make these changelogs a more regular thing. I guess that's what the earlier reports also had in mind -- but hey, one can try!

This report will only comprise the recent changes since the last server reboot on May 6th. We'll try to publish these changelogs whenever a new version goes online!

# Big Changes
* **New Feature:** Paying multiple costs is now bundled together
  * Whenever more than one type of cost has to be paid, the game now shows a single prompt for all costs to be paid simultaneously. Here's the case of stealing an SDS Drone Deployment from a Server with Ben Musashi and Strongbox.<br/>
<img src="https://user-images.githubusercontent.com/603677/57576409-62b6ac80-742d-11e9-8709-9ef7e0c01a0f.png" width=200><img src="https://user-images.githubusercontent.com/603677/57576410-64807000-742d-11e9-8c76-46f605a84bba.png" width=200>

* **New Feature:** Paying costs from credit providing cards (recurring credits, Patchwork) is now implemented fully!
  * Whenever paying a cost, the game now looks at available credit sources and asks whether you want to use them. So for example, whenever you're pumping a breaker with a Cloak and Net Mercur out, the game will now display this:
<img src="https://user-images.githubusercontent.com/1409906/60204308-d4687f80-984e-11e9-8927-4d3eb7c825d2.PNG" width=300><img src="https://user-images.githubusercontent.com/1409906/60204314-d8949d00-984e-11e9-88a2-0999d6a6061d.PNG" width=200>

  * The "old ways" of handling recurring credits still work, this is just a more convenient alternative
  * This works on the following actions: Installing cards, using stealth credits, using paid abilities on programs, credits during the run, trashing cards, playing Events, removing tags, rezzing cards, advancing cards, traces
    * For a full list of cards, [look here](https://github.com/mtgred/netrunner/pull/4262#issuecomment-497955715)!


# Notable Changes
* **Bugfix:** Chat box should now auto-scroll correctly
* **Bugfix:** Deck legality is now displayed correctly together with a reason for illegality
* **Refactoring:** Accessing cards, forced trashing and access abilities were completely reworked. Accessing a Mumbad Virtual Tour should now be possible without any hiccups!
* **New Feature:** Several commands were added for further manual changing of the game state: `/install-ice` `/peek n` `/summon n` `/swap-ice` `/swap-installed` (see help for further information)

# Small Changes
* **Bugfix:** Rejig now applies correct discount
* **Bugfix:** Pelangi now doesn't remove already applied subtypes from the list
* **Bugfix:** ETR and replacement effects are now registering correctly (Giordano ending a run after it is successful doesn't make it unsuccessful...)
* **Bugfix:** Fully Operational now triggers PAD Tap
* **Cleanup:** Director Haas Pet Project is now printing agenda-scored message correctly
* **Bugfix:** CtM triggered on Runner cards
* **Bugfix:** Architect Deployment Test was only working on ICE
* **Bugfix:** Fixed access step for Khusyuk, Top Hat or Information Sifting interacting with Ash or Eater
* **Bugfix:** Fixed Showing Off + Bacterial Programming (turning R&D upside down to be able to use existing routines was a fine idea until BP was printed)
* **Bugfix:** Mining Accident prompt does not get stuck anymore
* **Bugfix:** Möbius is not triggering all the time anymore
* **Bugfix:** Acacia + LLDS Energy Regulator now triggers correctly. Long live the jank!