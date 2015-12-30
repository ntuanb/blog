---
layout: post
title: Ionic Link Bug
---

Currently on the iOS, there is a link bug which occurs between page transitions
with Ionic. I don't know if it occurs when the app is published, but it
occurring on the Ionic App thats used for testing.

If the link uses **menu-close** or **nav-clear**, occasionally the page swipes
backwards and other times, the back button occurs. If it was a consistent problem,
a work around or the bug could be fixed, but in this case, it only occurs occasionally.

To solve this, we'll use **ng-click** and **$location.path()** to create the link in
the controller. This solves the transition problem and also lets us control the
next view animation. This probably isn't the best solution, but it seems to work
well enough for now.

### HTML

{% highlight html %}

<a ng-click="goTo('/app/place')">
	Lets Go To This Place
</a>

{% endhighlight %}

### JS

{% highlight js %}

$scope.goto = function goTo(path) {

	// Clears the history because we don't need
	$ionicHistory.clearHistory();

	// Disables the back button
	$ionicHistory.nextViewOptions({disableBack: true});

	// If the sidebar is open, lets close
	if ($ionicSideMenuDelegate.isOpen()) {
		$ionicSideMenuDelegate.toggleLeft();
	}

	// Lets go to the destination
	$location.path(path);
}

{% endhighlight %}
