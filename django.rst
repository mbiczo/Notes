Django Project/App Quick Reference
==================================

u:admin

p:Don'tForget!


Begin
-----
**Create Project Shell**
	``$ django-admin startproject <myproject>``

**Create App Shell**
	``$ python manage.py startapp <appname>``

**Run Dev Server**
	``$ python manage.py runserver``
	
**Invoke Python (Django) interactive shell**
	``$ python manage.py shell``


Views - What users do
---------------------
*Each view is responsible for doing one of two things: returning an HttpResponse object containing the content for the requested page, or raising an exception such as Http404. The rest is up to you - Django docs*


Create first View (contains page content for now)
	edit the views.py script by defining a function returning an HttpResponse()

Create url to define index **within <appname>** urls.py
	add index to *urlpatterns* in urls.py

Create url to point to page **within <myproject>** urls.py
	add url path and be sure to include() the respective apps urls script

Models - Working with Data
--------------------------
Define models within <appname>.models.py

Install app by editing the settings.py INSTALLED_APPS
	<appname>.apps.PollsConfig

Store data model changes by creating a migration
	``$ python manage.py makemigrations <appname>``

Validate and View SQL code for migration
	``$ python manage.py sqlmigrate <appname> 0001`` (or whatever id)

Commit model changes
	``$ python manage.py migrate``


Templates - What users see
-------------------------
It's best practices to separate the page content code from the view functionality by creating a template for the view to load or reference.

First, add a template folder to the <appname> directory called *templates*.  Django will automatically reference this under the hood.  Within the *templates* dir, create another dir with the <appname> and put any and all templates there - in this case.

Create the initial index.html and any other pages you'd like!


Admin
-----

**Create superuser** (for admin purposes).  You will be prompted for a username, e-mail and pwd.
	``$ python manage.py createsuperuser``
	
**Make items editable in the admin**

Must register the model within the ``<appname>/admin.py`` script as follows:
	
.. code:: python

	from django.contrib import admin
	from .models import Question
	  
	admin.site.register(Question)	

