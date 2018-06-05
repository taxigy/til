# Transparent overlay image

So you have a screen with a big view that contains a log of buttons placed on top of gradient color background. It's cool, however there's no "background-image: linear-gradient" thing in React Native. To put any image that isn't a solid color (which could be done with View component), you need to put it as an image:

```javascript
<View>
  <Text style={{ fontSize: 40 }}>Some header</Text>
  <Button>Some button</Button>
  <Image
    style={{ height: '100%', width: '100%' }}
    source={require('./path/to/image.png')}
  />
</View>
```

But now the button isn't clickable because it is below the image that takes the whole space within its parent element.

To pass tap events through, use "pointerEvents" prop on Image component:

```javascript
<View>
  <Text style={{ fontSize: 40 }}>Some header</Text>
  <Button>Some button</Button>
  <Image
    pointerEvents="none"
    style={{ position: 'absolute', height: '100%', width: '100%' }}
    source={require('./path/to/image.png')}
  />
</View>
```

The [official docs](https://facebook.github.io/react-native/docs/view.html#pointerevents) describe this prop for View component only, but it works for Image as well.
