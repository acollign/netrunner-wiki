This is a list of event hooks that can be found in the code, taken from uses in `cards`.

### Actions
Events related to click actions taken on corp or runner's turn.

#### Corp
    :corp-click-credit
    :corp-click-draw
    :corp-draw
    :advance
    :pre-corp-install
    :corp-install
    :server-created
    :pre-purge
    :purge
    :play-operation

#### Runner
    :runner-click-draw
    :runner-draw
    :runner-spent-click
    :pre-install
    :runner-install
    :play-event

### Runs
Events related to runs

    :run
    :pre-ice-strength
    :pre-breaker-strength
    :no-action
    :jack-out
    :pass-ice
    :pre-access-card
    :access
    :successful-run
    :successful-run-ends
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
    :pre-tag

### Agendas, stealing and scoring

    :pre-advancement-cost
    :agenda-counter-spent
    :pre-steal-cost
    :agenda-stolen
    :agenda-scored

### Others
    :pre-rez
    :pre-rez-cost
    :rez
    :pre-damage
    :damage
    :pre-trash
    :runner-trash
    :expose
    :counter-added
    :card-moved
    :corp-loss
    :runner-loss
    :psi-game