# Show a snackbar (toast) with an error message in Flutter

A typical scenario is when an event handler needs to process some value and throw an exception in case of failure, and it would be nice to inform the user that there was an error. Snackbar seems like a good way to do that.

Here's an example of a handler:

```dart
void handleSomething<T>(T value) {
    try {
        someProcessing();
    } catch (error, stacktrace) {
        print(error);
        print(stacktrace);

        ScaffoldMessenger.of(context).showSnackBar( // <- show the snackbar
            SnackBar(
                content: Text(
                    'Error: $error',
                ),
            ),
        );
    }
}
```