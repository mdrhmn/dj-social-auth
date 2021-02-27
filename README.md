# Django OAuth 2.0 Google Authentication
This is a sample project to explore social (Google) OAuth 2.0 authentication integration with Django using [`django-allauth`](https://django-allauth.readthedocs.io/en/latest/index.html).

## Getting Started with Django

### 1. Set up your Django project

If you are **cloning this repo**, run the following command preferably inside your virtual environment to install all dependencies:

- Using **venv**:
    ```Shell
    $ pip install -r requirements.txt # (Python 2)
    $ pip3 install -r requirements.txt # (Python 3)
    ``` 

Else, to **create your Django project** from scratch (make sure to have Django installed):

```Shell
$ django-admin startproject project_name
``` 

And then create a virtual environment (highly recommended):
- To **create** virtual environment:
    ```Shell
    $ python3 -m venv env_name # using Python's venv
    # or
    $ virtualenv env_name # using virtualenv
    ``` 
- To **activate** virtual environment (Linux/Mac OS):
    ```Shell
    $ source env_name/bin/activate
    ``` 

- Install all dependencies:
    ```Shell
    $ pip install -r requirements.txt # (Python 2)
    $ pip3 install -r requirements.txt # (Python 3)
    ``` 

Next, **navigate** into the newly created project folder. Then, **start a new Django app**. We will also **run migrations** and **start up the server**:

```Shell
$ cd project_name
$ python manage.py startapp app_name
$ python manage.py migrate
$ python manage.py runserver
``` 

If everything works well, we should see an instance of a Django application running on this address — http://localhost:8000

![alt text](https://scotch-res.cloudinary.com/image/upload/v1542486456/ia8jlkozut4uxwatnqwp.png)

<br>

### 2. Configure project settings

1. Add app inside INSTALLED_APPS (`settings.py`)

    Once you’ve created the app, you need to install it in your project. In `project_name/settings.py`, add the following line of code under `INSTALLED_APPS`:

    ```python
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'app_name',
    ]
    ```

    That line of code means that your project now knows that the app you just created exists.

2. Add templates folder directory in TEMPLATES  (`project_name/settings.py`)

    ```python
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': ['templates/'], # HERE
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
            },
        },
    ]
    ```

2. Add static and media folder directory in STATIC_ROOT  (`project_name/settings.py`)

    ```python
    STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
    MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
    ```

3. Add desired URL for the app (`project_name/urls.py`)

    ```python
    from django.contrib import admin
    from django.urls import path, include

    urlpatterns = [
        path('admin/', admin.site.urls),
        path('(INSERT_URL)', include('APP_NAME.urls')),
    ]
    ```

4. Create new `urls.py` for the app (`app_name/urls.py`)

<br>

### 3. Configure app settings

1. **Create new template (**`app_name/templates/`**)**
    - You need to create the HTML template to display to the user after creating a view function.
    - Create a directory named **templates a**nd subsequently a file named `app_name.html` inside it:

        ```python
        # Create directory (if haven't)
        mkdir app_name/templates/

        # Create HTML template
        touch app_name/templates/app_name.html
        ```

2. **Create view function (FBV/CBV) in app's `views.py`**
    - Views in Django are a **collection of functions or classes** inside the `views.py` file in your app directory.
    - Each function or class handles the logic that gets processed each time a different URL is visited.

        ```python
        from django.shortcuts import render

        def view_name(request):
            return render(request, 'template_name.html', {})
        ```

    - The function defined is called a **view function**. When this function is called, it will render an HTML file called `app_name.html`.

3. **Add URL to app's `urls.py`**
    - The final step is to hook up your URLs so that you can visit the page you’ve just created.
    - Your project has a module called `urls.py` in which you need to include a URL configuration for the app. Inside `app_name/urls.py`, add the following:

        ```python
        from django.contrib import admin
        from django.urls import path, include

        urlpatterns = [
            path('', views.view_name, name="view_name"),
        ]
        ```

        - This looks for a module called `urls.py` inside the application and registers any URLs defined there.
        - Whenever you visit the root path of your URL (localhost:8000), the application’s URLs will be registered.

<hr>


## Getting Started with django-allauth

## What is django-allauth?

`django-allauth` is an **integrated set of Django applications** addressing **authentication, registration, account management** as well as **3rd party (social) account authentication**. It offers  a fully integrated authentication app that allows for both local and social authentication, with flows that just work.

### Installing django-allauth

To install `django-allauth`:
```Shell
    $ pip install django-allauth # (Python 2)
    $ pip3 install django-allauth # (Python 3)
``` 

### Configuring django-allauth

#### settings.py

After installing the package, register `django-allauth` by adding it to `INSTALLED_APPS` in `settings.py`. You can include the providers you want to enable (refer [here](https://django-allauth.readthedocs.io/en/latest/installation.html)):

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'django.contrib.sites', # Add this

    # Add the following django-allauth apps
    'allauth',
    'allauth.account',
    'allauth.socialaccount',
    'allauth.socialaccount.providers.google', # for Google OAuth 2.0
    # ...
]
```

At the bottom of `settings.py`, we also need to specify the allauth backend and configuration settings:

```python
AUTHENTICATION_BACKENDS = [
    'django.contrib.auth.backends.ModelBackend',
    'allauth.account.auth_backends.AuthenticationBackend'
]
```

Continue to add `SITE_ID` and specify redirect URL upon successful Google login. You can also add additional configuration settings as shown below:

```python
SITE_ID = 1
LOGIN_REDIRECT_URL = '/'

# Additional configuration settings
SOCIALACCOUNT_QUERY_EMAIL = True
ACCOUNT_LOGOUT_ON_GET= True
ACCOUNT_UNIQUE_EMAIL = True
ACCOUNT_EMAIL_REQUIRED = True
```

Finally, enable email scope to receive user’s email addresses after successful social login:

```python
SOCIALACCOUNT_PROVIDERS = {
    'google': {
        'SCOPE': [
            'profile',
            'email',
        ],
        'AUTH_PARAMS': {
            'access_type': 'online',
        }
    }
}
```

#### urls.py

Go to `urls.py` file of your project directory and add the allauth urls and specify `include` on top of import. We add a route /accounts that includes all django-allauth URLs. All OAuth operations will be performed under this route. 

```python
SOCIALACCOUNT_PROVIDERS = {
    'google': {
        'SCOPE': [
            'profile',
            'email',
        ],
        'AUTH_PARAMS': {
            'access_type': 'online',
        }
    }
}
```

<hr>

## Configuring Google APIs

To add Google login on your app, you’ll need to set up OAuth application via [**Google Developers Console**](https://console.developers.google.com/).

Head over to Google Developer APIs Console and **create a new project**:

![Imgur Image](https://www.section.io/engineering-education/django-google-oauth/create-g-oauth-project.jpg)

