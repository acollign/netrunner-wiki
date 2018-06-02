# Reference Reading
[The 3 Ways of Components](https://github.com/reagent-project/reagent/blob/master/docs/CreatingReagentComponents.md)

[Purely Functional Guide to Reagent](https://purelyfunctional.tv/guide/reagent/)

[Guide on event handling](https://purelyfunctional.tv/guide/reagent/#events)

[DOM Manipulation](https://presumably.de/reagent-mysteries-part-3-manipulating-the-dom.html)

# Jinteki Implementation Nodes

### Component State
In most cases component local state has been bound to a Reagent atom called `s`.  This is often passed to child components which need to update some state in the parent.  In the old code this was mostly done using core.async

Example of this initial state setup in the outer Reagent function
```clojure
(defn msg-input-view [channel]
  (let [s (r/atom {})]
    (fn [channel]
      [:form.msg-box {:on-submit #(do (.preventDefault %)
                                      (send-msg s channel))}
```

### What is a React ref?
The legacy OM code base used the old version of React refs which were strings.  During migration it was seen that consumers of the ref such as jQuery did not always "find" the DOM node.  A new approach was used which adds the DOM node to a a clojure atom and it can be called from there.  

Example of a ref binding now - which binds the DOM node into the chat-state atom
```clojure
(defn message-view [message s]
          (when (not my-msg)
            [:div.panel.blue-shade.block-menu
             {:ref #(swap! chat-state assoc :msg-buttons %)}

```
This binding can then be reffed from elsewhere in the app - for example:
```clojure
(defn- hide-block-menu []
  (-> (:msg-buttons @chat-state) js/$ .hide))
```

Typically refs have been bound to a component local state atom, though in some cases if the parent is many layers higher, or the code hard to read - the binding is to a name-space global atom.

### What the hell does this Hiccup mean? :<>    React Fragments
[Fragments in React](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html)
[Hiccup Support for Fragments](https://github.com/reagent-project/reagent/commit/87b61475e20fb31735ffc03e77eaf78f692879d9)

Long and short: 
``` clojure 
[:<>  stuff1 stuff2 ]
```

This has been used as a wrapper to help out in places where it was not easy to give a :key to list items.  Ract loves those. 

### Don't do this! Updating a r/atom will cause a re-render
In the Reagent app, app-state is a Reagent atom.  When a reagent atom is updated is causes any components using it in a render method to render.  Some cases where you want to be careful...

In our gameboard namespace we have this code which causes audio to play after a render:
```clojure
(defn gameboard []
      {:component-did-update
       (fn []
         (update-audio {:sfx (:sfx @game-state) :sfx-current-id (:sfx-current-id @game-state)
                        :gameid (:gameid @game-state)} soundbank))
```

This calls the update audio code - can you see a problem in the old snippet here?
```clojure
(defn update-audio [{:keys [gameid sfx sfx-current-id]} soundbank]
    (swap! app-state assoc :sfx-last-played {:gameid gameid :id sfx-current-id}))))
```

So `update-audio` makes a `swap!` on the `app-state` atom ... this causes a re-render on the parent component which causes the component-did-update to fire... and then calls right back into update-audio.  We have a re-rendering loop!  Simple fix... don't use a r/atom to record this stuff.
```clojure
(defonce sfx-state (atom {}))

(defn update-audio [{:keys [gameid sfx sfx-current-id]} soundbank]
    (swap! sfx-state assoc :sfx-last-played {:gameid gameid :id sfx-current-id}))))
```

### Simple Rule - every component must return a div or similar.. not a list
This might have cost me an hour ... there is something wrong in this component:
```clojure
(defn build-hand-card-view
  [player remotes wrapper-class]
  (let [side (get-in @player [:identity :side])
        size (count (:hand @player))]
      (map-indexed
        (fn [i card]
          [:div {:class (str
                          (if (and (not= "select" (get-in @player [:prompt 0 :prompt-type]))
                                   (= (:user @player) (:user @app-state))
                                   (not (:selected card)) (playable? card))
                            "playable" "")
                          " "
                          wrapper-class)
                 :style {:left (* (/ 320 (dec size)) i)}}
           (if (or (= (:user @player) (:user @app-state))
                   (:openhand @player)
                   (spectator-view-hidden?))
             [card-view (assoc card :remotes remotes)]
             [facedown-card side])])
        (:hand @player))))
```

What could it be?  It returns a list of divs.  This gives this error in React which baffled me:
```
Uncaught Error: Objects are not valid as a React child (found: object with keys {ns, name, fqn, _hash, cljs$lang$protocol_mask$partition0$, cljs$lang$protocol_mask$partition1$}). If you meant to render a collection of children, use an array instead.
```

The fix?  Wrap it in a [:div]!
```clojure
(defn build-hand-card-view
  [player remotes wrapper-class]
  (let [side (get-in @player [:identity :side])
        size (count (:hand @player))]

[:div
      (map-indexed
        (fn [i card]
          [:div {:class (str
                          (if (and (not= "select" (get-in @player [:prompt 0 :prompt-type]))
                                   (= (:user @player) (:user @app-state))
                                   (not (:selected card)) (playable? card))
                            "playable" "")
                          " "
                          wrapper-class)
                 :style {:left (* (/ 320 (dec size)) i)}}
           (if (or (= (:user @player) (:user @app-state))
                   (:openhand @player)
                   (spectator-view-hidden?))
             [card-view (assoc card :remotes remotes)]
             [facedown-card side])])
        (:hand @player))))
]
```
