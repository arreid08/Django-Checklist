# Django Checklist

*NOTE*: Use your own variable names throughout this!!! Also, don't add `my` onto your variables! That's just for the cheat sheet!

## Setup

### Install all dependencies and created our virtual environment
- [ ] Make a directory for the project
- [ ] Create a virtual environment `pip3 install virtualenv`
- [ ] `python3 -m venv .env` - .env can be named anything you want.
- [ ] `source .env/bin/activate` - this will activate the virtual environment
- [ ] Don't forget to active each time you work on your project!
- [ ] You should see your environment name in the line of your terminal (i.e. .env)
- [ ] Install Django `pip install django`
- [ ] Install the library for connection Django to PostgreSQL`pip install psycopg2-binary`

### Start the Django Project
- [ ] `django-admin startproject tunr_django .` - the . on the end will create the project without creating a subfolder. 
- [ ] Create our app: `django-admin startapp tunr` (tunr should be changed to the name of your app)

## Create Database

- [ ] `psql -d postgres` - to enter PostgreSQL
- [ ] Create a database: `CREATE DATATBASE _____;` (fill in the blank with your database name)
- [ ] Create a dummy login: `CREATE USER ______ WITH PASSWORD '____';`  (fill in the blanks with your username and password)
- [ ] Grant privileges to your user: `GRANT ALL PRIVILEGES ON DATABASE _____ TO ____;`  (the first blank is your database name, the second is your username)
- [ ] You can now exit psql: `\q`
- [ ] Since you are using PostgreSQL, you will need update your settings.py file.  Fill in the blanks accordingly
```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': '____',
        'USER': '____',
        'PASSWORD': '____',
        'HOST': 'localhost'
    }
}
```
- [ ] Finally at the end of the INSTALLED_APPS section, add a new line with your app name.
- [ ] Back in the terminal run: `python3 manage.py runserver`
- [ ] Navigate in your browser to: `localhost:8000` to see the Django welcome page

## Define a Model

- [ ] Within your file structure, you should see a file called `models.py`.  Below is an example model.
```python
class Artist(models.Model):
    name = models.CharField(max_length=100)
    nationality = models.CharField(max_length=100)
    photo_url = models.TextField()

    def __str__(self):
        return self.name
```

## Migrate your Model

- [ ] In your terminal type: `python manage.py makemigrations`.  This will generate a file that gets all of it's data from the code in the `models.py` file.  The new file will be called `0001_initial.py`
- [ ] Every time you make changes to the `models.py` file, you will need to run the above command.
- [ ] Once you are done with your changes, you will run: `python manage.py migrate`
- [ ] You can check that your database now shows your new tables.

## Create a SuperUser for the Admin Console

- [ ] In your terminal: `python manage.py createsuperuser`
- [ ] You will be prompted to create a username and password.  You want this to be a real login for the superuser so take note.
- [ ] In the `admin.py` file, you can now add the admin info
```python
    from django.contrib import admin
    from .models import Artist

    admin.site.register(Artist)
```
- [ ] Test the work in your browser: `localhost:8000/admin`
- [ ] You should have access to everything!

## Django Extensions

- [ ] In your Terminal `pip install django-extensions`
- [ ] Add `django_extensions` to your `INSTALLED_APPS` list:
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'tunr',
    'django_extensions'
]
```
- [ ] Now you can run `python manage.py shell_plus` to get the python shell
- [ ] Install ipython to create a nicer interface: `pip install ipython`
- [ ] You can now run the python shell to test and run within your terminal: `python manage.py shell_plus --ipython`
- [ ] Example queries/inputs:
```bash
# Select all of the artist objects in the database
Artist.objects.all()

# Select All Objects and Print All Values
Artist.objects.all().values_list()

# Select All Objects and Print Specific Value
Artist.objects.all().values_list('nationality')

# Get the artist with the id of 1 (can also do pk here which stands for primary key)
Artist.objects.get(id=1)

