# `namespacefy`

[![Clojars Project](https://img.shields.io/clojars/v/namespacefy.svg)](https://clojars.org/namespacefy)  
[![CircleCI](https://circleci.com/gh/Jarzka/namespacefy.svg?style=shield)](https://circleci.com/gh/Jarzka/namespacefy)

[API documentation](https://jarzka.github.io/namespacefy/docs/)

namespacefy is a simple Clojure(Script) library which aims to make it easy to keep map keys namespaced, no matter where your data comes from.

# Introduction

When data is fetched from a database or received from an external system, the output is often a map with unnamespaced keywords. This is often the desired end result. However, to avoid naming conflicts, it is often recommended to use namespaced keywords in Clojure. This library aims to solve this problem by providing simple helper functions to convert keys in maps to namespaced keywords, no matter where your data comes from. When the namespacing is not needed anymore (for example if you want to send the data to your JSON-loving neighbour), unnamespacing the map can be done easily before JSON conversion.

# Installation

Add the following line to your Leiningen project:

```clj
[namespacefy "0.4"]
```

# Usage

```clj
(:require [namespacefy.core :as namespacefy])
```

You can namespacefy keywords, maps or collection types with a single *namespacefy* function:

```clojure
(def data {:name "Seppo"
           :id 1
           :tasks {:id 666 :time 5}
           :points 7
           :foobar nil})

(namespacefy data {:ns :product.domain.player
                   :except #{:foobar}
                   :custom {:points :product.domain.point/points}
                   :inner {:tasks {:ns :product.domain.task}}})

;; We get the following output:
;; {:product.domain.player/name "Seppo"
;;  :product.domain.player/id 1
;;  :product.domain.player/tasks {:product.domain.task/id 666
;;                                :product.domain.task/time 5}
;;  :product.domain.point/points 7
;;  :foobar nil}"
```

To unnamespacefy the same data, use the *unnamespacefy* function. It also supports keywords, maps and collections:

```clojure
(unnamespacefy data {:recur? true})

;; We get the following output:
;; {:name "Seppo"
;;  :id 1
;;  :tasks {:id 666 :time 5}
;;  :points 7
;;  :foobar nil}
```

There are also other helper functions available:

```clojure
;; Get the specific key from a map, regardless if it is namespaced or not:
(get-un {:product.domain.player/name "Seppo"} :name) ; => "Seppo"
(get-un {:product.domain.task/name "The Task"} :name) ; => "The Task"
(get-un {:name "The Task"} :name) ; => "The Task"

;; Assoc the data to the given keyword which matches the corresponding namespaced or unnamespaced keyword.
(assoc-un {:product.domain.player/name "Seppo"} :name "Ismo")
;; => {:product.domain.player/name "Ismo"}
(assoc-un {:product.domain.task/name "The Task"} :name "The Task 123")
;; => {:product.domain.task/name "The Task 123"}
(assoc-un {:name "The Task"} :name "The Task 123")
;; => {:name "The Task 123"}
```

For more information on the available options, please read the [API documentation](https://jarzka.github.io/namespacefy/docs/).

# Changelog

Here: https://github.com/Jarzka/namespacefy/releases
