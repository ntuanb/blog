---
layout: post
title: Ionic - Swipe to Load Next or Previous Page
---

Hybrid frameworks such as **Ionic** provide an easier way to develop a phone app using
a language that you would usually prefer into a native looking app that is smooth
and has all the features of the phone readily available to be used.

Using Ionic, we can create a page that allows swipe movements on the screen to
move to the next item in the array.

### Controller

As usual, set up your a blank Ionic project and install all the required components
to make the project work.

In the controller, add the following snippet to the attached view:

{% highlight js %}

$scope.pages = [
	{'id': 1, 'name': 'Page 1', 'content': 'Page 1 Content'},
	{'id': 2, 'name': 'Page 2', 'content': 'Page 2 Content'},
	{'id': 3, 'name': 'Page 3', 'content': 'Page 3 Content'},
];

$scope.swipeRight = function swipeRight(id) {

	var curr_page = null;

	for (var i = 0; i < $scope.pages.length; i++) {
		if ($scope.pages[i].id === id) {
			if (i == 0) {
				curr_page = $scope.pages[
					$scope.pages.length - 1
				];
			}
			else {
				curr_page = $scope.pages[i - 1];
			}

			$location.path('/pages/' + curr_page.id);
		}
	}

}

$scope.swipeLeft = function swipeLeft(id) {

	var curr_page = null;

	for (var i = 0; i < $scope.pages.length; i++) {
		if ($scope.pages[i].id == id) {
			if (i == $scope.pages.length - 1) {
				curr_page = $scope.pages[0];
			}
			else {
				curr_page = $scope.pages[i + 1];
			}

			// Tells Ionic which transition direction
			$ionicViewSwitcher.nextDirection('back');
			$location.path('/pages/' + curr_page.id);
		}
	}

}

{% endhighlight %}

### HTML

Add the following to the template:

{% highlight html %}

<ion-view view-title="page.name">
	<ion-content
		on-swipe-left="swipeLeft()"
		on-swipe-right="swipeRight()"
		>
		{% raw %}{{ page.content }}{% endraw %}
	</ion-content>
</ion-view>

{% endhighlight %}

There is currently a problem where the swipes aren't immediate because the page
needs to slide, but we'll work on adding them later.
