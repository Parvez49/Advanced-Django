# Advanced-Django

django: Django is a free and open-source, Python-based web framework that follows the model–template–views architectural pattern.

## Contents

- [Advanced Django](#advanced-django)
    - [Form submission without reload](https://github.com/Parvez49/Advanced_Django)
    - [Django Rest API](https://github.com/Parvez49/Advanced_Django)
    - [Model](#Model)
 


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






