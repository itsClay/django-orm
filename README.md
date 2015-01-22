Django Object-Relational Mapping (ORM)
======================

###New Django Project
* Personally I use a directory simply named projects in my root folder
* I can always get there by running cd ~/projects
* Make this folder, whatever you'd like to call it, and make sure your terminal is now in that directory.

####New Django Project - Virtualenv?
What is this virtual environment thing?

* Virtual environments help us organize the python packages we're using in our project.
* Imagine you start a new project and you start using the latest version of Django.
* You're work then has you start working on an older project that is using an earlier version of Django.
* If you have the latest version of Django installed, then you can't work on the older project and vice versa.
* This is where virtualenvs help!
* It creates a 'virtual' python environment for each of our projects.
* This means we can install different python packages in each of our virtualenvs and no longer have to worry about conflicts.

####New Django Project - Create Virtualenv
Let's create our first virtualenv

* You should already have installed virtualenv and virtualenvwrapper.
* Virtualenvwrapper gives us some handy tools on our command line to create and activate our virtual environments.
* First, let's make our new virtualenv, which we'll call 'writers'.
* Run mkvirtualenv writers
* If this does not work edit your ~/.bash_profile and put this line in it source "/usr/local/bin/virtualenvwrapper.sh" then open up a new tab in your terminal.
* Now we're inside of a virtualenv! Your command line should say (writers) to show you're inside of it.
* Run deactivate, this will take you outside of your virtualenv.
* Now run workon writers to get back inside of it.

####New Django Project - Start Django
Let's get the Django project created.

* Run <code>pip install django</code>, this will install the latest version of django in your virtualenv.
* Run <code>pip install psycopg2</code>, this will install the python package which helps python talk to PostgreSQL.
* Run <code>django-admin.py startproject writers</code>, this will create our new django project files.
* We need to create the database we'll use for this project
	* Run <code>postgres -D /usr/local/var/postgres</code> if your database isn't running
	* Run <code>psql postgres</code> then <code>create database writers;</code> to create the database for our project.
* Now let's open this new project, writers, in an entirely new window in PyCharm.

####New Django Project -Local Settings
Let's hook up our database to Django.

* Under the main folder, on the same level as <code>manage.py</code>, create a new file called <code>local_settings.py</code>.
* Copy and past the below code into this file. This is telling our Django project to use the writers database we just created.

````Python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql_psycopg2',
        'NAME': 'writers',
    }
}
````
####New Django Project - Local Settings 2

* Open your <code>settings.py</code> and copy and paste the below code in at the bottom of the file. This code tells our settings file to read from our local_settings file.

````Python
try:
    from local_settings import *
except ImportError:
    pass
````

* We use a local settings file, because when we have a project on our computers for development, we want to use different settings than if it was in production. For example, we may have more secure names and passwords for our database in production.

####New Django Project -PyCharm
We're almost there! We need to tell PyCharm about the virtualenv we created that has our python package we'll be using for this project.

* Click on PyCharm -> Preferences
* Click on Project Interpreter
* Click on the Gear Icon -> Add Local
* Go to your home directory -> .virtualenvs -> writers -> bin
* Choose the file that's just called <code>python</code>
Now PyCharm knows about where our virtualenv is. That folder we just found is literally where virtualenv installs our python packages when we do <code>pip install</code>

####New Django Project - Test
We should be all set to try it out!

1. Click the green play button in the top right corner of PyCharm. This runs our local Django development web server.
2. Your console should show a link to http://127.0.0.1:8000, click on it.
3. We should get the defacto your Django website is up page!

####New Django Project -Recap
* Unfortunately, you will have to do this process every time you start a new Django project.
* Luckily, from here on out we'll be making a new Django project almost every day, so you'll have a ton of practice.
* Soon it will become second nature to go through these steps, but make sure you keep these as a reference.
* We'll be building on this setup process as we add more tools we use with Django in future lessons.


###Django ORM
The Django ORM (Object-Relational Mapping) is a python abstraction on top of PostgreSQL, which allows us to write python code, which interacts with the database.

This means, we can write python code to create tables, insert data into tables, and query tables just as we saw in yesterday's lesson.

* The ORM also does not care what kind of database we're using. We would write the same python code if the backend was sqlite or MySQL.
* We also don't have to worry about writing such complex queries. The ORM makes it easier to get data between related tables.
* Sometimes the ORM doesn't do exactly what we need or is slow, but we always have the ability to execute a raw SQL query if we'd like to.

###Django App
Let's create our first Django app in our writer's project called blog.

* Inside our writer's folder, on the same level as manage.py, let's run <code>python manage.py startapp blog</code>. This is the built in command to create a new Django app.
* Django projects are made up of multiple Django applications. Each app is supposed to focus on a small set of functionality. This helps us write modular code.
* Often times it can be a pain to try to name our project differently than our first app, but choose your names wisely cause you're usually stuck with them once a project has started!
* Next, we need to register our new app with our Django project, open <code>settings.py</code> and in <code>INSTALLED_APPS</code> add <code>'blog'</code> to the end of the list.

###Django Models
How do Django models work?

