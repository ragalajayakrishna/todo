#  home .html

<!DOCTYPE html>
<html>
<head>
  <title>Todo List</title>
</head>
<body>
    <div class="background">
        <div class="shape"></div>
        <div class="shape"></div>
    </div>
    <form method="post">
        {% csrf_token %} 
        <h3>CREATE TODO LIST</h3>

        <label for="title">Title</label>
        <input type="text" placeholder="Enter a task" name="title" id="title" required>

        <label for="description">Description</label>
        <input type="text" placeholder="Write a description" name="description" id="description" required>

        <button type="submit">Submit</button>

        <a href="list" class="button-link">
            <button type="button">Task List</button>
        </a>




    </form>
</body>

</html>



# tasklist.html


<!DOCTYPE html>
<html>
<head>
  <title>List of Task</title>
</head>
<body>
  <h3>Task View</h3>

  <p>
    {% for i in list %}
        <p>{{ i.id }}--{{ i.t_title }}---{{ i.t_description }}<p>
        <form action="{% url  'delete' i.id %}" method="post" >
            {% csrf_token %}
            <button type="submit">delete</button>
            </button>
           </form>
    {% endfor %}
  </p>

  <form method="POST" action="{% url 'update' %}">
    <h3>Update Task</h3>
    {% csrf_token %}
    <input type="hidden" name="_method" value="PUT">
    <input type="text" id="id" name="id" required >

    <label for="title">Title:</label>
    <input type="text" id="title" name="title" >

    <label for="description">Description:</label>
    <input type="text" id="description" name="description" >

    <button type="submit">Save Changes</button>


</form>
</body>
</html>



## apps.py

from django.apps import AppConfig


class TejaConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'teja'


#### admin.py

from django.contrib import admin



##### models.py


from django.db import models

# Create your models here.
class vinay(models.Model):
    t_title = models.CharField(max_length =50)
    t_description = models.TextField()



### test.py

from django.test import TestCase


### project urls.py


from django.urls import path
from . import views

urlpatterns = [
    path('',views.home),
    path('list/',views.list),
    path('update/',views.Update_Task,name='update'),
    path('delete/<int:id>',views.Delete_Task,name='delete')
]


#### views.py

#from django.shortcuts import render

#Create your views here.


from django.shortcuts import render,get_object_or_404,HttpResponseRedirect,redirect
from .models import vinay

#Create your views here.
def home(request):
    if request.method == 'POST':
        title = request.POST['title']
        description = request.POST['description']
        list = vinay(t_title=title,t_description=description)
        list.save()
    return render(request,'home.html')

def list(request):
    if request.method == 'GET':
        a = vinay.objects.all()
        return render(request,'tasklist.html',{'list':a})

def Update_Task(request):
    if request.method == 'POST':

        id = request.POST['id']
        title = request.POST['title']
        description = request.POST['description']

        b = get_object_or_404(vinay,pk=id)

        b.t_title=title
        b.t_description = description
        b.save()
        return HttpResponseRedirect ("/list/")

def Delete_Task(request,id):
    vinay= vinay.objects.get(id=id)
    vinay.delete()
    return redirect('/list/')






### app urls.py


"""
URL configuration for vinay project.

The `urlpatterns` list routes URLs to views. For more information please see:
    https://docs.djangoproject.com/en/5.0/topics/http/urls/
Examples:
Function views
    1. Add an import:  from my_app import views
    2. Add a URL to urlpatterns:  path('', views.home, name='home')
Class-based views
    1. Add an import:  from other_app.views import Home
    2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
Including another URLconf
    1. Import the include() function: from django.urls import include, path
    2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))
"""
from django.contrib import admin
from django.urls import path,include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('teja.urls')),
]




##### 
