## Factories and Directives Working Together

In our demo app, here's what our InboxFactory looks like:

```js
angular.module('EmailApp')
  .factory('InboxFactory', function InboxFactory ($q, $http, $location) {
    'use strict';
    var exports = {};

    exports.messages = [];

    exports.goToMessage = function(id) {
      if ( angular.isNumber(id) ) {
        // $location.path('inbox/email/' + id)
      }
    }

    exports.deleteMessage = function (id, index) {
      this.messages.splice(index, 1);
    }

    exports.getMessages = function () {
      var deferred = $q.defer();
      return $http.get('json/emails.json')
        .success(function (data) {
          exports.messages = data;
          deferred.resolve(data);
        })
        .error(function (data) {
          deferred.reject(data);
        });
      return deferred.promise;
    };

    return exports;
  });
```

And here's what our finished Directive looks like:
```js
app.directive('inbox', function () {
    return {
        restrict: 'E',
        replace: true,
        scope: true,
        templateUrl: "js/directives/inbox.tmpl.html",
        controllerAs: 'inbox',
        controller: function (InboxFactory) {
            this.messages = [];
            this.goToMessage = function (id) {
                InboxFactory.goToMessage(id);
            };
            this.deleteMessage = function (id, index) {
                InboxFactory.deleteMessage(id, index);
            };
            InboxFactory.getMessages()
                .then( angular.bind( this, function then() {
                    this.messages = InboxFactory.messages;
                }) );
        },
        link: function (scope, element, attrs, ctrl) {
            /* 
            by convention we do not $ prefix arguments to the link function
            this is to be explicit that they have a fixed order
            */
        }
    }
  });
```

Now we can use this directive anywhere in our application's scope by creating an `<inbox></inbox>` element (using `restrict: 'E'` to specify an element directive).

When the application runs, Angular will replace the `<inbox>` element with the template at the `templateUrl`. We then make an alias for the controller under the name of `inbox`. This alias is then accessible inside the controller as `this` and inside the template as `inbox`. If you look inside the template you'll see expressions like `inbox.messages` and `inbox.deleteMessage(id, $index)`. These are the same `this.messages` and `this.deleteMessage` we can see this in the InboxFactory.

Finally we have a link function that will run straight after the controller runs. The link function will then receive the aliased controller as the fourth argument, here we named it `ctrl`.  Yes that's right, the link function has fixed positions for it's arguments i.e. scope is always first.

###### Note: This is different behavior to the controller's arguments which are injected and therefore can take any order. For the link function we don't use the $ prefix for scope, element and attributes to make it clear that they aren't under the control of dependency injection.

Here's the complete HTML template (view) for the directive:

```html
<div class="inbox">
  <div class="inbox__count">
    You have {{ inbox.messages.length && inbox.messages.length || 'no' }} messages
  </div>
  <div ng-hide="!inbox.messages.length">
    <input type="text" class="inbox__search" 
      ng-model="inbox.search" 
      placeholder="Search by 'from', e.g. TicketMaster">
  </div>
  <ul class="inbox__list">
    <li ng-show="!inbox.messages.length">
      No messages! Try sending one to a friend.
    </li>
    <li ng-repeat="message in inbox.messages | filter:{ from: inbox.search }">
      <div class="inbox__list-info">
        <p class="inbox__list-from"> from: {{ message.from }} </p>
        <p class="inbox__list-date"> {{ message.date | date: 'dd/MM/yyyy' }} </p>
        <p class="inbox__list-subject"> {{ message.subject }} </p>
      </div>
      <div class="inbox__list-actions">
        <a href="#" ng-click="inbox.goToMessage(message.id);">
          Read
        </a>
        <a href="" ng-click="inbox.deleteMessage(id, $index);">
          Delete
        </a>
      </div>
    </li>
  </ul>
</div>
```

Notice the use of built-in angular directives such as `ng-hide`, `ng-show`, `ng-click`, `ng-repeat` and `ng-model`. We won't go into much detail about these directives as each can be quite a big topic but you can read about them [here](https://docs.angularjs.org/api/ng/directive).

> Code check: [06-first-directive](https://github.com/Thinkful/guide-intro-to-angular/tree/master/clean/06-first-directive)

###### Note: To run this Code check you'll need to:
- Make sure you've downloaded the code. Do this by going [here](https://github.com/Thinkful/guide-intro-to-angular/tree/clean) and either cloning the repo or clicking "Download Zip".
- In your terminal, navigate to the project folder (e.g. `/Users/carl/Downloads/guide-intro-to-angular/app/06-first-directive`)
- Run a simple local server. On a Mac, you can do this by running `python -m SimpleHTTPServer`. If you're on windows, try doing this by installing [Mongoose](https://code.google.com/p/mongoose/).
- In your browser, go to `http://localhost:8000/`

The 3 files we just saw are located here:
- Inbox Factory: `js/factories/InboxFactory.js`
- Inbox Directive: `js/directives/inbox.js`
- Inbox Template: `js/directives/inbox.tmpl.html`
