# python-grade-finale
pip install django djangorestframework


django-admin startproject school_communication
cd school_communication
django-admin startapp communication


# models.py in communication app

from django.db import models

class Teacher(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()

    def __str__(self):
        return self.name


class Parent(models.Model):
    name = models.CharField(max_length=200)
    email = models.EmailField()
    student = models.ForeignKey('Student', on_delete=models.CASCADE, related_name="parents")

    def __str__(self):
        return self.name


class Student(models.Model):
    first_name = models.CharField(max_length=100)
    last_name = models.CharField(max_length=100)
    grade = models.CharField(max_length=50)

    def __str__(self):
        return f"{self.first_name} {self.last_name}"


class Message(models.Model):
    sender = models.ForeignKey(Teacher, on_delete=models.CASCADE)
    recipient = models.ForeignKey(Parent, on_delete=models.CASCADE)
    content = models.TextField()
    timestamp = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return f"Message from {self.sender.name} to {self.recipient.name}"


class Meeting(models.Model):
    teacher = models.ForeignKey(Teacher, on_delete=models.CASCADE)
    parent = models.ForeignKey(Parent, on_delete=models.CASCADE)
    scheduled_time = models.DateTimeField()
    agenda = models.TextField()

    def __str__(self):
        return f"Meeting with {self.parent.name} scheduled for {self.scheduled_time}"


# views.py in communication app

from rest_framework import viewsets
from .models import Teacher, Parent, Student, Message, Meeting
from .serializers import TeacherSerializer, ParentSerializer, StudentSerializer, MessageSerializer, MeetingSerializer

class TeacherViewSet(viewsets.ModelViewSet):
    queryset = Teacher.objects.all()
    serializer_class = TeacherSerializer


class ParentViewSet(viewsets.ModelViewSet):
    queryset = Parent.objects.all()
    serializer_class = ParentSerializer


class StudentViewSet(viewsets.ModelViewSet):
    queryset = Student.objects.all()
    serializer_class = StudentSerializer


class MessageViewSet(viewsets.ModelViewSet):
    queryset = Message.objects.all()
    serializer_class = MessageSerializer


class MeetingViewSet(viewsets.ModelViewSet):
    queryset = Meeting.objects.all()
    serializer_class = MeetingSerializer

# urls.py in communication app

from django.urls import include, path
from rest_framework.routers import DefaultRouter
from . import views

router = DefaultRouter()
router.register(r'teachers', views.TeacherViewSet)
router.register(r'parents', views.ParentViewSet)
router.register(r'students', views.StudentViewSet)
router.register(r'messages', views.MessageViewSet)
router.register(r'meetings', views.MeetingViewSet)

urlpatterns = [
    path('api/', include(router.urls)),
]
# urls.py in school_communication project

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('communication/', include('communication.urls')),
]
