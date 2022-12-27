# Sort dates in Dart

Say, there's a list of dates:

```dart
List<DateTime> dates = [DateTime(2022, 12, 27), DateTime(2022, 12, 26), ...];
```

To sort them in descending order, latest first:

```dart
dates.sort((a, b) => b.compareTo(a));
```

and in ascending order:

```dart
dates.sort((a, b) => a.compareTo(b));
```

Same works for a list of objects:

```dart
class SomeObject {
  DateTime date;
}

List<SomeObject> dates = [
  SomeObject(date: DateTime(2022, 12, 01)),
  SomeObject(date: DateTime(2022, 12, 02)),
  ...
];

// in descending order
dates.sort((a, b) => b.date.compareTo(a.date));
```

