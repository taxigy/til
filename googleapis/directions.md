# Showing directions

You have a map and you have two points: origin and destination. Your user starts their path at the origin point and the goal is to reach destination point. Classic.

You can use Google Directions API (in conjunction with Google Maps API) in your web app to show the direction.

Prerequisites:

- ask user's permission to share location,
- enable Directions and Maps API, and
- get the API key.

Now, there are a few gotchas:

1. If you use React, first, you need to wait until the element has been rendered so that there's ref to it. Once you have ref, you have reference to [Element](https://developer.mozilla.org/en-US/docs/Web/API/Element). From this moment, you can use this element to render a map.
1. Both origin and destination have to be plain-object of two properties: `lat` and `lng`, both numbers.
1. Building directions is an asynchronous operation, and you'll see that, to show directions, you need to use callback function. Expect your map to re-render a couple times before it actually shows the route, not just destination point.
1. Still, it's a good idea to show destination point at first. Or maybe the origin point?

The whole logic is spaghetti of callback-driven operations:

- request permission to know user's location,
- actually get this location,
- wait until the element has been rendered (ref is instance of Element),
- instantiate the map and immediately initiate directions service to calculate route,
- wait until the route is ready, and finally,
- update the map (in fact, it will update automatically).

The code:

```javascript
class MyDirections extends Component {
  renderMap = (node) => {
    // sometimes node can be null; to make it work, it has to be instance of Element
    if (node instanceof Element) {
      const {
        destination // <- let's pretend we have destination location from props
      } = this.props;
      const map = new window.google.maps.Map(node, {
        zoom: 16,
        center: destination
      });

      new window.google.maps.Marker({ // -> show marker at the destination point by default
        position: destination,
        map
      });

      navigator.geolocation.getCurrentPosition((position) => {
        const {
          coords
        } = position;
        const origin = { // -> now we have origin location
          lat: coords.latitude,
          lng: coords.longitude
        };
        const directionsDisplay = new window.google.maps.DirectionsRenderer();
        const directionsService = new window.google.maps.DirectionsService();

        directionsDisplay.setMap(map);
        directionsService.route({
          origin,
          destination,
          travelMode: 'WALKING'
        }, (response, status) => {
          if (status === 'OK') {
            directionsDisplay.setDirections(response); // -> render the route, finally!
          } else {
            console.error('Could not build route:', status);
          }
        });
      }, () => {
        console.error('Could not request current location');
      });
    }
  }

  render() {
    <div className={styles.root}>
      <div
        className={styles.map}
        ref={node => this.renderMap(node)} />
    </div>
  }
}
```

----

Links:

- [Travel modes](https://developers.google.com/maps/documentation/javascript/examples/directions-travel-modes).
