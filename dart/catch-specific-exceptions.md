# Catch specific exceptions

In Dart, it's possible to process exceptions of a specific type separately from each other in a try/catch block:

```dart
try {
    doSomething();
} on SomeException catch (error) {
    print('some exception happened, see more: $error');
} on AnotherException {
    print('another exception happened');
}
```

Convenien: define `toString` method of an exception class to make it render nicely when interpolated in a string:

```dart
class SomeException implements Exception {
    final int code;
    final String message;

    SomeException({
        required this.code,
        required this.message,
    });

    @override
    String toString() {
        return 'SomeException{code=$code,message="$message"}'; // <- gonna render that when in a string, like in 'some exception happened, see more: $error'
    }
}