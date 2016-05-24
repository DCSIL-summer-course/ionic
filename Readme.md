# Ionic v1.x

In the early stages of a startup it is rarely possible to build a native iOS app + a native Android app + a webapp. Doing so requires building 3 different codebases in 3 different languages. Many startups have focused on a single platform and then added support for other platforms later. The advantage of this approach is that developers can focus on one codebase, but the disadvantage is that fewer people can use their app. Ionic is an open source framework that attemps to solve this issue.

With Ionic, developers can create one JavaScript/AngularJS app that can be cross-compiled to run "natively" on iOS and Android. These apps run within webview containers that support HTML, CSS and JavaScript. Additionally, plugins exist to add native-like functionality to these JavaScript based applications.

Ionic isn't perfect, but it's a great plaform for rapidly prototyping applications and a good choice for an early-stage startup.

## A Wikipedia app

Using Ionic, we're going to create a small wikipeda app that will:

1. show a list of 10 article titles (hard-coded)
2. when a title is selected it takes them to a "details" view that includes the article
3. articles can be favourited

There will be 3 pages in our app:

1. Main page (list of 10 article titles)
2. details page (a specific wikipedia page)
3. Favourites page (list of favourited articles)

## Getting started

Follow the instructions on Ionic's getting started page: [http://ionicframework.com/getting-started/](http://ionicframework.com/getting-started/) (Be sure to fully follow the instructions in step 1!!!!!).

## Start the project

In your terminal type:

```bash
ionic start wikiApp blank
```

The above command generates a blank Ionic app to help you get started. Before we run your new app in the Android/iOS simulator we're going to start with a browser.

```bash
cd wikiApp
ionic serve
```

The blank app should now be open in your default browser. Let's start to modifying it!

## Creating the Wikipedia App

Open the `wikiApp` folder in your favourite text editor. The files that we'll be editing will be in `wikiApp/www`.

### Updating index.html

The first step is to delete everything nested in the `<body>` tag and update it to look like:

```javascript
<body ng-app="starter">
	<ion-nav-view></ion-nav-view>
</body>
```

`<ion-nav-view>` is a special element that changes its content based on the URL (e.g. state of app). In the next step we'll create a mapping between specific URL's (states) and specific templates (HTML views). `<ion-nav-view>` will make sure to display the corret template for the correct URL based on the mapping that we specificy.

### Creating App States

We're going to start by creating two states: 1) a special "abstract" state for our menu and a 2) normal state for our main page. A "normal" state is effectively a page in your app. To ensure that the menu is always available we make it `asbtract`, which means that it will be present when any of its children are active (e.g. Main). 

```
> Menu (abstract state)
  > Main (/app/main) 
```
If the user has navigated to `/app/main` then the parent abstract state, Menu, will be active as well.

Open `www/js/app.js` and we'll add the following code to the end of the file:

```javascript
.config(function($stateProvider, $urlRouterProvider) {

  $stateProvider
    .state('app', {
      url: '/app',
      abstract: true,
      templateUrl: 'templates/menu.html',
    })
    .state('app.main', {
      url: '/main',
      views: {
        'menuContent': {
          templateUrl: 'templates/main.html'
        }
      }
    })

  $urlRouterProvider.otherwise('/app/main');
});
```

The first state is an abstract state called `app`, it's an abstract state that will contain our menu (`templates/menu.html`). The second state is `app.main`, a child state of `app`. To get to the main state you can navigate to `/app/main`.

`templateUrl` is the path to the template (html) files that contain the content for the menu and main page respectively. We'll create those in the next step.

In the state `'app.main'` we explicitly say that the template `'templates/main.html'` will be rendered inside of `'menuContent'`. For this to work `<ion-nav-view name="menuContent"></ion-nav-view>` has to be defined within `'templates/menu.html'`.

The final line tells the app that if it reaches a state (URL) that is not explicitely mapped by `$stateProvider` to redirect to `/app/main`.

### Creating our templates

In `wikiApp/www` create a new folder called `templates`. This is where we'll put all our html files (e.g our app's views).

#### Creating the main page

Create the main page template `wikiApp/www/templates/main.html` and fill it with:

```html
<ion-view view-title="Main">
  <ion-content>
    <h1>Main Page</h1>
  </ion-content>
</ion-view>
```

