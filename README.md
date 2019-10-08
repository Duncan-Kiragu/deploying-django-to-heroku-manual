# A guide for deploying to Heroku

## Info:
This guide shows a simple step by step tutorial for deploying django applications.
>Tested with django 2.2.5


## Requirements:
#### Starters:
* A heroku account
* Heroku CLI
	
#### Files needed:
* A django application
* A Procfile
* a .env file
* a runtime.txt file

#### Installations:
* gunicorn
* whitenoise
* Python dotenv
* Python-decouple
* dj-database-url
* psycopg2


# Let's setup our app :)

#### 1. Login to your heroku account
```python
	 heroku login
```

#### 2. Create a heroku application
In this case, we'll create an app called 	``picsgarage``

```python
	heroku create picsgarage
```
 Heroku apps include a `Procfile` that specifies the commands that are executed by the app’s dynos.
#### 3. Create a Procfile
```python
$ touch Procfile
```
Add the following settings to your Procfile
```python
web: gunicorn your_project_name.wsgi --log-file -
```

#### 4. Selecting a runtime
To specify a Python runtime, add a `runtime.txt` file to your app’s root 	    directory that declares the exact version number to use:
```python
nano runtime.txt
```
Add the version below to the runtime.txt file
	```python
	python-3.7.2
	```
#### 5. Whitenoise: Django Static Files settings
First configure static related parameter in `settings.py`
```python
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
# Static files (CSS, JavaScript, Images)
# https://docs.djangoproject.com/en/1.9/howto/static-files/
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATIC_URL = '/static/'

# Extra places for collectstatic to find static files.
STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)
```

Django does not support serving static files in production. However, WhiteNoise project can integrate into your Django application, and was designed with exactly this purpose in mind.
So, let's install `whitenoise`
```python
pip install whitenoise
```
Next, install `WhiteNoise` into your Django application. This is done in `settings.py’s middleware section` (at the top):
```python
    MIDDLEWARE = [  
  'whitenoise.middleware.WhiteNoiseMiddleware',  
]
```

Add the following setting to `settings.py` in the static files section to enable gzip functionality.

```python
# Simplified static file serving.
# https://warehouse.python.org/project/whitenoise/

STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
```
#### Requirements file.
If your under a virtual environment run the command below to generate the requirements.txt file which heroku will use to install python package dependencies.
```python
pip freeze > requirements.txt
```
Make sure the following are on the file
```
dj-database-url==0.4.2
Django==2.0.2
dj-static==0.0.6
gunicorn==19.7.1
psycopg2-binary==2.7.4
python-decouple==3.1
unicodecsv==0.14.1
whitenoise==3.3.1
Unipath==1.0
```
#### Adding environment variables to .env file
Create a `.env` file on your root folder by running the command,
```python 
touch .env 
``` 


Add the following to the `.env` file
```python
SECRET_KEY='309899das98yaifuwh32547lmnj3k5lb34hk6bhj5b36i'  
DEBUG=True  
DB_NAME='picsgarage'  
DB_USER='vick'  
DB_PASSWORD='p'  
DB_HOST='127.0.0.1'  
MODE='dev'  
ALLOWED_HOSTS='*'
DISABLE_COLLECTSTATIC=1
```
or an easier way
```python
SECRET_KEY='309899das98yaifuwh32547lmnj3k5lb34hk6bhj5b36i'  
DEBUG=True 
DATABASE_URL='postgres://vick:p@localhost/picsgarage'
MODE='dev'  
ALLOWED_HOSTS='*'
```

#### Install the following python packages first;
```python
pip install python-dotenv
```
```python
pip install django-heroku
```
```python
pip install python-decouple
```
```python
pip install dj-database-url
```

#### Add the following to the `settings.py` imports
```python
import os  
  
import dj_database_url  
import django_heroku  
from decouple import config, Csv  
from dotenv import load_dotenv  
  
load_dotenv()
```

#### Adding the `SECRET_KET` to the config file
```python
SECRET_KEY = config('SECRET_KEY')
```
#### Setting `DEBUG` values
```python
DEBUG = config('DEBUG', default=False, cast=bool)
```
#### `DATABASES` configurations
```python
DATABASES = {  
  'default': {  
  'ENGINE': 'django.db.backends.postgresql_psycopg2',  
  'NAME': config('DB_NAME'),  
  'USER': config('DB_USER'),  
  'PASSWORD': config('DB_PASSWORD'),  
  'HOST': config('DB_HOST'),  
  }  
}
```
or use the easy way

```python
DATABASES = {  
    'default':  dj_database_url.config(  
        default=config('DATABASE_URL')  
    )  
}
```

Then add the code below under the `DATABASES` object

```python
db_env = dj_database_url.config(conn_max_age=500)  
DATABASES['default'].update(db_env)  
ALLOWED_HOSTS = config('ALLOWED_HOSTS', cast=Csv())
```

#### Pushing config variables to heroku
```python
heroku config:set $(cat .env | sed '/^$/d; /#[[:print:]]*$/d')
```

#### Pushing your DB to heroku
Run the command below to push your local db to heroku
```
syntax ==> heroku pg:push <db_name> DATABASE_URL --app <heroku_app_name>
```
```python
heroku pg:push picsgarage DATABASE_URL --app picsgarage
```

### And that's it...Your app should now be deployed to Heroku

improved from @jakhax deployment manual
> &copy; Victor Waichigo 2019
