Django Project/App Quick Reference
==================================

This document contains a loose workflow as well as reference notes to create a basic Django project and application.  The following examples are based off ``Django 1.9.4``.  Also, these scripts have neither been extensively tested, nor claim to show best practices.  This is a work in progress.  The following was also ran under a ``virtualenv``, and assume proper installation.


First Steps
-----------

**Create Project Shell**

``django-admin startproject <myproject>``

**Create App Shell**

``python manage.py startapp <appname>``

**Run Dev Server**

``python manage.py runserver``
	
**Invoke Python (Django) interactive shell**

``python manage.py shell``


Views - What users do
---------------------
Each view is responsible for doing one of two things: returning an HttpResponse object containing the content for the requested page, or raising an exception such as Http404. The rest is up to you - Django docs


**Create first View** (contains page content for now)

edit the views.py script by defining a function returning an HttpResponse()

**Create url to define index within** ``<appname>`` ``urls.py``

add index to ``urlpatterns`` in ``urls.py``

**Create url to point to page within** ``<myproject>`` ``urls.py``

add url path and be sure to include() the respective apps urls script
	

Generic Views
'''''''''''''
Generic views abstract common patterns to the point where you don’t even need to write Python code to write an app.

First add generic view to ``<appname>/urls.py`` as such:

.. code:: python

	from django.conf.urls import url

	from . import views

	# assign app namespace
	app_name = 'polls'
	
	urlpatterns = [
	    # add views.IndexView here
	    url(r'^$', views.IndexView.as_view(), name='index'),
	]

Next, update the ``views.py`` file in your <appname> directory as such:

.. code:: python

	class IndexView(generic.ListView):
		template_name = 'polls/index.html'
    		context_object_name = 'latest_question_list'
		def get_queryset(self):
		        '''
		        Return the last five published questions.
		        '''
		        return Question.objects.order_by('-pub_date')[:5]


	
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

**Install app by editing the** ``settings.py`` ``INSTALLED_APPS``

``<appname>.apps.PollsConfig``

**Store data model changes by creating a migration**

``python manage.py makemigrations <appname>``

**Validate and View SQL code for migration**

``python manage.py sqlmigrate <appname> 0001`` (or whatever id)

**Commit model changes**

``python manage.py migrate``


Templates - What users see
--------------------------
It's best practices to separate the page content code from the view functionality by creating a template for the view to load or reference.

First, add a template folder to the ``<appname>`` directory called ``templates``.  Django will automatically reference this under the hood.  Within the ``templates`` dir, create another dir with the ``<appname>`` and put any and all templates there - in this case.

*Make sure the new template is added/updated in the* ``<appname>/views.py`` *script*

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


Static Files
''''''''''''

Aside from the HTML generated by the server, web applications generally need to serve additional files — such as images, JavaScript, or CSS — necessary to render the complete web page. In Django, we refer to these files as “static files”.

First, create a directory called ``static`` in your ``application`` directory such as ``polls/static/polls``.  Django will automatically pick up this location.

**CSS**

Add necessary files such as ``.css``, for example, to the newly creatd subfolder.  Be sure to update your ``.html`` file to include these files as follows:

.. code:: django

	{% load staticfiles %}
	
	<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
	
**Images**

Create another sub folder called ``images`` inside ``application/static/application/`` and place desired images.  Don't forget to add the new reference to your ``.css`` file.

TODO: add example



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
	
**Modifying Admin Page**

There are several things you can do to customize the Admin look and feel.  For all features, visit the oficial Django documentation regarding `The Django admin site`_  For now, here is an example of a ``admin.py`` file with a few implementations:

.. _The Django admin site: https://docs.djangoproject.com/en/1.9/ref/contrib/admin/

.. code:: python

	from django.contrib import admin
	from .models import Choice, Question
	
	# Register your models here.
	class ChoiceInline(admin.TabularInline):  # use StackedInline to stack items
	    model = Choice
	    extra = 3
	
	class QuestionAdmin(admin.ModelAdmin):
	    # For many fields, use fieldsets
	    fieldsets = [
	            (None,                  {'fields': ['question_text']}),
	            ('Date information',    {'fields': ['pub_date']}),
	    ]
	
	    # Tell Django that Choice objects are edited on the Question admin page.
	    inlines = [ChoiceInline]
	
	    # Tell Django to include individual fields on page
	    list_display = ('question_text', 'pub_date', 'was_published_recently')
	
	    # Tell Django to add a 'filter' option sidebar
	    list_filter = ['pub_date']
	
	    # Add search capability
	    search_fields = ['question_text']
	
	admin.site.register(Question, QuestionAdmin)
	

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


**NOTE - Always return an HttpResponseRedirect after successfully dealing with POST data.  This prevents data from being posted twice if the user hits the Back button.**

**NOTE - If 2 users pull the same data to update, there may be write conflicts.  In web development, this is called race conditions.  In order to these, visit the page on** `using F() expressions in queries`_.


.. _using F() expressions in queries: https://docs.djangoproject.com/en/1.9/topics/db/queries/#using-f-expressions-in-filters


Further Reading
'''''''''''''''

This is the end of the basics.  For more in-depth documentation on specific topics, visit the `Django Topics page`_.

.. _Django Topics page: https://docs.djangoproject.com/en/1.9/topics/





