**Statement**
The current set of tools to handle wait-completed or delay-completed effect is not abstracted enough.  It requires too deep an understanding by a developer - particularly when creating card logic.  Less of a problem in the core code in my view.

**Tools in the kit bag**
`make-eid`  Every ability resolution in the game calls this or makes use of the `eid` to enable the delayed completion engine we have today.  Functions like resolve-ability will call this if no `eid` is passed in.  Seems pretty reasonable.

`when-completed` A macro we can use to call a function and wait for it to complete by looking at the `eid`, before calling another function.  This macro waits for the first `eid` to complete then calls the rest of the code.

`continue-ability` A macro used when we want to continue a chain of functions or effects using the same `eid`.  Can be used following `when-completed` or via a standard `resolve-ability` call with no async operations.

`:delayed-completion`  A key we can set to (usually) true in an ability map to avoid an effect being marked as complete at the end of the resolve-ability function call. 

**Why is this hard?**
* It requires every developer to know when a called effect might be async
* Or that the called effect itself has a listener which might be async
* For example when we call `tag` from a card effect we don't know or there will be another card listening to that effect which might allow a player to enhance or prevent the tag
* But we don't want to block the game - i.e. make it all sync.

**Ideas/Ramble**
* When we call `tag` or `damage` in an effect the calling function does not know if this might be async, so we have to mark it as such via :delayed-completion
* When we call a prompt such as :optional, :psi (psi-game), :choices, or :trace we know this is async so we can do this
* So a great start might be to wrap other things we know are (maybe) async like `tag` and `damage`.  These effects `know` they might be async - not the caller so why should the calling effect have to know this?
* A difference so consider is that there might be no async or user interaction.  But cards which can cause these to be async must have pre-registered that effect in the state... so the function can handle it.