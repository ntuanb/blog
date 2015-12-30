---
layout: post
title: Ionic ng-if, ng-show, ng-hide and ng-class
---

All these attributes are similiar in a way where they can all be used to display
or hide an element, but differ in the way they function.

### ng-show and ng-hide

ng-show and ng-hide work by adding and removing an Angular specific class
**.ng-hide** to the element.

### ng-if

ng-if works by displaying the element if the variable is true and completely
removing the element if false. This can affect performance if the element changes
constantly, where the DOM has to manually create and remove each time, taking up
CPU cycles unnecessarily.

In most cases, loading the items straight after the initial load is usually better
to keep things smoother during usage, as long as it doesn't hinder the initial load
speeds.

### ng-class

ng-class works by adding a custom class you define if a variable is equal to what
you choose.

{% highlight html %}

<button ng-class="{'d-none': buttonIsViewable == true}">
	Button
</button>

{% endhighlight %}

It works just like ng-show and ng-hide, but with the added customizability. ng-class
can also get more complicated by adding in || and &&, just like a normal if/else
statement.