# Get the artist with the name "Kanye West", if there are two Kanye's this will error!
Artist.objects.get(name="Kanye West")

# Get all the Artists who are from the USA
Artist.objects.filter(nationality="USA")

# Store an artist in a variable for later access:
p = Artist.objects.find(name="Prince")

# Now you can look up the artist's songs:
p.songs.all()
p.songs.all().values_list()

# Create an Artist with the following attributes, then save, commiting it to the database
kanye = Artist(name="Kane West", photo_url="test.com", nationality="USA")
kanye.save()

# Oops, we misspelled Kanye's name! Let's change it and then commit to the DB
kanye.name = "Kanye West"
kanye.save()

# Let's add a song to the artist
song = Song(title="Ultralight Beam", album="The Life of Pablo", preview_url="test.com", artist=kanye)
song.save()

# Delete the song
song.delete()
```
- [ ] Check out more neat stuff you can do with `shell_plus` [here](https://django-extensions.readthedocs.io/en/latest/shell_plus.html)

## Creating a View
- [ ] In `views.py` add a view to pass data to our templates
- [ ] Here we are creating a new view called artist_list.  This list will return all the artists.  In the render, you can see we are reference an HTML file and passing the values of artists to the HTML file.  We will create the HTML file in a minute.
```python
from django.shortcuts import render
from .models import Artist, Song

def artist_list(request):
    artists = Artist.objects.all()
    return render(request, 'tunr/artist_list.html', {'artists': artists})
```

## Adding a URL
- [ ] Next we will create a URL to access our view through.  In `urls.py` add the following.  Here you can see the connection to the urls file and this will render at localhost:8000.  the last line will render at localhost:8000/songs/.
```python
from django.conf.urls import include
from django.urls import path
from django.contrib import admin

urlpatterns = [
    path('admin', admin.site.urls),
    path('', include('tunr.urls')),
    path('songs/', admin.site.urls)
]
```

## Creating the Template
- [ ] In the app folder, create a new folder called templates.  within that folder create another folder called your app name. Finally, create an HTML file matching your above view name.  Per the example referenced here: `artist_list.html`
- [ ] Within this file, create html to show your info.
```html
<ul>
    {% for artist in artists %}
        <li>
            {{ artist.name }}
        </li>
    {% endfor %}
</ul>
```
- [ ] You can now run the server and navigate to any path you defined.
- [ ] Then run your server to see your changes: `python manage.py runserver`

## Base HTML
- [ ] Here you can create a `base.html` file that will hold you basic page layout and you will then insert each template into that base as you navigate around the webpage.  This file will also be stored in the `templates` folder.
- [ ] If you create a CSS file, you will link it with the `{% load static %}` and the typical `<link>` tag you see below.
- [ ] the `{% block content %}{% endblock %}` line is where your other templates will be inserted once they are activated through url.  
```HTML
{% load static %}
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=>, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Tunr</title>
    <link rel="stylesheet" href="{% static 'css/tunr.css' %}" />
</head>
<body>
    <h1>Tun.r</h1>
    <nav>
        <a href="/songs">Songs</a>
        <a href="/">Artists</a>
    </nav>
    {% block content %}{% endblock %}
</body>
</html>
```
- [ ] In each of your template files, you will need to state that you are adding that block. Note the first two lines and the last line.  the word 'content' can be anything you want but just had to match from base to templates.
```HTML
{% extends 'tunr/base.html' %} 
{% block content %}
<h1>New Song</h1>
<form method="POST" class="song-form">
    {% csrf_token %} {{ form.as_p }}
    <button type="submit" class="save btn btn-default">Save</button>
</form>
{% endblock %}
```

## CRUD
### Create
#### Form
- [ ] First you will add to the `form.py` file.  Below you can see we pull in the models for each model and are calling the `Meta` class inside of another class.  The `Meta` class is required.  This code is used to verify the form was correctly input by the user.
```python
from django import forms
from .models import Artist, Song

