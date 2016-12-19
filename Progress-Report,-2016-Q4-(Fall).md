Welcome to the Jinteki.net open-source progress report for Q4 2016. In this quarterly report, we will highlight some of the recent changes to the Jinteki.net Android: Netrunner platform.

#### About Jinteki.net

[Jinteki.net](http://www.jinteki.net) is a service for playing Android: Netrunner in a browser. It requires no installation to play, is compatible with most operating systems and modern browsers, and includes an interactive deck-builder, chat room, and automation of most game rules and cards.

### New Contributors
Thanks to the new developers who added to the platform this quarter: benhosp, DeepSpawn, dylancwood, erbridge, kevinpowe, kleer1, kyzen, mattchainsaw, poi2000, and shazzner!

Interested in contributing? Check out our new [Getting Started With Development](https://github.com/mtgred/netrunner/wiki/Getting-Started-with-Development) guide, pieced together by queueseven, JoelCFC25, and mtgred! nealterrell also recorded some "[live coding](https://www.livecoding.tv/video/jintekinet-intro-tenma-line-12/)" streams to introduce our engine while implementing new cards.

More interested in simply learning to use the site? We also have a great [Jinteki.net guide](https://github.com/mtgred/netrunner/wiki/Jinteki.net-Guide) put together by darlingsensei. Help keep it current by adding new items or removing outdated information.

### Major System Changes

#### Sounds

You've been asking for over a year, and we can finally deliver. As of December 11, sounds have come to Jinteki.net! krystman and his "sound guy" were very kind to donate the first iteration of Jinteki sounds to plug into Zaroth's sound framework from our April Fools event.  We are collecting feedback [here](https://github.com/mtgred/netrunner/issues/968), so please leave your thoughts after playing a few games with the new effects. If the sounds aren't to your liking, you can turn them off in your My Account settings (see below).

Sounds are currently tied to these in-game events:

* Score agenda
* Steal agenda
* Click-advance
* Click-draw
* Click-credit
* Click-remove tag
* Click-run
* Game ends
* Corp install
* Runner install
* Play Event/Operation
* Rez ice
* Rez non-ice
* Run successful
* Purge viruses

#### My Account / Settings

Alongside sounds, our early December deploy included a new My Account / Settings page for logged-in users to adjust a few _minor_ account settings. nealterrell resurrected domtancredi's older work on a profile page to implement this modest group of settings. For now, My Account will let you:

* Change your avatar via a link to Gravatar
* Enable or disable sounds (saved as a local browser setting via JavaScript localStorage) and adjust their volume
* Choose a background image for the gameboard from a few presets
* Choose to show or hide your opponents' alt-art card images (new users, or streamers catering to new users, should turn off alt-art)

We also implemented an alt-art editor for users who have donated to the site. In the past, donors would see all their cards replaced by alt-art images (when they existed), even if they preferred the original Cypsis art. A new editor in My Account lets these kind supporters of our site choose which of their cards show as original artwork and which show as alternate arts. We've even included the option to choose images from the World Champion 2015 full-bleed artwork cards!

![Alt-art editor](https://cloud.githubusercontent.com/assets/10083341/21082515/c888c5aa-bf91-11e6-981c-ade400649784.png)

_Alternate and World Champion 2015 artwork for Haas-Bioroid: Engineering the Future_

#### Agenda access rework

One of the most problematic cards on the site has historically been The Future Perfect. With difficult interactions with the on-access trigger, Film Critic, and Imp, this is one agenda that just never worked right in all cases. When it was first released, we added a special key "`steal-req`", which allowed agendas to specify the only conditions under which they could be stolen "the normal way" (for TFP: if installed). TFP could then disable stealing when it wasn't installed, instead using the on-access trigger to show a psi game that, if equal, directly "stole" the agenda. Unfortunately this simple design was incompatible with Imp, as without a "You accessed The Future Pefect" window (part of "the normal way") there was no UI to use Imp before OR AFTER the psi game.

Enter Film Critic, which to this day remains one of our hackiest card implementations. Film Critic split agenda access into two windows: "You are about to access The Future Perfect", during which Film Critic can be used, and then either TFP's psi game or a normal agenda's "You accessed ____" window with a "Steal" button. Imp could now be used during the "about to access" phase to trash TFP before the psi game fired, but there remained no way of using Imp _after_ a psi game failure.

In a champion pull request that fixed 5 longstanding agenda-access issues, including TFP + Imp, Saintis reworked the agenda access code for greater consistency and rules compliance. A runner who accesses an agenda now follows this flow of logic:

If an agenda has an on-access effect (TFP, Fetal AI, TGTBT, etc.), we show this window prior to access. Film Critic can be used here to avoid this effect:

<img width="192" alt="screen shot 2016-09-22 at 19 37 24" src="https://cloud.githubusercontent.com/assets/13198563/18759329/5410c7c8-80fc-11e6-964f-b92391d0a3ec.png">

After the agenda has been "accessed" (or if there was no access effect), the "steal" prompt is shown. This is where Imp can be used to trash the agenda instead of stealing it, or Film Critic used to avoid stealing:

<img width="192" alt="screen shot 2016-09-22 at 19 38 03" src="https://cloud.githubusercontent.com/assets/13198563/18759380/8a8b4e54-80fc-11e6-8220-d7835cb37d5f.png">

If the runner loses the TFP psi game, we show this screen. Imp can finally be used here:

<img width="190" alt="screen shot 2016-09-22 at 19 37 44" src="https://cloud.githubusercontent.com/assets/13198563/18759436/c862bb90-80fc-11e6-9e80-198984c4a5ce.png">

#### Workflow changes

A lot of work this quarter went into features that you'll never notice on our production server. Zaroth, erbridge, and domtancredi collaborated heavily to configure a developer's test server, where new features and ideas can be tried in a production-like environment prior to merging into our master codebase. (You'd be shocked how many submissions work fine on local developer machines but fail when deployed to live.) We use a [Puppet](https://github.com/zaroth/betajin-pupfiles) configuration maintained by Zaroth which downloads and installs all the packages needed to run a Jinteki instance; this configuration is deployed to a machine commissioned by domtancredi at [our dev server URL](http://jinteki.zaroth.net). Contributors can submit code to our "dev" branch on GitHub, and once submitted a project collaborator can deploy the submitted code to the dev server with a simple shell script.

The dev server itself runs a separate database from the live site, but otherwise is perfectly fine to play on. We only recommend using it if the main site is down for some reason, as like all "in development" projects, the code running on dev is more likely to be unstable or incomplete. 

### Other Improvements and Automations

* Adam Directives are now chosen before game start (Saintis)
* Enhanced Logic Protocol is finally automated! (mattchainsaw)
* Edward Kim will automatically trash any trashable operations (benhosp)
* Omni-drive will correctly refund memory of hosted cards (poi2000)
* Frantic Coding and Accelerated Beta Test will not trash cards at the top of your draw pile if installing a card from their effect causes a shuffle of your deck, i.e., Frantic Paige and ABT Foundry now work correctly (nealterrell)
* Old Hollywood Grid now works on accesses outside of runs (Quest Completed, Gang Sign) (kevkcc)
* Account Siphon credits cannot be used to avoid its tags using New Angeles City Hall (nealterrell)
* Ark Lockdown will not remove cards from the heap if Blacklist is rezzed (JoelCFC25)

#### Implementation warnings

Saintis added warnings to the "toast" area (upper right corner) when hovering over a card whose implementation is not fully defined in the game engine. These implementation warnings should help clarify when a particular rule is not enforced by the site, and will hopefully reduce Joel's daily "please review the card status document" message count by a few.

<img width="378" alt="screen shot 2016-09-10 at 13 07 42" src="https://cloud.githubusercontent.com/assets/13198563/18410081/49ad2d24-7758-11e6-9942-115c3e727573.png">

_Implementation warning for Jeeves Model Bioroids_

#### Medium + Nerve Agent
A Neal and Joel tag-team effort to beautify the Medium and Nerve Agent code, which was a nasty pile of spaghetti-like workarounds. These cards now prompt the user with a default selection of the maximum number of additional accesses (did you know you can choose less than the maximum amount to access?) and perform their effects during a new "pre-access" event in the game engine. Users can now benefit from Nerve Agent counters when non-run access from Gang Sign or Raymond Flint occurs.

#### UI improvements

erbridge made a big behind-the-scenes revamp to the gameboard layout using HTML flexboxes. Besides making the code cleaner and easier to maintain, he also fixed several longstanding UI complaints regarding multiple upgrades in central servers and cramped hosting on Worlds Plaza and Full Immersion RecStudio.

#### Notable new card implementations
* **erbridge:** Omar Keung
* **JoelCFC25:** Raman Rai _(yes, really)_, Credit Crash, Fumiko Yamamori
* **nealterrell:** Top Hat, Baba Yaga
* **mattchainsaw:** Enhanced Login Protocol
* **kevkcc:** Frantic Coding
* **shazzner:** Reaver

As of time of this writing, we are at [__98.4%__ card automation](https://docs.google.com/spreadsheets/d/1ICv19cNjSaW9C-DoEEGH3iFt09PBTob4CAutGex0gnE/pubhtml) card automation through _Martial Law_!

### Contribute

Jinteki.net is an open-source project run and paid for by mtgred and other volunteers. You can contribute by reporting bugs, submitting fixes, and even adding new implementations on our GitHub page.