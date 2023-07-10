# Advanced-Django

django: Django is a free and open-source, Python-based web framework that follows the model–template–views architectural pattern.

## Contents

- [Advanced Django](#advanced-django)
    - [Form submission without reload](https://github.com/Parvez49/Advanced_Django)
    - [Django Rest API](https://github.com/Parvez49/Advanced_Django)
    - [Model](#Model)
    - [Query](#Query)
    - [Form](#Form)
    - [Social Authentication](#Social-Authentication)
    - [Gmail Validation](#Gmail-Validation)
    - [Gmail Send](#Gmail-Send)
    - [Databases](#Databases)
    - [RequirementFile](#Requirement.txt)
    - [pip command](#PIP-Command)
 


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
    decimal_field = models.DecimalField(max_digits=5, decimal_places=2)
    duration_field = models.DurationField()
    email_field = models.EmailField(max_length=254, null=True, blank=True)
    file_field = models.FileField(upload_to='files/', max_length=100) // max_length: The maximum length of the file path.
    float_field = models.FloatField(null=True, blank=True, default=0.0)
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
from django.core.mail import send_mail
from django.conf import settings
subject = '____'
    message = "_________"
    email_from = settings.EMAIL_HOST_USER
    recipient_list = [email]
    send_mail(subject, message , email_from ,recipient_list )
```

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