class ArtistForm(forms.ModelForm):

    class Meta:
        model = Artist
        fields = ('name', 'photo_url', 'nationality',)
```

#### Views
- [ ] In the `views.py` file you will add a new function for create.  The below method will recognize that you are POSTing and then validate the form based on the info in the `form.py` file.  Then it will save the new artist for you.  Finally it will redirect you to the artist detail page based on the id(pk is primary key so as not to interfere with id) of that artist.
- [ ] Be sure to add redirect at the top.
```python
from django.shortcuts import render, redirect
from .forms import ArtistForm
def artist_create(request):
    if request.method == 'POST':
        form = ArtistForm(request.POST)
        if form.is_valid():
            artist = form.save()
            return redirect('artist_detail', pk=artist.pk)
    else:
        form = ArtistForm()
    return render(request, 'tunr/artist_form.html', {'form': form})
```

#### URL
- [ ] In the `urls.py` file we will add another new line for the new path.  As you can see, you create the path you want for creating a new artist, link it to the function you created above in the `views.py` file and finally name it.
```python
path('artists/new', views.artist_create, name='artist_create'),
```

#### Template
- [ ] Create a new HTML file for the form.  Named `artist_form.html` for this example.
- [ ] the `csrf_token` is what brings in the form. and the `form.as_p` wraps everything in a paragraph tag.
- [ ] You can now go to your list page to update/create a link to this new page.
```HTML
{% extends 'tunr/base.html' %} {% block content %}
<h1>New Artist</h1>
<form method="POST" class="artist-form">
  {% csrf_token %} {{ form.as_p }}
  <button type="submit" class="save btn btn-default">Save</button>
</form>
{% endblock %}
```

### Edit
- [ ] Edit is very similar to create with a few simple differences.

#### View
- [ ] `view.py` As you can see below there is only a little bit of changes.
- [ ] The first difference here is that we are adding in our primary key so we only edit the one we want. 
- [ ] Next we are calling out an instance.  this is again so we are working on that specific artist and not all. 
```python
def artist_edit(request, pk):
    artist = Artist.objects.get(pk=pk)
    if request.method == "POST":
        form = ArtistForm(request.POST, instance=artist)
        if form.is_valid():
            artist = form.save()
            return redirect('artist_detail', pk=artist.pk)
    else:
        form = ArtistForm(instance=artist)
    return render(request, 'tunr/artist_form.html', {'form': form})
```
#### URL
- [ ] Here is an example of the edit url for the `urls.py` file.
```python
path('artists/<int:pk>/edit', views.artist_edit, name='artist_edit'),
```

#### Template
- [ ] again, you only need to update the HTML if you want it to look differently

### Delete
#### View
- [ ] `view.py` here is an example for delete.
```python
def artist_delete(request, pk):
    Artist.objects.get(id=pk).delete()
    return redirect('artist_list')
```

#### URL
- [ ] Here is an example of the delete url for the `urls.py` file.
```python
path('artists/<int:pk>/delete', views.artist_delete, name='artist_delete'),
```



## Additional Resources
* [GA Lesson](https://git.generalassemb.ly/seir-1118/django-models)
* [Django Admin Documentation](https://docs.djangoproject.com/en/2.1/ref/django-admin/)
* [Django Models Documentation](https://docs.djangoproject.com/en/2.1/topics/db/models/)
* [Django Built-In Model Field Types](https://docs.djangoproject.com/en/2.1/ref/models/fields/#model-field-types)
* [Django Migration Documentation](https://docs.djangoproject.com/en/2.1/topics/migrations/)
* [Django Seed Data](https://docs.djangoproject.com/en/2.1/howto/initial-data/)
* [Django Model Meta Options](https://docs.djangoproject.com/en/1.11/ref/models/options/)