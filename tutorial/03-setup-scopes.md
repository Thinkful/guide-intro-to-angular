## Setup, Scopes and Directives

When learning a new framework it helps to have something working as soon as possible. This enables you to play around and dig into what does what. But, before we can get there, we need to talk about Angular scopes and our first two directives: `ng-app` and `ng-controller`.

One of the most fundamental parts of Angular is scopes. Scopes hold your Models (that's your data), they cooperate with your Controllers and they give the Views everything they need (that's what the user sees and interacts with). Angular scopes operate in a very similar way to the common JavaScript concept of [scope](http://msdn.microsoft.com/en-us/library/ie/bzt2dkta(v=vs.94).aspx).

The first scope we'll need is the application scope, this is exactly what you'd expect it to be: that's the scope your Angular application can operate in. We set this up in our HTML using the `ng-app` attribute.

```html
<html ng-app="myApp">
    <head></head>
    <body></body>
</html>
```

Notice how we gave our app a name of `'myApp'`. This will be usable in our whole html file.

###### Note: We can use ng-app without the "myApp" to declare a default app.  In other words, just `<html ng-app>`.

The second scope is `ng-controller`, this will determine where our controller can operate. We can have multiple controllers within our application. Each controller will have it's own scope. For example, we may have an `inbox.html` file, containing the below code. It will give responsibility to a controller named `'InboxCtrl'` (in our JavaScript) for this scope.

```html
<div ng-controller="InboxCtrl">
    <!-- inside InboxCtrl scope -->
</div>
```

Both, `ng-app` and `ng-controller`, are Angular directives. Think of Angular directive as something that allows you to extend your HTML. Here's a [more nerdy explanation](http://stackoverflow.com/a/13898058/1319206) in case you're interested.

## Your First Controller: A Quick Example

Let's put everything we just learned into practice.

1. Start with a blank HTML document
2. Link your Angular and jQuery files

```html
<script src="lib/jquery-v1.11.1.js"></script>
<script src="lib/angular-v1.2.22.js"></script>
```

3. Add `ng-app="myApp"` to the HTML tag
4. Inside the body, create a sample controller

```html
<div ng-controller="TestCtrl">
    <h1>{{title}}</h1>
    <input type="text" ng-model="title">
</div>
```

We'll explain what `{{title}}` and `ng-model="title"` is in a minute.

5. Create an inline JavaScript script — make sure this is below the script tag where you load the libraries.

```html
<script>
  function TestCtrl($scope) {
    $scope.title = 'Write a title here...';
  };
</script>
```

See how the function name is the same as the `ng-controller's` value?  Angular will be looking for a function with this name in our JavaScript so that it can act as a Controller.  Nifty stuff!

Note: As of Angular 1.3.0 this behavior has been removed and global constructors are not working anymore and will result in errors. [Changelog](https://github.com/angular/angular.js/blob/master/CHANGELOG.md#130-beta15-unbelievable-advancement-2014-07-11)

Below's the final version:

```html
<!doctype html>
<html ng-app>
  <head>
    <title>Sample AngularJS Controller</title>
  </head>
  <body>
    <div ng-controller="TestCtrl">
        <h1>{{title}}</h1>
        <input type="text" ng-model="title">
    </div>

    <script src="lib/jquery-v1.11.1.js"></script>
    <script src="lib/angular-v1.2.22.js"></script>

    <script>
      function TestCtrl($scope) {
        $scope.title = 'Write a title here...';
      };
    </script>
  </body>
</html>
```

> Code check: [02-sample-controller](https://github.com/Thinkful/guide-intro-to-angular/tree/clean/app/02-sample-controller)

__This can be confusing!__ It often helps to see the code in action. View the result by downloading the _Code check_ and opening the `index.html` file in your browser.  Be sure to change the $scope.title's value as well as play around with the input.

Interested in learning more about controllers? Here's a [good guide from AngularJS.org](https://docs.angularjs.org/guide/controller).
