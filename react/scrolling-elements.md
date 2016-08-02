# Scrolling an overflown element

React components and DOM elements are different. JSX only looks like HTML although it isn't. Sometimes DOM elements can have attributes that can be set via component's props, just take a look at this input field:

```javascript
const MyInput => ({value}) => (
  <input type="text" value={value} />
);
```

But you can't scroll an element via props because you can't scroll it via attribute. You need to manipulate a lower-level DOM API to do it:

- `Element#scrollLeft` — to set its horizontal scrolling position (see [ref](http://www.w3schools.com/jsref/prop_element_scrollleft.asp)), and
- `Element#scrollTop` — to set its vertical scrolling position.

To know what current, say, horizontal scrolling position is, you can use

- `Element#clientWidth` — to get width of the visible part of the element, and
- `Element#scrollWidth` — to get the actual width of the element, which is, if the element overflows, is greater than its `clientWidth`.

It's handy to represent scrolling position in percentage, within range [0, 1]. To do it, you need to keep in mind two things:

- when the element is scrolled to its leftmost edge, `scrollLeft === 0`, and
- when the element is scrolled to its rightmost edge, `scrollLeft === scrollWidth - clientWidth`.

So, to calculate the position within range [0, 1], simply do

```javascript
const {
  scrollLeft,
  scrollWidth,
  clientWidth
} = element;
const position = scrollLeft / (scrollWidth - clientWidth);
```

Backwards, to scroll an element to certain position, do

```javascript
element.scrollLeft = (scrollWidth - clientWidth) * position;
```

It's pretty old DOM API that works via assigning the `Element#scrollLeft` property. But it works in every browser and so we'll use it.

The tricky part here is that you can't assign DOM element's attribute `scrollLeft` with some value to make it scroll. This is not going to work:

```javascript
const MyOverflownElement = ({scrollLeft}) => (
  <div
    className="my-overflown-element"
    scrollLeft={scrollLeft}>
    ...
  </div>
);
```

Nope. Not working. Because this way, you assign _attribute_ of DOM element, i.e. `Element#attributes.scrollLeft`. Instead, you should change the value of `Element#scrollLeft`. The only way to do that is via [refs](https://facebook.github.io/react/docs/more-about-refs.html). The easiest way to make it work is to make the component stateful:

```javascript
import React, { Component } from 'react';

class MyOverflownElement extends Component {
  constructor() {
    super();
    this.state = {};
    this.refs = {};
  }

  render() {
    return (
      <div
        className="my-overflown-element"
        ref={node => {
          this.refs.node = node
        }}>
        ...
      </div>
    );
  }
}
```

Let's pretend every time the component receives a prop `position` is has to scroll to that position. The most reliable way to scroll it properly is to wait until the component has ben rendered and then scroll the element. Updating the DOM element via low-level DOM API is safe against React and so it won't cause any issues:

```javascript
import React, { Component } from 'react';

class MyOverflownElement extends Component {
  constructor() {
    super();
    this.state = {};
    this.refs = {};
  }

  componentDidUpdate() {
    const {
      node
    } = this.refs;
    const {
      position
    } = this.props;
    const {
      scrollWidth,
      clientWidth
    } = node;

    node.scrollLeft = (scrollWidth - clientWidth) * position; // <- making the element scroll here
  }

  render() {
    return (
      <div
        className="my-overflown-element"
        ref={node => {
          this.refs.node = node
        }}>
        ...
      </div>
    );
  }
}
```

Now, every time the component is updated via either new props or its own state update, the `componentDidUpdate` is executed, where the side-effect happens and the element scrolls at the point that is represented by value of `position` prop, which, as you remember, stays within [0, 1].

-----

See also [the Codepen](http://codepen.io/rishatmuhametshin/pen/vKzNOr?editors=0120) about scrolling and spying.

TBD:

- [ ] smooth scrolling using `setInterval`.
