Game requests from the client to server - and the corresponding response are completely de-coupled and asynchronous.  This is mostly fine - however it is possible during network latency for a client to send the same command multiple times before the first one has resolve.

We handle this to some extent today via a client lock mechanism.  This is implemented in the gameboard.cljs file as follows:
```clojure
(defonce lock (atom false))

(defn send-command
  ([command] (send-command command nil))
  ([command {:keys [no-lock] :as args}]
   (when (or (not @lock) no-lock)
     (try (js/ga "send" "event" "game" command) (catch js/Error e))
     (when-not no-lock (reset! lock true))
     (ws/ws-send! [:netrunner/action {:command command :args args}]))))

(defn handle-state [state]
  (swap! game-state #(assoc state :side (:side @game-state)))
  (reset! last-state @game-state)
  (reset! lock false))

(defn handle-diff [diff]
  (swap! game-state #(differ/patch @last-state diff))
  (swap! last-state #(identity @game-state))
  (reset! lock false))
```
How does this work?
* We initialise an atom set to false meaning the client can take actions
* The send-command function will send a command to the server if lock = false, and the set lock to true
* The send-command function will not set a command if lock=true
* The lock is cleared on reception of new state from the server.  However this state change does not necessarily correspond to a command send from THIS client.  Any game state change will clear the lock.

Some logs showing how this looks server side:

**Clicking on Cache**
"handle-game-action:" {:command "ability", :args {:card {:faction "Criminal", :limited 3, :rotated false, :cycle_code "lunar", :runner-abilities [], :zone ["rig" "program"], :cid 11, :new true, :added-virus-counter true, :art nil, :type "Program", :abilities [{:label "Gain 1 [Credits]"}], :image_url "https://www.cardgamedb.com/forums/uploads/an/med_ADN17_37.png", :title "Cache", :counter {:virus 3}, :uniqueness false, :host nil, :packquantity 3, :normalizedtitle "cache", :code "06037", :side "Runner", :cost 1, :hosted nil, :subroutines [], :installed true, :memoryunits 1, :implementation "full", :set_code "tsb", :subtype "Virus", :previous-zone ["hand"]}, :ability 0}}

** Handle Action passes the request to the resolve action function **
"handle action:" "ability" ":" :runner ":" {:card {:faction "Criminal", :limited 3, :rotated false, :cycle_code "lunar", :runner-abilities [], :zone ["rig" "program"], :cid 11, :new true, :added-virus-counter true, :art nil, :type "Program", :abilities [{:label "Gain 1 [Credits]"}], :image_url "https://www.cardgamedb.com/forums/uploads/an/med_ADN17_37.png", :title "Cache", :counter {:virus 3}, :uniqueness false, :host nil, :packquantity 3, :normalizedtitle "cache", :code "06037", :side "Runner", :cost 1, :hosted nil, :subroutines [], :installed true, :memoryunits 1, :implementation "full", :set_code "tsb", :subtype "Virus", :previous-zone ["hand"]}, :ability 0}

** Diffs are sent back to the clients.
"public-diffs-corp" [{:runner {:rig {:program [0 {:counter {:virus 2}}]}, :credit 5}, :eid 61, :log [:+ {:user "__system__", :text "wozzit uses Cache to gain 1 [Credits]."}]} {}]
"public-diffs-runner" [{:runner {:rig {:program [0 {:counter {:virus 2}}]}, :credit 5}, :eid 61, :log [:+ {:user "__system__", :text "wozzit uses Cache to gain 1 [Credits]."}]} {}]

**Ideas to Improve This**
1. Add an request-id that the client sends to the server.  When the action is resolved, pass this request-id back to the client which will make it unblock.  This would require that id to flow through handle-game-action, handle-action, resolve-ability, and differ related functions.  This would be for every game effect interaction - multiple per ability, vs eid on the server side.

2. Server side locking (in addition to client side).  This would require the server to lock on a game effect request and ignore subsequent requests until it passes a response back.  This would still require some data to pass through handle-game-action, handle-action, resolve-ability, and differ related functions so the server knows when the request is completed.  The client would lock on sending a request too.  In the diff message some data will tell the client when to unlock.

Number 2 seems easier.  No ID to track.