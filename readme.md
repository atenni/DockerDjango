DockerDjango
============

This repo contains a base `docker-compose` configuration for a generic Django
app.


Quick start
-----------

Create your `secrets.env` file and place your app within ./django/app_dir/ (see below). Then, depending on if you're _developing_ or sending to _production_...

**Development** (note: reads in `docker-compose.override.yml`):

```
docker-compose up

# App will be accessible at <your docker machine's IP>:8080
```

**Production** (see Docker: [multiple compose files][compose-doco]):

```
docker-compose -f docker-compose.yml -f docker-compose.prod.yml up

# App will be accessible at <your docker machine's IP>:80 
```



Usage
-----

Source code for the Django app should live within the `django/app_dir/` folder
and include a `requirements.txt` in the top level directory (see also
"Environment" below). More explicitly:

```
├── DockerDjango
│   ├── django
│   │   ├── app_dir
│   │   │   ├── django_project/    # Django project folder
│   │   │   ├── manage.py          # manage.py file here
│   │   │   └── requirements.txt   # requirements.txt file here
│   │   └── Dockerfile
│   ├── docker-compose.yml
│   │
```

Passwords and configuration are via environment variables (as per
[Twelve-Factor App](http://12factor.net/config)) and should be stored in
`secrets.env`. You can use `secrets.env.example` as a template to see which
variables are expected (see also "Django settings" below).

After the `app` service is built for the first time the following command
needs to be run to populate database tables:

```bash
docker-compose run web /usr/local/bin/python manage.py migrate
```

[compose-doco]: https://docs.docker.com/compose/extends/#different-environments


Environment
-----------

* Python 3.5.x
* Django 1.9.x
* Postgres 9.5.x
* Nginx 1.9.x

Note: This implies that your `requirements.txt` file should *at least* include:

```
Django >=1.9, <=1.9.99
psycopg2 ~= 2.6
```


Django settings
---------------

Sensitive and environments specific data are stored in environment variables
and not in your version controlled source. As far as `settings.py` is
concerned, these are the ones you'll be interested in:

```python
import os

DEBUG = os.environ.get('DJANGO_DEBUG')
TEMPLATE_DEBUG = DEBUG

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': os.environ.get('POSTGRES_DB'),
        'USER': os.environ.get('POSTGRES_USER'),
        'PASSWORD': os.environ.get('POSTGRES_PASSWORD'),
        'HOST': 'db',    # Via docker-compose linking
        'PORT': '5432',
    }
}

SECRET_KEY = os.environ.get('DJANGO_SECRET_KEY')
```


TODOs
-----

- Run app via `gunicorn` or `uWSGI`
- Include a data volume for static files
- Include a SASS service to compile `.scss` files to `.css`
- Use `django-environ` to simplify secrets.env
- Include `startup.sh` script in django image that checks if an environment
  has been created and if not runs `python manage.py migrate`.

