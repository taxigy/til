# Parse an API response which is a list of objects in Flutter

I had a few cryptic error messages and figured that if an API response is a JSON that looks like

```json
[{
  "id": "123-abc",
  "value": 4
}, {
  "id": "456-xyz",
  "value": 7,
}]
```

and the corresponding class for the object is like

```dart
class SomeObject {
  final String id;
  final String value;

  factory SomeObject fromJson(Map<String, dynamic> json) {
    return SomeObject(id: json['id'], value: json['value']);
  }
}
```

then sending a request to the API would be like

```dart
Response response = await post(
  Uri.parse(...),
  headers: ...,
  body: ...,
);

List<dynamic> responseBody = json.decode(response.body);
```

but when the time comes to deserialize the response body, it's important that the result of that is an Iterable, not a List:

```dart
Iterable<SomeObject> result =
  responseBody.map<SomeObject>((element) => SomeObject.fromJson(element));
```

here, replace Iterable with List to get an error like

```
type 'MappedListIterable<dynamic, SomeObject>' is not a subtype of type 'List<SomeObject>'
```

so keeping it an Iterable solves the issue.

If the surrounding function is supposed to return a list of SomeObject, convert the result to a list:

```dart
return result.toList(); // -> will return a List<SomeObject> instead of Iterable<SomeObject>
```

