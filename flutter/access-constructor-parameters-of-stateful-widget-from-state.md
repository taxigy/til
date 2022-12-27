# Access the constructor parameters, provided to a stateful widget, from state

A situation I've bumped into a few times: when there's a screen that shows a list of something, and tapping on any of the tiles should lead to another screen that shows details of something(id=x). The code for navigator transition looks like

```dart
Navigator.push(
  context,
  MaterialPageRoute(
    builder: ((context) {
      return SomeScreen(
        id: someModel[i].id,
      );
    }),
  ),
);
```

Cool, how is then SomeScreen, which is a stateful widget, supposed to refer to id?

A naive way would be to drill the value of id down via SomeScreen.createState, but this makes Flutter linter unhappy.

Turns out there's a happy way! Inside `State<SomeScreen>`, there's a variable called `widget` that provides access to all of the SomeScreen's fields (it seems?).

Code:

```dart
class SomeScreen extends StatefulWidget {
  final String id;

  const SomeScreen({super.key, required this.id});

  @override
  State<SomeScreen> createState() => SomeScreenState();
}

class SomeScreenState extends State<SomeScreen> {
  Future<void> fetchTicketDetails() async {}

  @override
  Widget build(BuildContext context) {
    // here, get the value of SomeScreen.id via widget.id:
    return Text(widget.id);
  }
}
```

