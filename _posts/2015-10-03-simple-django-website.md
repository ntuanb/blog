---
layout: post
title: Simple Django Website
---

This tutorial is for version 1.5 as it was written about a year ago during the early days
where I started off using Python first.

For Django, I believe there hasn't been a huge change since then, so this guide will definitely still be useful but
for more information, best check out their actual website at [Django Project](https://djangoproject.com).

### Install

To begin, run the command:

{% highlight js %}

pip install django

{% endhighlight %}

This will install the Django project into the console so that the project can be
created using Django's own commands.

### Create the project

Change to your chosen directory and use the following command to create the
project.

{% highlight js %}

django-admin startproject myblog

{% endhighlight %}

### Database

Open the file **settings.py** in the folder and edit the **DATABASES** variable
to connect the website tot he **POSTGRESQL** database:

{% highlight py %}

DATABASES = {
	'default': {
		'ENGINE': 'django.db.backends.postgresql_psycopg2',
		'NAME': 'mydatabase',
		'USER': 'mydatabaseuser',
		'PASSWORD': 'mypassword',
		'POST': '5432'
	}
}

{% endhighlight %}

### Create an app

In the root of the project folder, create a new app to begin a new feature in
the project, in this case a **pages** feature.

{% highlight js %}

python manage.py startapp pages

{% endhighlight %}

### Adding a view

Open the **views.py** file within the newly created feature/app called **pages**
and replace everything with the following:

{% highlight py %}

from django.shortcuts import render_to_response
from django.template import RequestContext

def home(request, template = 'home.html'):
	context = {}
	return render_to_response(
		 template,
		 context,
		 context_instance = RequestConext(request)
	)

{% endhighlight %}

Inside the **templates** folder within the feature/app, create a file called
**home.html** to allow the view to point to. Add a line of text into this file
to indicate that the file can be scene once we run the website. **Hello World!**
should be good enough to start.

### Template and Installed Apps

This section must be added in to tell the website to load the **pages** app and
the **templates** folder.

Open the file **settings.py** file again and scroll down to find a variable called **TEMPLATE_DIRS**.
Replace it with the following to tell the website to poin to the **templates** folder:

{% highlight py %}

TEMPLATE_DIRS = (
	os.path.join(BASE_DIR, 'templates'),
)

{% endhighlight %}

Add a **pages** string to the **INSTALLED_APPS** like so:

{% highlight py %}

INSTALLED_APPS = (
  'django.contrib.admin',
  'django.contrib.auth',
  'django.contrib.contenttypes',
  'django.contrib.sessions',
  'django.contrib.messages',
  'django.contrib.staticfiles',
  'pages',
)

{% endhighlight %}

### URLS

Add a route to the urls in the **urls.py** file like so

{% highlight py %}

url(r'^$', 'pages.views.home', name='home'),

{% endhighlight %}

### Run and view the website

Back in the console, run the following command to start the website:

{% highlight py %}

python manage.py runserver

{% endhighlight %}

Congratulations, the website can now be viewed using the following link [http://localhost:8000](http://localhost:8000).
