Game requests from the client to server - and the corresponding response are completely de-coupled and asynchronous.  This is handled via a client locking mechanism.  

**How does this work?**

* At game start, we initialise an atom called `lock` on the client set to false meaning the client can take actions
* The send-command function will only send a command to the server if lock = false, and then it will set lock to true
* The send-command function will not send a command if lock=true as we need to wait for the original command outcome to get sent back
* The server sets a unique Action ID (`:aid`) for each game-command received for each game side (corp and runner) which is attached to the game-state
* The result of the command with a new `:aid` (and any other game-state change) is sent back to the client using a `diff` mechanism. 
* The client will clear the `lock` state if it sees a change in the Action ID (`:aid`) for its side (corp or runner) - this is how it knows it got a response.

**Client Implementation**

This is implemented in the gameboard.cljs file as follows:
```clojure
(defonce lock (atom false))

(defn send-command
  ([command] (send-command command nil))
  ([command {:keys [no-lock] :as args}]
   (when (or (not @lock) no-lock)
     (try (js/ga "send" "event" "game" command) (catch js/Error e))
     (when-not no-lock (reset! lock true))
     (ws/ws-send! [:netrunner/action {:command command :args args}]))))

(defn check-lock?
  "Check if we can clear client lock based on action-id"
  []
  (let [aid [(:side @game-state) :aid]]
    (when (not= (get-in @game-state aid)
                (get-in @last-state aid))
      (reset! lock false))))

(defn handle-diff [{:keys [gameid diff]}]
  (when (= gameid (:gameid @game-state))
    (swap! game-state #(differ/patch @last-state diff))
    (check-lock?)
    (reset! last-state @game-state)))
```

**Server Implementation**

```clojure
(defn set-action-id
  "Creates a unique action id for each server response - used in client lock"
  [state side]
  (swap! state update-in [side :aid] (fnil inc 0)))

(defn handle-action
  "Ensures the user is allowed to do command they are trying to do"
  [user command state side args]
  (if (not-spectator? state user)
    (do ((commands command) state side args)
        (set-action-id state side))
    (when-let [cmd (spectator-commands command)]
      (cmd state side args))))
```
