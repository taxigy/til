# How to apply a sequence of functions?

Let's say, you have a composition of functions:

```clojure (inc (inc (inc (inc 1)))) ```

The number of functions is random, and the only thing that you
know is that every function takes precisely one argument, which
makes them _composable_. Now, let's say this sequence of
functions comes in a vector:

```clojure (def fns [inc inc inc inc]) ```

To apply composition of these functions to a certain argument,
do

```clojure ((apply comp fns) 100) ```
