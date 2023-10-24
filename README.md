# Advanced-Django

django: Django is a free and open-source, Python-based web framework that follows the model–template–views architectural pattern.

## Contents

- [Advanced Django](#advanced-django)
    - [Form submission without reload](https://github.com/Parvez49/Advanced_Django)
    - [Django Rest API](https://github.com/Parvez49/Advanced_Django)
    - [Model](#Model)
    - [Query](#Query)
        - [select related and prefetch_related](#Select-Related-and-Prefetch-Related)
    - [Form](#Form)
    - [Social Authentication](#Social-Authentication)
    - [Gmail Validation](#Gmail-Validation)
    - [Gmail Send](#Gmail-Send)
    - [Cookie](#Cookie)
    - [Session](#Session)
    - [Caching](#Caching)
        - [MemcachedCache](#MemcachedCache)
        - [RedisCache](#RedisCache)
    - [Celery](#Celery)
    - [Json Web Token](#JWT)
    - [Databases](#Databases)
        - [MySQL](#Django-MySQL) 
        - [PostgreSQL](#Django-PostgreSQL)
        - [Redis](#Redis)
    - [RequirementFile](#Requirement.txt)
    - [pip command](#PIP-Command)
    - [Git](#Git)
    - [Interview Question](#Interview-Question)
 


## Admin interface

## Model
```
from django.db import models

class ExampleModel(models.Model):
    auto_field = models.AutoField(primary_key=True)
    big_auto_field = models.BigAutoField(primary_key=True)
    binary_field = models.BinaryField(max_length=255)
    boolean_field = models.BooleanField(default=True, null=True, blank=True)
    char_field = models.CharField(max_length=100, null=True, blank=True, choices=[('option1', 'Option 1'), ('option2', 'Option 2')])
    date_field = models.DateField(auto_now=True, auto_now_add=True, null=True, blank=True)
// auto_now: automatically set to the current date every time the object is saved.
// auto_now_add: automatically set to the current date when the object is first created.
    datetime_field = models.DateTimeField(auto_now=True, auto_now_add=True, null=True, blank=True)
    decimal_field = models.DecimalField(max_digits=10, decimal_places=2) // 567.89 (2 digits after the decimal point)
    duration_field = models.DurationField()
    email_field = models.EmailField(max_length=254, null=True, blank=True)
    file_field = models.FileField(upload_to='files/', max_length=100) // max_length: The maximum length of the file path.
    float_field = models.FloatField(null=True, blank=True, default=0.0) // we cannot set the decimal_places attribute in a FloatField in Django.
    image_field = models.ImageField(upload_to='images/', max_length=100)
    integer_field = models.IntegerField(null=True, blank=True, default=0)
    positive_integer_field = models.PositiveIntegerField(null=True, blank=True, default=0)
    positive_small_integer_field = models.PositiveSmallIntegerField(null=True, blank=True, default=0)
    slug_field = models.SlugField(max_length=100, null=True, blank=True)
    small_integer_field = models.SmallIntegerField(null=True, blank=True, default=0)
    text_field = models.TextField(null=True, blank=True)
    time_field = models.TimeField(null=True, blank=True)
    url_field = models.URLField(max_length=200, null=True, blank=True)
    uuid_field = models.UUIDField(primary_key=True)

    def __str__(self):
        return f"Example Model {self.pk}"
```
Relationships between models (one-to-one, one-to-many, many-to-many):
- One-to-One Relationship: In a one-to-one relationship, each Employee has exactly one EmployeeProfile.
```
class Employee(models.Model):
    name = models.CharField(max_length=100)
class EmployeeProfile(models.Model):
    employee = models.OneToOneField(Employee, on_delete=models.CASCADE)
    bio = models.TextField()
```
- One-to-Many Relationship: In a one-to-many relationship, each Customer can have multiple Orders.
```
class Customer(models.Model):
    name = models.CharField(max_length=100)
class Order(models.Model):
    customer = models.ForeignKey(Customer, on_delete=models.CASCADE)
    order_number = models.CharField(max_length=20)
```
- Many-to-Many Relationship: In a many-to-many relationship, each Customer can have multiple Tags, and each Tag can be associated with multiple Customers.
```
class Tag(models.Model):
    name = models.CharField(max_length=100)
class Customer(models.Model):
    name = models.CharField(max_length=100)
    tags = models.ManyToManyField(Tag)
```


# Select Related and Prefetch Related 
Let's assume you have two models: Author and Post, where each Post has a foreign key relationship with an author.
```
from django.db import models

class Author(models.Model):
    name = models.CharField(max_length=100)

    def __str__(self):
        return self.name

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    author = models.ForeignKey(Author, on_delete=models.CASCADE)

    def __str__(self):
        return self.title

```
## select_related: 
is used to fetch related objects in a single database query by performing a join operation. It follows foreign keys and retrieves the related objects. This method is useful when you know that you will access the related objects and want to minimize database queries.
```
# Fetch all posts with their associated authors using select_related
posts = Post.objects.select_related('author').all()

# Access the author of each post
for post in posts:
    print(f"Post: {post.title} by {post.author.name}")
Output:
Post: Post 1 by John Doe
Post: Post 2 by John Doe
Post: Post 1 by Jane Smith
Post: Post 2 by Jane Smith
```
In this example, select_related('author') retrieves the author information for each post in a single database query. Therefore, when accessing post.author.name, no additional database query is executed.

Equivalent SQL Query:
```
from django.db import connection
query = '''
    SELECT post.id, post.title, post.content, author.id, author.name
    FROM post
    INNER JOIN author ON post.author_id = author.id
'''
posts = Post.objects.raw(query)
```
In general, for most common use cases, relying on Django's ORM and using select_related provides a more efficient and convenient way to fetch related objects compared to writing raw SQL queries.

## prefetch_related: It fetches all the related objects and caches them in memory, reducing the overall database hits. This method is useful when you are dealing with many-to-many relationships or reverse foreign key relationships.
```
# Fetch all authors with their associated posts using prefetch_related
authors = Author.objects.prefetch_related('post_set').all()

# Access the posts of each author
for author in authors:
    print(f"Author: {author.name}")
    for post in author.post_set.all():
        print(f"Post: {post.title}")
Output:
Author: John Doe
Post: Post 1 by John
Post: Post 2 by John
Author: Jane Smith
Post: Post 1 by Jane
Post: Post 2 by Jane
```
In this example, prefetch_related('post_set') fetches all the posts associated with each author. The related posts are cached in memory, allowing efficient access without additional database queries. Therefore, when accessing author.post_set.all(), no extra database query is executed.

Eqivalent SQL Query:
```
from django.db import connection

query = '''
    SELECT author.id, author.name, post.id, post.title, post.content
    FROM author
    LEFT JOIN post ON author.id = post.author_id
    ORDER BY author.id;
'''
posts = Post.objects.raw(query)
```




## Query
```
all_objects = Model.objects.all()
limited_objects = Model.objects.all()[:5]
obj = Model.objects.get(pk=1)
first_object = Model.objects.first()
first_object = Model.objects.last()
filtered_objects = Model.objects.filter(attribute=value) // filter with condition
excluded_objects = Model.objects.exclude(attribute=value) //filter out objects that match the condition
filtered_objects = Model.objects.filter(attribute1=value1, attribute2=value2) // AND operation between two condition

from django.db.models import Q
filtered_objects = Model.objects.filter(Q(attribute1=value1) | Q(attribute2=value2)) // OR operation
ordered_objects = Model.objects.order_by('field_name')

reversed_objects = Model.objects.reverse()
distinct_objects = Model.objects.values('field_name').distinct() // to retrieve distinct values of a specific field from the model.
object_count = Model.objects.count()

// aggregate() function is a dictionary-like object that contains the computed aggregation results.
max_value = Model.objects.aggregate(max_value=models.Max('field_name'))  // print(max_price['max_price'])
min_value = Model.objects.aggregate(min_value=models.Min('field_name'))
total_sum = Model.objects.aggregate(total_sum=models.Sum('field_name'))

// Group objects based on a field
from django.db.models import Count
grouped_objects = Model.objects.values('field_name').annotate(count=Count('field_name')) // Count('field_name') aggregation function counts the occurrences of each distinct value in the specified field.
Sample Output:
[
  {'field_name': 'field_name1', 'count': 10},
  {'field_name': 'field_name2', 'count': 5},
  {'field_name': 'field_name3', 'count': 8}
]


filtered_objects = Model.objects.filter(attribute__iexact='value') //  Perform case-insensitive filtering: It retrieves all objects where the attribute value is exactly equal to 'value'
searched_objects = Model.objects.filter(attribute__icontains='value') // Perform a case-insensitive search: It retrieves all objects where the attribute value contains the substring 'value'. it will match 'value', 'SomeValue', 'another_value', and so on. 

```
## Form
```
from django import forms
class MyForm(forms.Form):
    char_field = forms.CharField(max_length=100)
    integer_field = forms.IntegerField()
    float_field = forms.FloatField()
    decimal_field = forms.DecimalField(max_digits=5, decimal_places=2)
    boolean_field = forms.BooleanField()
    date_field = forms.DateField(widget=forms.DateInput(attrs={'type': 'date'}))
    datetime_field = forms.DateTimeField(widget=forms.DateTimeInput(attrs={'type': 'datetime-local'}))
    time_field = forms.TimeField(widget=forms.TimeInput(attrs={'type': 'time'}))
    email_field = forms.EmailField()
    url_field = forms.URLField()
    file_field = forms.FileField()
    image_field = forms.ImageField()
    choice_field = forms.ChoiceField(choices=[('option1', 'string1'), ('option2', 'string2'),('option3', 'string2'),('option4', 'string2')])
    multiple_choice_field = forms.MultipleChoiceField(choices=[('option1', 'string1'), ('option2', 'string2'),('option3', 'string2'),('option4', 'string2')])
    #model_choice_field = forms.ModelChoiceField(queryset=MyModel.objects.all())
    #model_multiple_choice_field = forms.ModelMultipleChoiceField(queryset=MyModel.objects.all())
    password_field = forms.CharField(widget=forms.PasswordInput)
    #regex_field = forms.CharField(validators=[RegexValidator(r'^\d{10}$', 'Enter a 10-digit number.')])
```

## Social Authentication
## Google
![goggle_login](https://github.com/Parvez49/Advanced-Django/assets/72366747/c5dbf80d-651b-478d-b63a-b86d5b36f6e3)


The Google provider is OAuth2 based.
Allauth documentation is here: https://django-allauth.readthedocs.io/en/latest/providers.html#google

Django setting:
```
ALLOWED_HOSTS = ['127.0.0.1']
INSTALLED_APPS = [
        ...
        'allauth.socialaccount.providers.google',
        ...
        ]
SITE_ID = 1

SOCIALACCOUNT_PROVIDERS = {
    'google': {
        'SCOPE': [
            'profile',
            'email',
        ],
        'AUTH_PARAMS': {
            'access_type': 'online',
        },
        'OAUTH_PKCE_ENABLED': True,
    }
}
```
Google Developer Console
https://console.developers.google.com/

Register as new project
```
Credential ----> Authorized JavaScript origins ----> http://127.0.0.1:8090
           ----> Authorized redirect URIs      ----> http://127.0.0.1:8090/accounts/google/login/callback/
OAuth Consent Screen ----> App information ----> App Name ----> Django Auth App
                                           ----> User support email ----> parvezhossen81@gmail.com
                    ----> App domain ----> Application home page ----> http://127.0.0.1:8090/
                                     ----> Application privacy policy link ----> http://127.0.0.1:8090/privacy/
                                     ----> Application terms of service link ----> http://127.0.0.1:8090/service/
```
Database Setting:
```
Django Administration ----> Sites ----> Domain name: 127.0.0.1:8090
                                  ----> Display Name: localhost
                      ----> Social applications ----> Provider, Name, Client id, Secret id, sites
```

## Github
Allauth documentation is here: https://django-allauth.readthedocs.io/en/latest/providers.html#github
Django setting:
```
SOCIALACCOUNT_PROVIDERS = {
    'github': {
        'SCOPE': [
            'user',
            'repo',
            'read:org',
        ],
    }
}
```
Github Developer Setting
https://github.com/settings/applications/new
```
Credential ----> Authorized JavaScript origins ----> http://127.0.0.1:8090
           ----> Authorized redirect URIs      ----> http://127.0.0.1:8090/accounts/google/login/callback/
```
Database Setting:
```
Django Administration ----> Sites ----> Domain name: 127.0.0.1:8090
                                  ----> Display Name: localhost
                      ----> Social applications ----> Provider, Name, Client id, Secret id, sites
```

## Gmail Validation
to validate the recipient email to make sure it is valid and active before we try to send an email to it.
We can use API to valid it. Abstract API also gives this service: https://www.abstractapi.com/guides/django-send-email
Validation code this api response:
```
import requests

api_key = 'ab94318200074509bde56c4e37081464' # https://app.abstractapi.com/api/email-validation
api_url = 'https://emailvalidation.abstractapi.com/v1/?api_key=' + api_key                                                                                                                                                                               

def validate_email(email):
    response = requests.get(api_url + f"&email={email}")
    data = response.json()

    if (
    data['is_valid_format']['value'] and
    data['is_mx_found']['value'] and
    data['is_smtp_valid']['value'] and
    not data['is_catchall_email']['value'] and
    not data['is_role_email']['value'] and
    data['is_free_email']['value']):
        return True
    else:
        return False
```
## Gmail Send
Gmail Account Setting: In https://myaccount.google.com/security, do you see 2-step verification set to ON? If yes, then visiting https://myaccount.google.com/apppasswords should allow you to set up application specific passwords. 

django settings.py:
```
EMAIL_BACKEND = 'django.core.mail.backends.smtp.EmailBackend'
EMAIL_HOST = 'smtp.gmail.com'
EMAIL_USE_TLS = True
EMAIL_PORT = 587
EMAIL_HOST_USER = '-----@gmail.com'
EMAIL_HOST_PASSWORD = "apppasswordsFromGmailAccountSetting"
```
Code for sending gmail:
```
from django.core.mail import send_mail# Storing shopping cart contents in the session
request.session['cart'] = {
    'items': [
        {'id': 1, 'name': 'Product 1', 'price': 10.99},
        {'id': 2, 'name': 'Product 2', 'price': 19.99},
        # ...
    ],
    'total': 30.98
}
from django.conf import settings
subject = '____'
    message = "_________"
    email_from = settings.EMAIL_HOST_USER
    recipient_list = [email]
    send_mail(subject, message , email_from ,recipient_list )
```

# Cookie
Cookies are small pieces of data that are stored on the client's web browser. They are used to store information about the user or their interactions with the website.  Here are some examples of the types of data commonly stored in cookies: User-specific data(username, user ID, or any other user-specific identifier), Session-related data(session ID), Authentication-related data(remember me), Tracking and analytics data etc.
```
from django.http import HttpResponse

def set_cookie_view(request):
    response = HttpResponse("Cookie has been set.")
    response.set_cookie('username', 'john', max_age=3600)  # Set a cookie named 'username' with value 'john' and a maximum age of 1 hour
    return response

def get_cookie_view(request):
    username = request.COOKIES.get('username')  # Retrieve the value of the 'username' cookie
    return HttpResponse(f"The username from the cookie is: {username}")
...
response.delete_cookie('username')
...
```
It's important to note that cookies have a limited storage capacity (typically around 4KB) and should not be used to store large amounts of data. Additionally, since cookies are stored on the client-side, they are inherently less secure than storing sensitive data on the server-side using sessions or databases. Therefore, it's recommended to avoid storing sensitive or confidential information directly in cookies.


# Session
Sessions in Django are a way to store and retrieve arbitrary data for each individual user across multiple requests. Unlike cookies, the session data is stored on the server side, and only a session ID is sent to the client as a cookie.

To implement sessions in a Django project:
Configure Django's session settings:
```
# settings.py

# Add 'django.contrib.sessions' to the INSTALLED_APPS list
INSTALLED_APPS = [
    'django.contrib.sessions',
]

# Configure the session middleware
MIDDLEWARE = [
    'django.contrib.sessions.middleware.SessionMiddleware',
]
```
Save and retrieve session data:
```
# views.py

from django.shortcuts import render, redirect
from django.contrib.auth.models import User

def login(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        # Perform authentication logic (e.g., verify credentials)
        user = authenticate(username=username, password=password)
        if user is not None:
            request.session['user_id'] = user.id
            return redirect('dashboard')
        else:
            return render(request, 'login.html', {'error': 'Invalid credentials'})
    else:
        return render(request, 'login.html')

def dashboard(request):
    user_id = request.session.get('user_id')
    if user_id:
        user = User.objects.get(id=user_id)
        return render(request, 'dashboard.html', {'user': user})
    else:
        return redirect('login')

def logout(request):
    del request.session['user_id']
    return redirect('login')
```
Other use of session:
```
# Storing user preferences in the session
request.session['preferences'] = {
    'theme': 'dark',
    'language': 'en',
    'timezone': 'UTC'
}
```
Let's consider an e-commerce website where users can add products to their cart and proceed to checkout. To implement this, we can leverage the power and flexibility of sessions.
```
# views.py

from django.shortcuts import render, redirect

def add_to_cart(request, product_id):
    product = get_product_by_id(product_id)
    if product:
        cart = request.session.get('cart', {})
        if product_id in cart:
            cart[product_id]['quantity'] += 1
        else:
            cart[product_id] = {
                'product': product,
                'quantity': 1
            }
        request.session['cart'] = cart
        return redirect('cart')
    else:
        return redirect('products')

def cart(request):
    cart = request.session.get('cart', {})
    total_items = sum(item['quantity'] for item in cart.values())
    return render(request, 'cart.html', {'cart': cart, 'total_items': total_items})

def checkout(request):
    cart = request.session.get('cart', {})
    if request.method == 'POST':
        # Process the checkout and complete the order
        # ...
        # Clear the cart from the session after successful checkout
        del request.session['cart']
        return redirect('order_confirmation')
    else:
        return render(request, 'checkout.html', {'cart': cart})
```
This example demonstrates the power and flexibility of sessions in managing user-specific data and complex workflows in a Django project.

In Django, the default behavior is that session data is not automatically deleted when a user logs out. The session data is maintained on the server and will persist until its expiration time is reached or until it is explicitly cleared.
The Django session framework provides options to control the behavior of session data upon user logout:
Expire on browser close:
```
# settings.py
# Session expires when the user closes the browser
SESSION_EXPIRE_AT_BROWSER_CLOSE = True
```
Custom expiration time:
```
# settings.py
# Set session timeout to 1 hour (in seconds)
SESSION_COOKIE_AGE = 3600
```
Manual deletion:
```
# Clear the session data upon logout
    request.session.clear()
    logout(request)
```

# Caching 
is used as a performance optimization technique to store frequently accessed data in a faster and more easily accessible location, such as memory, in order to reduce the need for repeated expensive computations or database queries.
Here are a few reasons why caching is used: Reduced load on the database, Improved performance

## Caching Technique

1. MemcachedCache:
django.core.cache.backends.memcached.MemcachedCache uses the Memcached caching system to store cached data. Memcached is a high-performance, distributed caching system that can be shared across multiple servers. It's suitable for production environments that require a scalable and distributed cache.
To use the MemcachedCache backend in Django:
Install required packages:
```
pip install python-memcached
```
Configure Django settings:
```
# settings.py

CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': '127.0.0.1:11211',  # Replace with your Memcached server address
    }
}
```
Use caching in your views:
```
from django.core.cache import cache
from .models import Product

def get_products():
    products = cache.get('products')
    if products is None:
        # Products not found in the cache, fetch them from the database
        products = Product.objects.all()
        # Store products in the cache for future requests with a cache timeout of 1 hour
        cache.set('products', products, timeout=3600)
    return products

def products_view(request):
    products = get_products()
    return render(request, 'products.html', {'products': products})
```
Clearing the cache:
```
# Clear a specific cache entry
cache.delete('products')

# Clear the entire cache
cache.clear()
```

2. RedisCache:
an in-memory data structure store, as the caching backend. Redis is known for its speed, flexibility, and advanced features.

It's important to note that sessions and caching serve different purposes. Sessions are primarily used for user-specific data and maintaining state, while caching is focused on improving performance by storing frequently accessed or computationally expensive data. 
To implement RedisCache as the cache backend in Django:
Install the required packages:
```
pip install redis
```
Configure the cache backend:
```
# settings.py

CACHES = {
    'default': {
        'BACKEND': 'django_redis.cache.RedisCache',
        'LOCATION': 'redis://localhost:6379/0',  # Replace with your Redis server details
        'OPTIONS': {
            'CLIENT_CLASS': 'django_redis.client.DefaultClient',
        }
    }
}
```
Use RedisCache in your views:
```
from django.core.cache import cache

def get_products():
    products = cache.get('products')
    if products is None:
        # Products not found in the cache, fetch them from the database
        products = Product.objects.all()
        # Store products in the cache for future requests
        cache.set('products', products, timeout=3600)  # Cache for 1 hour
    return products

def products_view(request):
    products = get_products()
    return render(request, 'products.html', {'products': products})
```
Clearing the Redis cache:
```
from django.core.cache import cache
cache.clear()
```



# JWT
Json Web Token
```
pip install PyJWT
```
```
import jwt
import datetime

payload = {
    'id': user.id,
    'username': user.username,
    'role': user.role,
    'is_active': user.is_active,
    'exp': datetime.datetime.utcnow() + datetime.timedelta(minutes=60),
    'iat': datetime.datetime.utcnow()
}

secret_key = 'your-secret-key'  # Replace with your actual secret key
algorithm = 'HS256'  # Specify the desired algorithm for signing the token

token = jwt.encode(payload, secret_key, algorithm=algorithm)

...
payload = jwt.decode(token, secret_key, algorithms=['HS256'])
...
```
The payload in a JWT can contain any number of fields as needed for your application.


# Celery
is an open-source, distributed task queue that is widely used in Python applications, including Django. It allows you to perform tasks asynchronously, meaning that instead of processing the task immediately, the task is added to a queue to be executed later. This is especially useful for time-consuming or resource-intensive tasks, such as sending emails, processing large files, or performing external API calls, without blocking the main application.
```
pip install celery
```
configure Celery in your Django settings:
```
# settings.py
# Celery settings
CELERY_BROKER_URL = 'redis://localhost:6379/0'
```
In the Django settings.py file, the CELERY_BROKER_URL setting defines the URL of the message broker that Celery will use. The message broker is responsible for handling the communication between the application and the Celery workers.

In this example, we are using Redis as the message broker, which will be responsible for managing the message queue.
Now, let's create a task that sends an email using Celery:
```
# tasks.py

from django.core.mail import send_mail
from celery import shared_task

@shared_task
def send_email_task(subject, message, from_email, recipient_list):
    send_mail(subject, message, from_email, recipient_list)
```
In this example, we've defined a Celery task called send_email_task, which takes the necessary parameters to send an email using Django's send_mail function.

Finally, let's trigger the Celery task from a view in our Django application:
```
# views.py
from django.shortcuts import render
from .tasks import send_email_task

def send_email_view(request):
    # Your view logic here...

    # Assume we have the following data
    subject = "Hello from Django and Celery!"
    message = "This is an asynchronous email sent using Celery."
    from_email = "sender@example.com"
    recipient_list = ["recipient@example.com"]

    # Trigger the Celery task
    send_email_task.delay(subject, message, from_email, recipient_list)

    # Return the response to the user
    return render(request, 'success.html')
```
The task is added to the Celery queue and will be executed in the background.

Now, when a user submits the email form, they will receive an immediate response, and the email will be sent asynchronously in the background using Celery, allowing the application to be more responsive and scalable.

Another Example:
Next, let's create the Celery task for processing the CSV file:
```
# tasks.py
import csv
from celery import shared_task
from .models import SalesData

@shared_task
def process_csv_file_task(file_path):
    with open(file_path, 'r') as csvfile:
        reader = csv.reader(csvfile)
        # Skip header row
        next(reader)

        for row in reader:
            # Process each row of data and save it to the database
            sales_data = SalesData(
                date=row[0],
                product=row[1],
                amount=float(row[2]),
                quantity=int(row[3])
            )
            sales_data.save()
```
Now, let's handle the CSV file upload in a Django view:
```
# views.py

from django.shortcuts import render
from .tasks import process_csv_file_task

def upload_csv_view(request):
    if request.method == 'POST' and request.FILES['csv_file']:
        # Get the uploaded file from the request
        csv_file = request.FILES['csv_file']

        # Save the uploaded file to a temporary location
        with open('temp.csv', 'wb') as temp_file:
            for chunk in csv_file.chunks():
                temp_file.write(chunk)

        # Trigger the Celery task to process the CSV file asynchronously
        process_csv_file_task.delay('temp.csv')

        return render(request, 'success.html')
    
    return render(request, 'upload.html')
```
By using Celery, the file processing happens in the background, allowing the user to receive an immediate response and continue using the application. Meanwhile, Celery takes care of processing the uploaded CSV file, and the user can check the results later or receive notifications when the task is completed. 



## Requirement.txt
This automatically create requirement.txt in project.
```
pip freeze > requirements.txt
```
## PIP Command
```
pip install django-allauth
pip install --upgrade django-allauth
pip uninstall django-allauth
```

# Databases
## Django-PostgreSQL
Django comes with a SQLite database which is great for testing and debugging at the beginning of a project.However, it is not very suitable for production. PostgreSQL database is an open source relational database, which should cover most demands you have when creating a database for a Django project.
official postgresql website: https://www.postgresql.org/download/linux/redhat/
https://developer.fedoraproject.org/tech/database/postgresql/about.html

During the PostgreSQL installation process, a default user called "postgres" is created.
Switch to the "postgres" user by running the following command:
```
sudo -u postgres psql
```
Reset the password by running the following command:
```
ALTER USER postgres WITH PASSWORD 'new_password';
```
When i registered in pgAdmin, i faced some problem like fetal error: to solve this i tried the following method:
Switch to the "postgres" user by running the following command:
```
sudo -u postgres bash
```
in the "postgres" user, locate the data directory by running the following command:
```
psql -c "SHOW data_directory;"
```
This command will display the path to the PostgreSQL data directory. Inside the data directory, you should find the pg_hba.conf file.
Open the pg_hba.conf file using the nano/vi/vim text editor:
```
nano pg_hba.conf
```
Change the authentication method from ident to md5 or password. The line should look like one of the following:
```
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```
to
```
# IPv4 local connections:
host    all             all             127.0.0.1/32            password
# IPv6 local connections:
host    all             all             ::1/128                 password
```
after making these changes, save the pg_hba.conf file and restart the PostgreSQL service for the changes to take effect.
some command:
```
systemctl status postgresql
sudo /usr/pgsql-15/bin/postgresql-15-setup initdb
sudo systemctl enable postgresql-15
sudo systemctl start postgresql-15
sudo systemctl stop postgresql-15
sudo systemctl restart postgresql-15
```


## Django-PostgreSQL
```
Firstly install mysql (pip instal mysqlclient)
mysql --version
sudo dnf install mysql-server
sudo systemctl start "mysqld"  fedora uses mariadb.service instead of mysqld
sudo systemctl enable mysqld
sudo mysql_secure_installation
mysql -u root -p OR sudo mysql

pip install mysql
pip install mysql-connector-python
pip install mysql-connector
```
###setting.py:
```
  DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}
```
to
```
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.mysql",
        "NAME": 'db',
        "USER":  'root',
        'PASSWORD': 'password789'
        'HOST': 'localhost',
        'PORT': '3306',
    }
}
```

# Redis
Redis is an open source, advanced key-value store and an apt solution for building highperformance, scalable web applications.
### String 
Redis string is a sequence of bytes. Thus, you can store anything up to 512 megabytes in one string.
>>> SET name "tutorialspoint" 
>>> GET name
>>> DEL tutorialspoint 
### Hashes
A Redis hash is a collection of key value pairs. Redis Hashes are maps between string fields and string values. 
>>>  HMSET user:1 username tutorialspoint password tutorialspoint points 200
>>> HGETALL user:1
### Lists
Redis Lists are simply lists of strings, sorted by insertion order. You can add elements to a Redis List on the head or on the tail.
>>> lpush tutoriallist redis
>>> lpush tutoriallist redis2
>>> lrange tutoriallist 0 10  
### Sets
Redis Sets are an unordered collection of strings. In Redis, you can add, remove, and test for the existence of members in O(1) time complexity.
>>> sadd tutoriallist redis
>>> sadd tutoriallist redi2
>>> smembers tutoriallist  



# Git

```
git init --> The git init command creates a new Git repository.
git config --global user.name "Name"  --> Git uses a username to associate commits with an identity.
git config --global user.email "user@example.com"

### cennection between git and github with ssh: https://www.youtube.com/watch?v=iWs34DO_H2M
git commit -m "This is my second commit."

git status
git add README
git commit

git diff README --> Git tracks the changes and displays that the file has been modified.
git log  --> Git log command shows the commit history of the repository.
```
Git Branch
```
git branch improve-output --> create a branch named improve-output.
git checkout improve-output --> Move to the improve-output branch from the master branch.
git add food_count.py
git revert [commit-ID]  --> revert back the previous commit from [commit-ID]


Merge operation

git checkout master --> switch to the master branch from the current branch improve-output branch
git merge improve-output


git clone https://github.com/[username]/[git-repo].git
git commit
git push origin main
# If some changes in github directory
git pull origin main -->  pull the current snapshot/commit in the remote repository to the local repository. This opens an editor that asks you to enter a commit message for the merge operation.
```
Forking and detect function behavior:
```
click Fork
git clone https://github.com/[git-username]/it-cert-automation-practice.git
git remote -v
// In terms of source control, you're "downstream" when you copy (clone, checkout, etc) from a repository. Information is flowed "downstream" to you.
Setting the upstream for a fork:
git remote add upstream https://github.com/[git-username]/it-cert-automation-practice.git
git push origin branch_name
Now make a pull request
```


# Interview Question:
1. Difference between authentication and authorization: Authentication is: "Who are you?" and Authorization is: "What are you allowed to do?". Authentication is the process of verifying the identity of a user, while authorization is the process of granting or denying access rights and permissions based on that authenticated identity.
2. Django management script: A Django management script is a script that allows you to run code and perform various tasks outside of the typical request/response cycle of a Django web application. These management scripts are typically used for administrative purposes, such as database management, data migration, creating or deleting objects, running periodic tasks, and other maintenance operations.

Example: python manage.py startapp myapp,python manage.py migrate, runserver, createsuperuser etc. These management scripts are helpful in automating various routine tasks and are often used in combination with tools like Celery to schedule periodic tasks, such as sending emails or performing data cleanup at regular intervals.
3. Difference between abstract and base class: 
An abstract class is a class that cannot be instantiated directly, meaning you cannot create objects directly from an abstract class. A base class is a class that other classes can inherit from. It is not necessarily an abstract class; it can be either abstract or concrete.
The primary purpose of an abstract class is to serve as a blueprint or template for other classes. The purpose of a base class is to provide common attributes and methods that can be shared by its subclasses.

4. The key differences between get() and filter() are:
   get() returns a single object, while filter() returns a queryset containing multiple objects (even if there's only one result).
   get() raises exceptions if no object or multiple objects are found, whereas filter() always returns a queryset, even if it's empty.








