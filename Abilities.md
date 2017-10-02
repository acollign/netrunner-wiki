The `core-abilities` file contains arguably the most important function of the engine: `resolve-ability`. This function is used to trigger the abilities that drive card implementation and most engine effects.

General flow is:

1. Check the `:req` function for any requirements for triggering the ability
2. Show any prompts, psi games, trace etc. as needed
3. Charges any costs associated with the ability
4. Invokes the `:effect` function to actually trigger the effects of the function on the state

List of all keys that can be used in an ability map are shown below.
Note that "5-function" is used to refer to the 5-argument functions used throughout the code. More details about these functions can be found on the [Macros](https://github.com/mtgred/netrunner/wiki/Macros-(effect-vs.-req)) wiki page.

**COMMON KEYS**
- `:req` -- a 5-function that must return true or false. If false, the ability will not be resolved.
- `:cost` -- a vector of costs to charge, for example `[:credit 1 :click 1]`. If the costs cannot be paid, the ability will not be resolved.
- `:msg` -- either a string or a 5-function that returns a string. Prints to the log when the ability is finished. Note that "`player` uses `card title` to " will be prefixed and a "." will be suffixed to the message.
- `:effect` -- a 5-function that will be called if the ability will be resolved (if `:req` is true or not present, costs can be paid, and any prompts are resolved.)
- `:player` -- manually specifies which player the ability should affect, rather than leave it implicit.

**PROMPT KEYS**
- `:choices` -- this key signals a prompt of some kind. Can be:
    * a 5-function returning a vector of cards or strings -- user chooses one option. Called a 'choice' prompt.
    * the keyword `:credit` -- user chooses an integer up to their current credit amount.
    * a map of `:counter` to a counter type (e.g. `:credit`, `:power`,...) -- user chooses an integer up to the number of the specified counter on the given card.
    * a map of `:number` to a 5-function returning an integer -- user chooses an integer up to the value supplied by the 5-function.
    * a map of `:req` to a 1-argument function returning `true` or `false`. Triggers a 'select' prompt with targeting cursor; only cards that cause the 1-argument function to return `true` will be allowed.
- `:prompt` -- a string or 5-function returning a string to display in the prompt menu.
- `:priority` -- a numeric value, or `true` (equivalent to 1). Prompts are inserted into the prompt queue and sorted based on priority, with higher priorities coming first. The sort is stable, so if two prompts have the same priority, the prompt that was inserted first will remain first after the sort. You should rarely need to use a priority larger than 1.
- `:not-distinct` -- `true` if the prompt should not collapse `:choices` entries of the same string to one button. Defaults to `false`.
- `:cancel-effect` -- if the prompt uses a Cancel button, this 5-function will be called if the user chooses Cancel.

**OTHER PROMPTS**
- `:psi` -- a map that starts a psi game. Has keys `:equal` and `:not-equal`, whose values are 5-functions which are triggered when the game resolves.
- `:trace` -- a map that starts a trace. Has a `:base` key that is either an integer or a 5-function returning an integer for the base strength. The map is otherwise a normal ability map that can contain `:req`, `:effect`, or any other key in an ability; the `:effect` is only triggered if the trace succeeds. Can also have a key `:kicker` that is an ability map with key `:min`, whose effect triggers if the trace strength matches or exceeds the `:min` value. (Constellation ice.)

**SIMULTANEOUS EFFECT RESOLUTION KEYS**
- `:interactive` -- when simultaneous effect resolution has been enabled for a specific event, the user receives a menu of cards that handle the effect and get to choose the order of their resolution. This menu is only shown if at least one ability handling the event has an `:interactive` function that returns `true`. If none are interactive, then all handlers will be resolved automatically, one at a time in an arbitrary order. In general, handlers should be marked `:interactive (req true)` if they have important order-of-effect interactions with other cards. The `:interactive` function can be coded to have smarter logic if necessary -- see Replicator, which is only interactive if there is another copy of the installed card remaining in the Stack.
- `:silent` -- any handler that does not require user interaction under any circumstances can be marked `:silent`. If a handler's `:silent` function returns `true`, then no menu entry will be shown for the handler. In that case, the ability will only be resolved once all non-silent abilities are resolved. Example: AstroScript has no important interactions with other 'agenda scored' effects, and doesn't care when an agenda token is placed. Example: Hayley Kaplan will not show a prompt if there are no valid targets in the grip.

**OTHER KEYS**
- `:counter-cost` / `:advance-counter-cost` -- number of counters to remove to resolve the ability
- `:once` -- its only value is `:per-turn`; signifies an effect that can only be triggered once per turn.
- `:once-key` -- by default, each `:once` is distinct per card. If multiple copies of a card can only resolve
               some ability once between all of them, then the card should specify a manual `:once-key` that can
               be any value, preferably a unique keyword.
- `:optional` -- shows a 'Yes/No' prompt to let the user decide whether to resolve the ability.
- `:end-turn` -- if the ability is resolved, then this ability map will be resolved at the end of the turn.
- `:show-discard` -- if `true` the discard panel will open when this ability resolves. Normally used with a prompt targeting the discard.
- `:delayed-completion` -- if `true` it signals that the ability will manually trigger `effect-completed`. If `false` it signals that `effect-completed` can be triggered directly even if using `:psi`, `:trace`, `:optional` or `:prompt`. See [Pull Request #1582](https://github.com/mtgred/netrunner/pull/1582) for details.