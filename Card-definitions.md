A card definition (`card-def`) is an ability definition (see [[Abilities]]) that may contain extra keys.

The normal ability keys (`:req`, `:effect` etc.) are normally used when the card is activated.

Some of the extra keys are explained below.

**COMMON KEYS**
- `:abilities` -- contains a vector of ability definitions (as described in [[Abilities]]) that can be triggered from the card
- `:data` -- contains data to be merged into the card-map on activation. Normally has any counters the card should start with
- `:events` -- contains a map of [[event hooks]] to ability definitions that are to be triggered by the specified hook
- `:in-play` -- contains a vector of properties to gain at install / rez and lose on uninstall / derez, e.g. `[:memory 1]`
- `:leave-play` -- extra logic to be triggered when the card leave play
- `:prevent` -- map of event type to cause type this card can prevent e.g. `{:damage [:meat]}`
- `:recurring` -- integer specifying number of recurring credits the card should have

**IDENTITY SPECIFIC KEYS**
- `:mulligan` -- 5-function to resolve after a mulligan is taken

**CORP SPECIFIC KEYS**
- `:access` -- ability triggered when the card is accessed
- `:advanceable` -- specifies restrictions on advancing the card. Allowed keywords:
  * `:always` -- can be advanced rezzed and unrezzed
  * `:while-rezzed` -- can only be advanced while rezzed
  * `:while-unrezzed` -- can only be advanced while unrezzed
- `:derezzed-events` -- same as `:events` except that these events trigger even if the card is unrezzed
- `:derez-effect` -- ability to trigger when the card is derezzed
- `:init` -- specifies where the card will be installed, e.g. `{:root "HQ"}`
- `:strength-bonus` -- 5-function that returns the integer to increase the strength of the ICE by
- `:trash-effect` -- ability to trigger when the card is trashed. Has an extra key `:while-unrezzed` that specifies if the ability should trigger even if the card is not rezzed
- `:rez-req` -- 5-function that specifies the requirements to be able to rez the card.

**AGENDA SPECIFIC KEYS**
- `:advancement-cost-bonus` -- 5-function that returns an integer to adjust the advancement cost of the agenda with
- `:agendapoints-runner` -- 5-function that specifies how many agenda points this agenda is worth to the runner
- `:install-state` -- specifies how the agenda is installed, used `:face-up` to specify **public** agendas
- `:steal-cost-bonus` -- 5-function that returns a cost vector that needs to be paid to steal the agenda
- `:steal-req` -- 5-function that returns `true` if the agenda can be stolen
- `:stolen` -- ability to trigger when the agenda is stolen
- `:swapped` -- ability to triggered when the agenda is swapped into the Corp's scored area

**RUNNER SPECIFIC KEYS**
- `:install-cost-bonus` -- 5-function that returns the additional costs that has to be paid to install this card
- `:strength-bonus` -- 5-function that returns the integer to increase the strength of the icebreaker by

**OTHER KEYS**
- `:additional-cost` -- vector of additional costs to activate this card
- `:can-host`
- `:flags`
- `:hosting`
- `:move-zone` -- 5-function to resolve when the card moves zone. Resolved even if not active
- `:suppress`
