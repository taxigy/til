# "Component will receive props" vs show/hide in render

You probably know pretty much about [component lifecycle
methods](https://facebook.github.io/react/docs/react-component.html#the-component-lifecycle).
Among them, there's `componentWillReceiveProps`. That's a good
one, especially when you [work with DOM API
directly](scrolling-elements.md).

Let's say, you want something to appear after a while, and the
component that shows the element you want to show depends on a
prop:

```javascript
export default function MyComponent(props) {
  return (
    <div>
      {props.showingSomething && (
        <div className="i-am-something">
          This is how you see something
        </div>
      )}
    </div>
  );
}
```

This component is tiny and easy to interpret: it has a root
element that is always there, and the root element has 0 or 1
child, depending on whether `showingSomething` prop is truthy.

Now let's say you want to instantiate a Leaflet map (see
[Gist](https://gist.github.com/taxigy/786d46ef627b77ad48d00c8a911d2263)) and show it when all the resources have been received:

```javascript
export default class MyMap extends React.Component {
  instantiateLeafletMap() {
    const map = L.map('mapElement', {
      // Leaflet options when the map is getting instantiated
    });
    const tiles = L.tileLayer(TILE_LAYER_API);

    tiles.addTo(map);
  }

  componentDidMount() {
    this.instantiateLeafletMap();
  }

  render() {
    return (
      <div>
        {this.props.resourcesLoadedSuccessfully && (
          <div id="mapElement" />
        )}
      </div>
    );
  }
}
```

Looks good: the map will only appear when
`resourcesLoadedSuccessfully` is truthy, which may happen after
some time and is by design deferred. But is it really good?

When you work with DOM API directly (and Leaflet one of many
cases), it's better to defer instantiation of DOM-related stuff
to the latest possible point in time, especially when it depends
on the resources that will have been received at some point in
future.

To make it work, let's depend on a prop and insantiate Leaflet as
soon as `resourcesLoadedSuccessfully` becomes truthy:

```javascript
export default class MyMap extends React.Component {
  instantiateLeafletMap() {
    const map = L.map('mapElement', {
      // Leaflet options when the map is getting instantiated
    });
    const tiles = L.tileLayer(TILE_LAYER_API);

    tiles.addTo(map);
  }

  componentWillReceiveProps(props) {
    if (props.resourcesLoadedSuccessfully) {
      this.instantiateLeafletMap();
    }
  }

  render() {
    return (
      <div>
        <div id="mapElement" />
      </div>
    );
  }
}
```

See that there's no more conditional rendering in `render`: the
element gets rendered as soon as component gets mounted, while
the Leaflet part will only get instantiated as soon (or late) as
`resourcesLoadedSuccessfully` becomes truthy.

There's a weak point: Leaflet will be reinstantiated every time
`resourcesLoadedSuccessfully` prop is received, among others, and
is truthy. Component update may happen in other cases, which may
not be the right moment to re-init DOM-related stuff. To prevent
it, store the status in component's own state:

```javascript
export default class MyMap extends React.Component {
  state = {
    mapInstantiated: false
  }

  instantiateLeafletMap() {
    const map = L.map('mapElement', {
      // Leaflet options when the map is getting instantiated
    });
    const tiles = L.tileLayer(TILE_LAYER_API);

    tiles.addTo(map);
  }

  componentWillReceiveProps(props) {
    if (!this.state.mapInstantiated && props.resourcesLoadedSuccessfully) {
      this.instantiateLeafletMap();
      this.setState({
        mapInstantiated: true
      });
    }
  }

  render() {
    return (
      <div>
        <div id="mapElement" />
      </div>
    );
  }
}
```

Done. Now the Leaflet map will only get instantiated once, as
soon as all the resources have been loaded successfully. Of
course, you'll need to implement the latter part on your own, but
you get the idea.

The _why_ of this approach is simple: sometimes, when a library
works with DOM API, it's very expensive to re-instantiate it at
every prop or state change that occurs in a React component that
wraps it. While React is super efficient, with its virtual DOM,
it's not the same with jQuery, Leaflet, HTML5 Canvas and other
DOM-first libs. Another problem is that, instantiated once with
some data or resources lacking, they visual or interactive
outcome may not be expected, sometimes not even usable. So you
always need to make sure that all the resources are ready and all
the dependencies are already present. Simply declare a prop, wait
for it to flip the value, and do the DOM stuff afterwards.