`<ion-view view-title="Main">` tells Ionic to put a title, "Main", in the top nav bar. `<ion-view ...>` and `<ion-content>` are needed for pages to scroll properly - they're basically `<div>`'s on steroids.

#### Creating a menu

Now create the menu template `wikiApp/www/templates/menu.html` and copy and paste the following:

```html
<ion-side-menus enable-menu-with-back-views="false">
  <ion-side-menu-content>
    <ion-nav-bar class="bar-stable">
      <ion-nav-back-button>
      </ion-nav-back-button>

      <ion-nav-buttons side="left">
        <button class="button button-icon button-clear ion-navicon" menu-toggle="left">
        </button>
      </ion-nav-buttons>
    </ion-nav-bar>
    
    <!-- "menuContent" is where ionic puts the content, see app.js -->
    <ion-nav-view name="menuContent"></ion-nav-view>
  </ion-side-menu-content>

  <ion-side-menu side="left">
    <ion-header-bar class="bar-stable">
      <h1 class="title">Options</h1>
    </ion-header-bar>
    <ion-content>
      <ion-list>
        <ion-item menu-close href="#/app/main">
          Main
        </ion-item>
      </ion-list>
    </ion-content>
  </ion-side-menu>
</ion-side-menus>
```

Most of that code is just boilerplate code to tell Ionic that our app will have a menu and a navigation bar at the top of the screen. The title for the menu is defined by `<h1 class="title">Options</h1>` and the items in the menu are in `<ion-list>`. The main

#### Yay!

Your app now has a main page, a navigation bar and a side menu. If it doesn't please ask me or your neighbour for help!

### Adding controllers

Now that we have a couple templates and states we can add controllers. Controllers are where we put JavaScript-based logic that is associated with each template. We'll have to update the states to tell Ionic that the `app.main` state is paired with a specific controller `MainCtrl`.

In `app.js` update the `views` object in the `app.main` state to look like:

```javascript
  views: {
    'menuContent': {
      templateUrl: 'templates/main.html',
      controller : 'MainCtrl'
    }
  }
```

Now we need to create `MainCtrl`. First create a `controllers.js` file in `www/js/`, next we need to include it in our `index.html` file. Open `index.html` and above the line with `<script src="js/app.js"></script>` add `<script src="js/controllers.js"></script>`. `js/controllers.js`.

Now open `controller.js` and add the following line:

```javascript
angular.module('starter.controllers', [])
```

The above line creates a new module called `starter.controllers` with zero dependencies (e.g. the empty array). On the next line we'll create a controller that will exist within `starter.controllers`.

```javascript
angular.module('starter.controllers', [])
.controller('MainCtrl', function($scope) {
  console.log('MainCtrl instantiated.');
});
```
Just to recap what we'e done:

1. Told Ionic that the `app.main` state has a controller called `MainCtrl`.
2. Created the `controllers.js` file.
3. Included the `controllers.js` file in our `index.html` file.
4. Created a module called `starter.controllers`.
5. Created a controller called `MainCtrl` that's part of the `starter.controllers` module.

The last thing that remains is to make our app's main module `'starter'` dependendent on our `starter.controllers` module. Open up `js/app.js` and update the module definition code to look like:

```javascript
angular.module('starter', ['ionic', 'starter.controllers'])
```

## Showing topics on our main screen

In our `MainCtrl` we're going to create an array attached to the `$scope` variable that we can access within `main.html`. The array will simply be a list of arbitrary wikiepdia topics (e.g. monkey, canada, fiat currency, etc). Note that wikipedia titles, in the URL, are case sensitive. Feel free find your own 10+ topics.

### Cheat
```javascript
.controller('MainCtrl', function($scope) {
  $scope.topics = [
    'Monkey',
    'Fiat money',
    'Canada',
    'Lumber',
    'Lizard',
    'Emu',
    'Boat',
    'World War II',
    'BMW',
    'Computer science',
    'Granite',
    'Uranium'
  ];
});
```

