# Dates in ClojureScript

## New instance of Date

How you do this in Javascript:

```javascript
const now = new Date();
const beginningOfTime = new Date(0);
```

How you do this in ClojureScript:

```clojure
(def now (new js/Date))
(def beginning-of-time (new js/Date 0))
```

## Methods of prototype of Date

How you get year in Javascript:

```javascript
(new Date()).getYear();
```

How you do this in ClojureScript:

```clojure
(getYear (new js/Date))
```

## Static methods of Date

How you do this in Javascript:

```javascript
Date.now();
```

How you do this in ClojureScript:

```clojure
(js/Date.now)
```