* To represent a table in our database, we create a model in Django.
* A model is just a subclass we create that inherits from a class that Django defines called <code>Model</code>
* Every field we define on the model corresponds to a column on our table.
* Our models always belong in our app's <code>models.py</code> file. Never put them anywhere else!

###Author Model
Now everything's ready for us to create our first Django model.

````Python
class Author(models.Model):
    name = models.CharField(max_length=120)
    twitter = models.CharField(max_length=40, null=True)

    def __unicode__(self):
        return u"{}".format(self.name)
````
* Copy this Author model into <code>models.py</code> inside of our blog application.
* A model is just a subclass we create that inherits from a class that Django defines called <code>Model</code>
* Every field we define on the model corresponds to a column on our table.
* Our models always belong in our app's <code>models.py</code> file. Never put them anywhere else!
* We have the same fields we saw in yesterday's lesson. We have a name field, that is a <code>CharField</code> with a <code>max_length</code> of 120.
* And we have a twitter field, that is a <code>CharField</code> with <code>max_length</code> of 40.
* This represents the same exact table we created yesterday, the auto incrementing id field as a primary key is implied in Django if you don't specify one.

###Migrate
We've got our model, now let's create the table.

* Django's ORM can handle creating database tables for us.
* Let's run <code>python manage.py makemigrations</code>. This will tell Django to look for any new models we've defined.
* Now we'll run <code>python manage.py migrate</code>, which will actually create the database tables for us. We'll learn more about this later.
* You'll notice, all of our Django specific commands are run by using <code>python manage.py</code>, if you want to see a list of all of the Django commands available, run <code>python manage.py help</code>
* Let's go to our database, <code>psql writers</code> and let's checkout our tables <code>\d</code>.
* Notice our table is called <code>blog_author</code>. This is because Django prefixes our model names with it's application name
* Let's inspect our <code>blog_author</code> table, <code>\d blog_author</code>. We can see that it looks almost exactly the same as our manually created one.

###Save Model Data
We've got a table created, let's put in some data.

````Python
from blog.models import Author

dr_seuss = Author(name='Dr. Seuss', twitter='@TheDoctor')
dr_seuss.save()

Author.objects.create(name='George R. Martin', twitter='@YourFavoriteCharactersDead')
Author.objects.create(name='Mark Twain')
````

* First let's open up Django's shell, <code>python manage.py shell</code>. This is like the python interpreter, but with all of Django's functionality loaded in.
* Now we can run the above commands. This creates a new Author object. These python instances of our Author model class can also be saved, which save to our table in the database.
* This second way, is a shortcut to quickly make a new Author object and save it at the same time.
* Let's go to our database, <code>psql writers</code> and let's quickly run a select query just to see that our new data is there.

###Querying Model Data
We've got a model with some data populated, let's see how to query it.

````Python
from blog.models import Author

all_authors = Author.objects.all()
for author in all_authors:
    print author.name, author.twitter
````

* The first line is the Django model equivalent to <code>SELECt * FROM author;</code>. It should return all of the authors in the all_authors variable.
* This variable is a Django <code>Queryset</code>, which is iterable.
* We can loop over our <code>all_authors</code> list and print each author object out.
* We access the table column values just as we would access the fields of a regular python class.

We've got a model with some data populated, let's see how to query it.

````Python
from blog.models import Author

dr_seuss = Author.objects.get(pk=1)

# List of all authors besides Dr. Seuss
authors = Author.objects.filter(pk__gt=1)
````
* We can get a single object by using the <code>get</code> method and specifying what primary key we're looking for.
* We can look for a list of objects by using the <code>filter</code> method and specifying conditional statements using any of this table's fields.
* Look at the [Django documentation] for a list of all the query types.
* These are running the same types of queries we did yesterday in the database.

###Model Update and Delete
Let's see how we would update or delete using models.

````Python
from blog.models import Author

# Get the author
author = Author.objects.get(pk=4)

# Change and save their twitter handle
author.twitter = '@NewHandle'
author.save()

# Delete the author
author.delete()

# Now throws an error
author = Author.objects.get(pk=4)
````

* First we get the author, then we're able to change any fields on the model, then save it back to the database.
* Next, all we have to do is call the model's <code>delete</code> method to remove it from the db.
* When we try to get the author again, Django gives us an error, because it no longer exists.

###Model Ordering and Limiting
We can also order and limit our queries, just like we did yesterday.

````Python
from blog.models import Author

alphabetical_authors = Author.objects.order_by('name')
reverse_authors = Author.objects.order_by('-name')

authors = Author.objects.all()[:1]
````
* Our model has a built in method called <code>order_by</code>, which we can use at the end of any queryset. It expects the name(s) of the fields to order by.
* By default, it orders by ascending. If we want to order by descending, we add a - in front of the column name.
* To limit a queryset, we use python's list splicing functionality, since the queryset is ultimately just a list. This gets us just one result back.


##Bite Sized Mission
Let's practice a bit of the basics we just learned with Django models

* Get a list of all authors whose name is 'Dr. Seuss'.
* Get a list of all authors ordered alphabetically by their twitter handle
* Get a list of all authors ordered by their primary key, descending limited to 2 results.


[Django Documentation]: https://docs.djangoproject.com/en/1.6/ref/models/querysets/#field-lookups