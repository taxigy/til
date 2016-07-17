# Using react-sortable-component in redux-form

Among React components that implement drag-n-drop and sortable features, the [react-sortable-component](https://www.npmjs.com/package/react-sortable-component) happens to be the easiest to use. With redux-form, though, it's tricky.

First of all, let's pretend that a sortable list is _one of the redux-form-wrapped component's fields_. Simply said, it is a field. Since field, in terms of redux-form, is anything that can get its value changed by the user, the assumption seems pretty close to reality.

So, for example, there's a component that implements form:

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';

@reduxForm({
  form: 'my-epic-form',
  fields: ['images'],
  initialValues: {
    'images': []
  }
})
export default class MyEpicComponent extends Component {
  render() {
    const {
      fields: {
        images
      }
    } = this.props;

    return (
      <div className="my-epic-component">
        <form onSubmit={::this.props.handleSubmit((values) => console.log('dummy', values))}>
        </form>
      </div>
    );
  }
}
```

Now, let's incorporate react-sortable-component into it:

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';
import {
  SortableItems,
  SortableItem
} from 'react-sortable-component';
import _ from 'lodash';

@reduxForm({
  form: 'my-epic-form',
  fields: ['images'],
  initialValues: {
    'images': []
  }
})
export default class MyEpicComponent extends Component {
  render() {
    const {
      fields: {
        images
      }
    } = this.props;

    return (
      <div className="my-epic-component">
        <form onSubmit={::this.props.handleSubmit((values) => console.log('dummy', values))}>
          <SortableItems
            items={images}>
            {_.map(images, (image, index) => (
              <SortableItem
                key={index}>
                {image}
              </SortableItem>
            ))}
          </SortableItems>
        </form>
      </div>
    );
  }
}
```

We immediately receive an error because React is not comfortable with rendering objects in

```javascript
{someObject}
```

notation. But `images` has to be an array of images, doesn't it?

Nope. The value of `images` is an object that is provided by redux-form so you could use it in a way like

```javascript
<input {...images} />
```

It contains different props, like `value`, `defaultValue`, and some functions among them, like `onChange` and `onBlur`. It's technically the same as injecting props one by one via

```javascript
<input
  value={images.value}
  defaultValue={images.defaultValue}
  onChange={images.onChange}
  onBlur={images.onBlur} />
```

but in just a single argument spread.

So, we need to map through `images.value` to reflect its value into sortable items. Like this:

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';
import {
  SortableItems,
  SortableItem
} from 'react-sortable-component';
import _ from 'lodash';

@reduxForm({
  form: 'my-epic-form',
  fields: ['images'],
  initialValues: {
    'images': []
  }
})
export default class MyEpicComponent extends Component {
  render() {
    const {
      fields: {
        images
      }
    } = this.props;

    return (
      <div className="my-epic-component">
        <form onSubmit={::this.props.handleSubmit((values) => console.log('dummy', values))}>
          <SortableItems
            items={images.value}>
            {_.map(images.value, (image, index) => (
              <SortableItem
                key={index}>
                {image}
              </SortableItem>
            ))}
          </SortableItems>
        </form>
      </div>
    );
  }
}
```

Since initially `images.value` is an empty array, the call to `_.map` returns zero elements. Why is initial value of `images.value` an empty array? See `initialValues` property of an object passed as the first argument to `reduxForm`.

Now, let's imagine there are some images. For example, current value of `images.value` looks like

```javascript
['https://lorempixel.com/100/100/1', 'https://lorempixel.com/100/100/2', 'https://lorempixel.com/100/100/3']
```

Whenever you drag and drop items within `SortableItems`, the `onSort` event is fired, and so the function you assigned to this event through prop is executed. So we need this props and the function, too:

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';
import {
  SortableItems,
  SortableItem
} from 'react-sortable-component';
import _ from 'lodash';

@reduxForm({
  form: 'my-epic-form',
  fields: ['images'],
  initialValues: {
    'images': []
  }
})
export default class MyEpicComponent extends Component {
  render() {
    const {
      fields: {
        images
      }
    } = this.props;

    return (
      <div className="my-epic-component">
        <form onSubmit={::this.props.handleSubmit((values) => console.log('dummy', values))}>
          <SortableItems
            items={images.value}
            onSort={() => console.log('SORTED!')}>
            {_.map(images.value, (image, index) => (
              <SortableItem
                key={index}>
                {image}
              </SortableItem>
            ))}
          </SortableItems>
        </form>
      </div>
    );
  }
}
```

Whenever the items within `SortableItems` are rearranged, the function `() => console.log('SORTED!')` is called, so you see this message on the console. But the order doesn't change. Why?

The order of `SortableItem` elements inside `SortableItems` element doesn't change because the value of `images.value` doesn't change. React reflects values into DOM, not the other way around. The only way to change DOM is to update values, so the component will re-render. In case of redux-form, the component will re-render after it receives new props, because redux-form dispatches a new action that, from the very top `Provider` component down to every component in the JSX tree, makes component receive new props. Some component will receive exactly the same props, because the part of application state that is resembled by these props hasn't changed.

The `MyEpicComponent` will not get re-rendered. Nothing has changed.

How to fix this?

To do that, you need to make sure that the value of `images.value` changes every time the arrangement of `SortableItem` elements inside `SortableItems` changes. The only way to do that is to use `images.onChange` function. This function receives a new value of the field. Since `SortableItems` behaves as a field, it has to bring a new value to `images.onChange` event.

Lucky enough, `SortableItems` passes a new array into its `onSort` event. So you can assign the `onSort` event with a function that accepts a single argument which is an array and just pass this array towards into redux-form's `images.onChange`. Like this:

```javascript
import React, { Component } from 'react';
import { reduxForm } from 'redux-form';
import {
  SortableItems,
  SortableItem
} from 'react-sortable-component';
import _ from 'lodash';

@reduxForm({
  form: 'my-epic-form',
  fields: ['images'],
  initialValues: {
    'images': []
  }
})
export default class MyEpicComponent extends Component {
  render() {
    const {
      fields: {
        images
      }
    } = this.props;

    return (
      <div className="my-epic-component">
        <form onSubmit={::this.props.handleSubmit((values) => console.log('dummy', values))}>
          <SortableItems
            items={images.value}
            onSort={(values) => images.onChange(values)}>
            {_.map(images.value, (image, index) => (
              <SortableItem
                key={index}>
                {image}
              </SortableItem>
            ))}
          </SortableItems>
        </form>
      </div>
    );
  }
}
```

That's it. The value of `values` in a function passed to `onSort` props is always a new array of elements, already sorted after the user drops an element. No extra adjustment needed.

So, what's happening in the latest setup:

1. A new `MyEpicComponent` component is created and wrapped into `reduxForm`, therefore new props are injected into this component, `fields` and `handleSubmit` among them.
1. The `MyEpicComponent` component now has `this.props.images` prop which is an object-like representation of whatever a user-editable field needs: `value`, `onChange`, etc.
1. Inside form, `SortableItems` appears. It contains as many items as there are in `images.value`. Initially, zero items.
1. We're pretending that somehow `images.value` receives a bunch of new items, each a string representing an URL.
1. Since there are many elements in the `images.value` array, a user can drag and drop them. So the `SortableItems` is listening to `onSort` event and executes an arrow-function every time the event fires.
1. The `onSort` event fires every time a user drops a previously dragged element.
1. Every time `onSort` happens, the new values are picked and passed into `images.onChange` function.
1. Once `images.onChange` function is called, redux-form dispatches a new action that causes `MyEpicComponent` re-render.
1. Once the component has finished rendering, the order of items in `SortableItems` is updated.

It all happens so fast it looks like only elements get rearranged. And it feels like the change is driven by DOM. Like, a user dragged an element, a user dropped an element, and so now React has to pick up the new DOM, parse a new order of items from it, and somehow update value of the field. But nope. Back in days, it was usual, since jQuery worked this way, and it felt natural. These days, with React, data rules, and DOM only reflects it.
