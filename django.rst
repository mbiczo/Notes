Django Project/App Quick Reference
==================================

u:admin

p:Don'tForget!

First Steps
-----------
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

Create url to define index **within <appname>** ``urls.py``
	add index to ``urlpatterns`` in ``urls.py``

Create url to point to page **within <myproject>** ``urls.py``
	add url path and be sure to include() the respective apps urls script
	
URLs - Setting the path
''''''''''''''''''''''''
After adding a view to the ``<app>``, you must update the ``urlpatterns`` variable within the ``urls.py`` file.

.. code:: python

	from django.conf.urls import url
	
	from . import views
	
	urlpatterns = [
	    #ex: /polls/
	    url(r'^$', views.index, name='index'),
	
	    #ex: /polls/5/
	    url(r'^(?P<question_id>[0-9]+)/$', views.detail, name='detail'),
	
	    #ex: /polls/5/results/
	    url(r'^(?P<question_id>[0-9]+)/results/$', views.results, name='results')
	]
	
Raising a 404 error
'''''''''''''''''''
To raise a 404 error on a view add something similar to the ``views.py`` file as follows:

**Long way**

.. code:: python
	
	from django.http import Http404
	
	# Create your views here.
	def detail(request, question_id):
		try:
			question = Question.objects.get(pk=question_id)
		except Question.DoesNotExist:
			raise Http404("Question does not exist")
		return render(request, 'polls/detail.html', {'question': question})
		
**Using Django Shortcuts**

.. code:: python

	from django.shortcuts import get_object_or_404
	
	# Create views here
	question = get_object_or_404(Question, pk= question_id)
	return render(request, 'polls/details.html', {'question': question})
	


Models - Working with Data
--------------------------
Define models within ``<appname>.models.py``

Install app by editing the ``settings.py`` ``INSTALLED_APPS``
	``<appname>.apps.PollsConfig``

Store data model changes by creating a migration
	``$ python manage.py makemigrations <appname>``

Validate and View SQL code for migration
	``$ python manage.py sqlmigrate <appname> 0001`` (or whatever id)

Commit model changes
	``$ python manage.py migrate``


Templates - What users see
--------------------------
It's best practices to separate the page content code from the view functionality by creating a template for the view to load or reference.

First, add a template folder to the ``<appname>`` directory called ``templates``.  Django will automatically reference this under the hood.  Within the ``templates`` dir, create another dir with the ``<appname>`` and put any and all templates there - in this case.

*Make sure the new template is added/updated in the ``<appname>/views.py`` script*

Create the initial ``index.html`` file and any other pages you'd like!  Here's an example:

.. code:: django

	{% if latest_question_list %}
	    <ul>
	    {% for question in latest_question_list %}
	        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
	    {% endfor %}
	    </ul>
	{% else %}
	    <p>No polls are available.</p>
	{% endif %}

...more on this later...

See the `template guide`_ - for more about templates.

.. _template guide: https://docs.djangoproject.com/en/1.9/topics/templates/



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
	

Forms
-----

Basic example of a form being added to ``detail.html``

.. code:: django

	<form action= "{% url 'polls:vote' question.id %}" method= "post">
	
	<!-- Prevent Cross Site Request Forgeries -->
	{% csrf_token %}
	
	{% for choice in question.choice_set.all %}
		
		<input type "radio" name= "choice" id= "choice{{ forloop.counter }}" value= "{{ choice.id }}" />
	
		<label for= "choice{{ forloop.counter }}">{{ choice.choice_text }}</label>
	
		<br/>
	
	{% endfor %}
		
		<input type= "submit" value= "Vote" />
	</form>


