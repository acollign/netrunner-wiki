[The 3 Ways of Components](https://github.com/reagent-project/reagent/blob/master/docs/CreatingReagentComponents.md)

[Purely Functional Guide to Reagent](https://purelyfunctional.tv/guide/reagent/)

[Guide on event handling](https://purelyfunctional.tv/guide/reagent/#events)

[DOM Manipulation](https://presumably.de/reagent-mysteries-part-3-manipulating-the-dom.html)

### Arm Wrestling with :key in react lists
[Fragments in React](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html)

[Hiccup Support for Fragments](https://github.com/reagent-project/reagent/commit/87b61475e20fb31735ffc03e77eaf78f692879d9)

Long and short: 
``` clojure 
[:<>  stuff1 stuff2 ]
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
