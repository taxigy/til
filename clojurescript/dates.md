# Dates in ClojureScript

## New instance of Date

How you do this in Javascript:

```javascript
const now = new Date();
```

How you do this in ClojureScript:

```clojure
(def now (new js/Date))
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