### Listing the topics in our `main.html`
Next we want to list to topics in the view using `<ul>`, `<li>` and `<a>`. The `<a>`'s will make each topic clickable (eventually the user will click on the topic and be taken to the article - don't worry about the navigation right away). Try to do this on your own (hint: use `class="item"` and `ng-repeat`). Refer to [Ionic CSS Docs](http://ionicframework.com/docs/components/) for documentation on the correct Ionic CSS classes to add to your `<ul>`, `<li>` and `<a>` elements.

### Cheat
In `main.html`:

```javascript
<ion-view view-title="Main">
  <ion-content>
    <ul class="list">
        <li ng-repeat="topic in topics">
          <a class="item" href="#">{{topic}}</a>
        </li>
    </ul>    
  </ion-content>
</ion-view>
```
### Adding some functions in our `MainCtrl`

Presently our list has two drawbacks: 1) the links don't go anywhere and 2) 2 of our topics have lowercase second words. We're going to solve the first problem by creating `$scope.createLink(title)` and the second with `$scope.capitalize(title)`. `$scope.createLink(title)` will return a link like so `"world war II"` => `"#/entry/world%20war%20II"`. Try to do this on your own.

### Cheat
```javascript
$scope.createLink = function(str){
 return '#/entry/' + encodeURIComponent(str);
}
  
$scope.capitalize = function(str){
  var parts = str.split(' ');
  return parts.map((s) => {
    return s.replace(/^[a-z]/, (t) => { 
      return t.toUpperCase() 
    });
  }).join(' ');
};
```

### Update `main.html` to include the two functions
```html
<a class="item" ng-href="{{createLink(topic)}}">{{capitalize(topic)}}</a>
```

Note that capitalize function would have been nicer as an [AngularJS filter](https://docs.angularjs.org/tutorial/step_09).

## Viewing the Wikipedia Story

At the end of this section we want to be able to:

1. click on a story and be taken to a new page in our app
2. The new page will fetch the story from the Wikipedia API
3. Display that story on the page

Clues: 1) create a new state that corresponds to the URL in the capitalize function (e.g. `/entry/:title`, 2) create a new controller (e.g. `EntryCtrl`), 3) create a template (e.g. `entry.html`) and 4) create a function in the controller that uses `$http.jsonp()` to fetch the content from Wikipedia's API endpoint and use the following URL:

```
https://en.wikipedia.org/w/api.php?action=query&callback=JSON_CALLBACK&prop=extracts&format=json&exintro=&titles=TITLE_OF_ARTICLE
```  
To get the title from the URL, within the controller, use `$stateParams`. Try to solve this on your own before scrolling down.

### Cheat - Creating the state

In `app.js` we're going to add a new state right after the `app.main` state:

```javascript
.state('app.entry', {
  url: '/entry/:title',
  views: {
    'menuContent': {
      templateUrl: 'templates/entry.html',
      controller: 'EntryCtrl'
    }
  }
})
```

### Cheat - Creating the Controller, `EntryCtrl`

```javascript
.controller('EntryCtrl', function($scope, $stateParams, $http){
  var urlStub = 'https://en.wikipedia.org/w/api.php?action=query&callback=JSON_CALLBACK&prop=extracts&format=json&exintro=&titles=';
  // append the article's title to the URL
  var url = urlStub + encodeURIComponent($stateParams.title);

  $http.jsonp(url)
    .then(function(response){
      var temp = response.data.query.pages;
      // the key for the article is an id number, must get it first
      var pageId = Object.keys(temp)[0];
      // once we have the ID, we can retrieve the article
      var article = temp[pageId];

      $scope.title = article.title;
      $scope.entry = article.extract;
    });
});
```

### Creating the Template, `entry.html`

First Create the template, `templates/entry.html`. We want the entry page to display the article's title and display a the article (html and all). Hint: use `<ion-nav-title>` for the title and `ng-bind-html` to display the article, which includes html. For more information on [ng-bind-html](https://docs.angularjs.org/api/ng/directive/ngBindHtml).

#### Cheat
Create the template, `templates/entry.html` and include the following lines:

```html
<ion-view>
  <ion-nav-title>{{title}}</ion-nav-title>
  <ion-content padding="true">
    <div ng-bind-html="entry">
    </div>
  </ion-content>
</ion-view>
```

## Adding a Favourite Button

We're going to add a favourite button to each article's entry page. The button will display the heart icon by default and if the user favourites the article it will switch to a check-mark. In a real web application we'd store favourites on the server, in our application we'll just store them in a service (e.g. in-memory).

steps:

1. Add button to template `templates/entry.html`
2. Add function `$scope.favourite` to  the `EntrCtrl`
3. Call `toggleFavourite(title)` via `ng-click` on the button
4. Create a file called `services.js` in the `js` folder and reference it in `index.html`
5. In `services.js` create a new module `angular.module('starter.services', [])`
6. In `app.js` make your app dependent on that module, like you did with the controller.
7. In `services.js` define your service `.factory('favourites', function(){ /.../ });`
8. Your factory should return a JavaScript object that has two functions: `toggleFavourite(title)` and `isFavourited(title)`. The factory should also store an object where the keys are titles and the values are true/false depending on where the article was favourited or not.
9. Connect the service to your controller.
10. Update the entry to use `isFavourited` and `ng-show`/`ng-hide` to hide/show the icons.


### Mega Cheat!

### Step 1
Add button to template `templates/entry.html`

```html
<ion-view>
  <ion-nav-title>{{title}}</ion-nav-title>
  <ion-content padding="true">
    <button class="button button-balanced button-block">
      <i class="icon ion-heart"></i>
      Favourite
    </button>
    <div ng-bind-html="entry">
    </div>
  </ion-content>
</ion-view>
```

### Step 2

Add function `$scope.favourite` to  the `EntrCtrl`

```js
$scope.toggleFavourite = function(title){
  console.log('Favourite ' + title);
}
```

### Step 3

Call `toggleFavourite(title)` via `ng-click` on the button you created in step 1.

```html
<button class="button button-balanced button-block" ng-click="toggleFavourite(title)">
  <i class="icon ion-heart"></i>
  Favourite
</button>
```

### Step 4
Create a file called `services.js` in the `js` folder and reference it in `index.html`

```html
<!-- your app's js -->
<script src="js/services.js"></script>
<script src="js/controllers.js"></script>
<script src="js/app.js"></script>

```

### Step 5

In `services.js` create a new module `angular.module('starter.services', [])`

### Step 6

In `app.js` make your app dependent on that module, like you did with the controller.

```javascript
angular.module('starter', ['ionic', 'starter.controllers', 'starter.services'])
```

### Step 7

In `services.js` define your service `.factory('favourites', function(){ /.../ });

```javascript
angular.module('starter.services', [])
.factory('favourites', function(){ 

});
```

### Step 8

Your factory should return a JavaScript object that has two functions: `toggleFavourite(title)` and `isFavourited(title)`. The factory should also store an object where the keys are titles and the values are true/false depending on where the article was favourited or not.

```javascript
angular.module('starter.services', [])
.factory('favourites', function(){ 
  var favourites = {};

  favourites.store = {};

  favourites.toggleFavourite = function(title){
    favourites.store[title] = !favourites.store[title];
  }

  favourites.isFavourited = function(title){
    return favourites.store[title];
  }

  return favourites;
});
```

### Step 9

Connect the service to your controller. Note that we're referencing `favourites` in the function's signature.

```javascript
.controller('EntryCtrl', function($scope, $stateParams, $http, favourites){
  var urlStub = 'https://en.wikipedia.org/w/api.php?action=query&callback=JSON_CALLBACK&prop=extracts&format=json&exintro=&titles=';
  var url = urlStub + encodeURIComponent($stateParams.title);

  $http.jsonp(url)
    .then(function(response){
      var temp = response.data.query.pages;
      var pageId = Object.keys(temp)[0];
      var article = temp[pageId];

      $scope.title = article.title;
      $scope.entry = article.extract;
    });

  $scope.toggleFavourite = function(title){
    console.log('Favourite ' + title);
    favourites.toggleFavourite(title);
  }

  $scope.isFavourited = favourites.isFavourited;
});
```

### Step 10

Update the entry to use `isFavourited` and `ng-show`/`ng-hide` to hide/show the icons.

```html
<ion-view>
  <ion-nav-title>{{title}}</ion-nav-title>
  <ion-content padding="true">
    <button class="button button-balanced button-block" ng-click="toggleFavourite(title)">
      <i class="icon ion-heart" ng-hide="isFavourited(title)"></i>
      <i class="icon ion-checkmark-round" ng-show="isFavourited(title)"></i>
      Favourite
    </button>
    <div ng-bind-html="entry">
    </div>
  </ion-content>
</ion-view>
```