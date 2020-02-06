# Django Checklist

*NOTE*: Use your own variable names throughout this!!! Also, don't add `my` onto your variables! That's just for the cheat sheet!

## Setup

### Install all dependencies and created our virtual environment
- [ ] Make a directory for the project
- [ ] Create a virtual environment `pip3 install virtualenv`
- [ ] `python3 venv .env` - .env can be named anything you want.
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


## Additional Resources
* [GA Lesson](https://git.generalassemb.ly/seir-1118/django-models)
* [Django Admin Documentation](https://docs.djangoproject.com/en/2.1/ref/django-admin/)
* [Django Models Documentation](https://docs.djangoproject.com/en/2.1/topics/db/models/)
* [Django Built-In Model Field Types](https://docs.djangoproject.com/en/2.1/ref/models/fields/#model-field-types)
* [Django Migration Documentation](https://docs.djangoproject.com/en/2.1/topics/migrations/)
* [Django Seed Data](https://docs.djangoproject.com/en/2.1/howto/initial-data/)
