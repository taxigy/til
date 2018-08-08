# Circular structures in Javascript

Javascript lives in both OOP and FP paradigms, which is amazing, and here's a tip on how to leverage the former with potentially infinite data structures. In this case, an tree-like object. This example is inspired by [a Q&A on Code Review Stack Exchange](https://codereview.stackexchange.com/questions/200495/traversing-the-parent-child-relationship-between-objects/200942#200942), where it actually appears originally.

```javascript
class Element {
  constructor(object) {
    Object.entries(object).map(([property, value]) => {
      if (typeof value === 'object') {
        this[property] = new Element(value);
        this[property].__parent__ = this;
      } else {
        this[property] = value;
      }
    });

    this.__current__ = this;
  }

  current() {
    return this.__current__;
  }

  parent() {
    return this.__parent__;
  }

  get(property) {
    if (!this[property]) {
      throw new Error('No such property:', property);
    }

    return this[property];
  }

  toJSON() {
    const reducer = element => {
      let output = {};

      for (let property in element) {
        if (
          property.slice(0, 2) === property.slice(-2) &&
          property.slice(-2) === '__'
        ) {
          continue;
        } else if (typeof element[property] !== 'object') {
          output[property] = element[property];
        } else {
          output[property] = reducer(element[property]);
        }
      }

      return output;
    };

    return reducer(this);
  }
}
```

This structure resembles the way a DOM [Element](https://developer.mozilla.org/en-US/docs/Web/API/Element) may be navigated: you can always go down to a certain property and pass reference to that property (which, sometimes, is itself an instance of Element) to a function or another context, or you can go up the hierarchy, to the parent element, then parent of the parent, and so on.

So now it has properties:

```javascript
> let e = new Element({ a: 1, b: { c: 2, d: { e: 3, f: { g: 4, h: { i: 5, j: {} } } } } });
undefined
> e
Element {
  a: 1,
  b:
   Element {
     c: 2,
     d:
      Element {
        e: 3,
        f: [Element],
        __current__: [Circular],
        __parent__: [Circular] },
     __current__: [Circular],
     __parent__: [Circular] },
  __current__: [Circular] }
> e.current() === e
true
> e.get('b').parent() === e
true
> e.get('b').get('d').parent() === e.get('b')
true
> e.get('b').current() === e.get('b')
true
> e.b === e.get('b')
true
> e.b.d === e.get('b').get('d').get('f').get('h').parent().parent()
true
```

and may have properties referring to other properties, including their direct or indirect parents. For example:

```javascript
> e.e = e
Element {
  a: 1,
  b:
   Element {
     c: 2,
     d:
      Element {
        e: 3,
        f: [Element],
        __current__: [Circular],
        __parent__: [Circular] },
     __current__: [Circular],
     __parent__: [Circular] },
  __current__: [Circular],
  e: [Circular] }
> e.e.e === e.e
true
> e.e.e.e.e.e.e.e === e.e.e.e
true
```
