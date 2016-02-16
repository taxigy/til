# Subscriptions in Meteor with Angular
Meteor with Angular as view engine has somewhat different type of subsciption
engine, as opposed to classic Meteor's pub/sub model. To subscribe to a
collection, the easiest way would be to inject `$reactive` service into
controller scope:

```javascript
'use strict';
angular.module('whatever').controller('SomeController', ['$scope', '$reactive', '$stateParams', '$meteor', function ($scope, $reactive, $stateParams, $meteor) {
  $reactive(this).attach($scope);

  this.subscribe('users');
  this.subscribe('parties');
  this.subscribe('images');

  const party = Parties.findOne({
    _id: $stateParams.partyId
  });
  const images = Images.find({
    _id: {
      $in: _.map(party.images, image => image.id)
    }
  });
  const imageDescriptionList = images.fetch().map(image => ({
    url: image.url(),
    ..._.find(party.images, ({id}) => id === image._id).dimensions
  }));

  $scope.name = party.name;
  $scope.description = party.description;
  $scope.images = imageDescriptionList;
}]);
```

In this code, `$reactive` called with `this` mutates `this` adding, among
others, the `subscribe` method so now it's possible to use `this.subscribe` to
subscribe to certain publications. After that, refer to collections as globals,
without using `$meteor.collection`.

## Links
* [`$reactive` service docs](http://www.angular-meteor.com/api/1.3.0/reactive)
