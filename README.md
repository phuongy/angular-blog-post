# Introduction to AngularJS
By @PhuongTHuynh

This is the long overdue follow-up from my AngularJS introduction talk, back in September 2014. In the talk I shared a few things I had learnt about AngularJS, from projects I’ve worked on using AngularJS and from things I read about how to best use it in a project.

I often explain to people that AngularJS is deceivingly simple when you first start. That you can do some pretty cool things, make some classes change with very little effort. In actual fact, the complexity of the framework shows itself when you try to add a bit more to your application.

In this article, I will go through some of the basic concepts and best practices that were mentioned in the presentation, with updated things I’ve learnt since the talk.

## Getting started

To get your first AngularJS application running, at its most basic, you just need to:

- include the angular library,
- create an angular module, 
- and include an `ng-app` attribute at the top of your page.

```
<html ng-app=“app”>
.
.
.
<script src=“angular.js></script>
<script>
	angular.module(‘app’, []);
</script>
</body>
</html>
```

It is recommended that you use the non-minified version of the library when you’re in development, it makes life easier when you need to debug or see error messages.

## Writing your first AngularJS application

Firstly, to add some basic functionality to your application, the easiest way to do this is by using the built-in directives that AngularJS provides.

Some of the more common directives include,

- ng-click,
- ng-show,
- ng-repeat,
- and ng-class.

As a basic example, the code below show a `divClass` variable is changed when a button is clicked, using the ng-click directive. AngularJS has a built-in two-way data binding that will update ng-class as a result of the `divClass` value change.

```
<div ng-class=“divClass”></div>
<button ng-click=“divClass=‘red’“>Red</button>
<button ng-click=“divClass=‘green’“>Green</button>
<button ng-click=“divClass=‘blue’“>Blue</button>
```

todo: show gif or CodePen of this in action

## A basic Todo list application

This next example uses a different combination of directives to achieve basic todo list functionality. 

### Todo list view

```
<div class=“todoAdd”>
	<input type=“text” ng-model=“todo.description” />
	<button ng-click=“todo.add()”>+ Add</button>
</div>
<div class=“todosList”>
	<div class=“todo” ng-repeat=“item in todo.items”>
		<div class=“todo_task” 
				 ng-show=“item.visible” 
				 ng-bind=“item.description”>
		</div>
		<div class=“todo_action”>
			<button ng-click=“todo.remove(item.id)”>x Remove</button>
		</div>
	</div>
</div>
```

The `ng-repeat` directive is used to display a list of todo list items. Usually if you wanted to display a list, you would need to connect this code to a controller that contained an array or object, but I will go through this in more detail later in the article.

The `ng-click` directive is used in the add button and remove button to manipulate the todo list.

The `ng-show` directive is bound to the visible state of the todo item, this allows the todo item to be hidden when the remove button is pressed.

Lastly, the `ng-bind` directive can be used to display the value of a variable. I tend to prefer the `ng-bind` over using {{item.description}}, mainly to keep the DOM clean, since it would add an extra span element just to display the value.

If you inspect the demo, you should be able to see the generated HTML and the binding in action as you add or remove items.

todo: add CodePen for this example

### Todo list controller

Controllers are used in AngularJS to handle state for your view.   They can quickly add complexity to your code, so I tend to limit how many I have in a single view.

For the todo list example, we would hook up the controller as below.

```
angular
	.module(‘app’)
	.controller(‘TodoCtrl’, function() {
	
		this.description = {};
		this.items = [];

		this.add = function() {
			this.items.push(
				{
					‘id’: this.items.length+1,
					’description’: this.description, 
					‘visible’: true
				}
			);

			this.description = ‘’;
		};

		this.remove = function(id) {
			this.items[id-1].visible = 0;
		};

	});
```

Note:
- It is recommended to use `angular.module` when referring to your module, rather than global variables.
- It is also better practice to not use the scope variable in combination with `controllerAs`.

In this example, the Controller `TodoCtrl` is declared. There are two variables that are required by the view. 

- description variable is bound to the input field that takes the description
- items is the array that holds the todo list items

The functions required for `ng-click` in the view are declared in this Controller.

With everything declared, the controller can be called by any parent DOM in the view 

```
<body ng-controller=“TodoCtrl as todo”>
…
</body>
```

## Tackling more complicated applications

If you’re looking into using AngularJS for your project, likely it is going to be more complicated than the examples included so far. For a decent sized project, you will need to use some of the more advanced featured provided by AngularJS.

### Custom directives

I believe custom directives is where AngularJS shines. You could break down a complicated application into components and self contain the logic into directives, making things a lot easier on yourself for maintaining and scaling the application in future.

```
<phone>
		<speaker></speaker>
		<screen>
			<navbar>
				<h1>Title</h1>
			</navbar>
			<container id=“main” position=“main”>				
				<main>
					main
				</main>
			</container>
			<container id=“menu” position=“offscreen” align=“right”>
				<nav>
					offscreen menu
				</nav>
			</container>
			<utility></utility>
		</screen>
		<home-button></home-button>
</phone>
```

This was a quick demo to get a point across about how you could Directives in a project. 

By separating the `home-button`, for example.

```
angular
.module(‘myApp’)
.directive(‘homeButton’, function() {
	return {
		restrict: ‘E’,
		replace: true,
		link: function(scope, element, attr, ctrl) {
			
		},
		controller: function() {
			this.clicked = function() {
				console.log(‘clicked’);
			};
		},
		controllerAs: ‘homeButtonCtrl’,
		template: ‘<div class=“home-button”><span class=“home-button-symbol” ng-click=“homeButtonCtrl.clicked()”></span></div>’
	}
});
```

My functions for handling the home button events are contained within this Directive. I could do the same with any states. 

Another thing worth mentioning, I could have bounded an  on(‘click’) event to the element in link, this will work, but I have found from experience that using ng-click and other ng event directive make things a bit more transparent about what is happening. I tend to prefer this, even though it feels like bringing back the old JavaScript inline onclick functions.

### Services and factories

Services and Factories in AngularJS is how you should be managing your model state. These things can be used as singletons to store state for your application, and then passed through to the Directives and Controllers, simplifying how state can be mutated.

My preference for representing models in AngularJS has always been with a Factory. I find that I tend to use Service only for things to connect to a Provider.

### Providers


