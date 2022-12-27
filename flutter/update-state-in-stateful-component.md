# Update state in a stateful component in Flutter

Very similar to React:

```dart
class SomeForm extends StatefulWidget {
  @override
  SomeFormState createState() => SomeFormState();
}

class SomeFormState extends State<SomeForm> {
  String someState = '';

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextField(
          onChanged: (value) {
            // here: instead of just assigning a new value directly to someState, use setState function
            setState(() {
              someState = value;
            });
          },
        ),
        ...
      ],
    );
  }
}
```

