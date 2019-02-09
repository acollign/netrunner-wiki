This is a list of event hooks that can be found in the code, taken from uses in `cards`.

### Actions
Events related to click actions taken on Corp or Runner's turn.

#### Corp
    :corp-click-credit
    :corp-click-draw
    :pre-corp-draw
    :corp-draw
    :post-corp-draw
    :advance
    :pre-corp-install
    :corp-install
    :server-created
    :pre-purge
    :purge
    :pre-play-instant
    :play-operation

#### Runner
    :runner-click-draw
    :pre-runner-draw
    :runner-draw
    :post-runner-draw
    :runner-spent-click
    :pre-install
    :runner-install
    :pre-play-instant
    :play-event

### Runs
Events related to runs

    :run
    :approach-ice
    :encounter-ice
    :no-action
    :continue
    :jack-out
    :pass-ice
    ::approach-server
    :pre-access-card
    :access
    :successful-run
    :successful-run-ends
    :unsuccessful-run
    :run-ends

### Turns
Events related to the start and end of player turns

    :pre-first-turn
    :corp-phase-12 -- when corp enters Step 1.2 between Start Turn and Mandatory Draw
    :corp-turn-begins -- when corp ends Step 1.2
    :corp-turn-ends
    :runner-phase-12 -- when runner enters Step 1.2 between Start Turn and Take Clicks
    :runner-turn-begins -- when runner ends Step 1.2 
    :runner-turn-ends

### Tracing and tagging
    :pre-init-trace
    :trace
    :successful-trace
    :unsuccessful-trace
    :pre-tag
    :pre-resolve-tag
    :runner-gain-tag
    :runner-lose-tag
    :avoided-tag

### Bad publiticy
    :pre-bad-publicity
    :pre-resolve-bad-publicity
    :corp-gain-bad-publicity

### Agendas, stealing and scoring
    :pre-advancement-cost
    :agenda-counter-spent
    :pre-steal-cost
    :agenda-stolen
    :agenda-scored
    :corp-forfeit-agenda
    :runner-forfeit-agenda

### ICE strength and subtypes
    :pre-ice-strength
    :ice-strength-changed
    :ice-subtype-changed

### Icebreaker strength
    :pre-breaker-strength
    :breaker-strength-changed

### Winning and Losing
    :corp-loss
    :runner-loss

### Rezzing
    :pre-rez
    :pre-rez-cost
    :rez

### Damage
    :pre-damage
    :prevented-damage
    :pre-resolve-damage
    :damage

### Trashing
    :pre-trash
    :corp-trash
    :runner-trash

### Purging
    :pre-purge
    :purge

### Expose
    :pre-expose
    :expose

### Others
    :counter-added
    :card-moved
    :psi-game
    :as-agenda