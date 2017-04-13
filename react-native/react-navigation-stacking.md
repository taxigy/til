# React Navigation and screen stacking

With [react-navigation](https://github.com/react-community/react-navigation), it's easy to scaffold navigation for your React Native app. There's one thing to keep in mind: it uses "stacks", when you have all the navigation history (just like history in a browser) stacked, but there may be many stacks. You can transition between stacks, which literally means switching to one or another stack. Each of them is _stateful_, so you have to be aware of what screen the user is going to jump at.

To make stacks separate, declare them separately.

In some cases, you may want to keep a single-screen stack (yep, still a stack) for home screen, just to make sure every time the user taps "go to home", they really go to home screen, and not any other screen that is in the "home" stack and accidentally is on top of current stack's state.

("stack stack stackity stack")

To make it work, you can actually pass stacks as value of `screen` property of the navigation config argument:

```javascript
const HomeStack = StackNavigator({
  Home: { screen : HomeScreen },
});

const MessagesStack = StackNavigator({
  MessageList: { screen: MessageListContainer },
  MessageDetails: { screen: MessageDetailsContainer },
});

const AppNavigation = StackNavigator({
  Tabs: {
    screen: TabNavigator({
      Home: { screen: HomeStack },
      Messages: { screen: MessagesStack },
    }),
  },
});
```

This way, when you `this.props.navigation.navigate('Home')` from any other location and any other stack, you're completely sure that it is isolated and the user will see exactly the home screen.

---

Todo:

- better examples, less hypothetical, more practical.
