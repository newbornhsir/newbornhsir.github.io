---
title: djano学习
date: 2017-06-08 17:07:31
tags: python django
---

# django学习
python=3.4.3,django=1.9.7,参考官网 [Writing your first Django app](https://docs.djangoproject.com/en/1.9/intro/tutorial01/) 希望能对django有一个初步的了解。

## 第一部分
1 创建项目`django-admin startproject projectname`

目录结构如下

--- mysite/

------ mysite/

------------ __ init __.py

------------ setting.py

------------ urls.py

------------ wsgi.py

--- manage.py

这些文件是：

- 外层mysite/根目录只是项目的容器，你可以重新命名
- manage.py 提供了使用命令行和django项目交互的功能，详情在[adango-admin and manage.py](https://docs.djangoproject.com/en/1.9/ref/django-admin/)
- 内层mysite/目录是项目实际意义的python程序包，通过程序包的名称import其中的内容。eg:mysite.urls
- mysite/\__init__.py: 空文件，告诉python此目录是一个python程序包
- mysite/settings.py： django项目的配置文件。[工作原理](https://docs.djangoproject.com/en/1.9/topics/settings/)
- mysite/urls.py: url配置
- mysite/wsgi.py:为项目服务的一个WSGI-compatible web服务器的入口点。 [详情](https://docs.djangoproject.com/en/1.9/howto/deployment/wsgi/)


2 启动`python manage.py runserver`

3 新建polls app`python manage.py startapp polls`,执行命令后会在项目下创建polls目录

-	编写第一个视图,polls/views.py

 ```
 from django.http import HttpResponse

 def hello(request):
		return HttpResponse("hello,worls")
 ```
-	编写urls,polls/urls.py
 
 ```
 from django.conf.urls import url
 from . import views
 urlpatterns = [
		url(r'^$',views.hello,name="hello"),
 ]
 ```
-	将根目录的URLconf指向polls.urls，mysite/urls.py

	```
	from django.conf.urls import include, url
	from django.contrib import admin
	urlpatterns = [
		url(r'^polls/', include('polls.urls')),//include()函数可以连接其它URLconf
		url(r'^admin/', admin.site.urls),
	]
	```

-	显示效果`localhost:8000/polls`

&ensp;&ensp;&ensp;url有四个参数，两个必须（正则表达式--匹配URl和视图--确定视图函数）,两个可选（关键字参数--用字典的形式传递到指定视图和name--命名式路由）

## 第二部分--数据库
数据库的配置在mysite/setting.py的DATABASES中，默认使用db.sqlite3。INSTALLED_APP中包含了django 项目中激活的实例。默认包含如下app

```
INSTALLED_APPS = [
    'django.contrib.admin',# admin site
    'django.contrib.auth', # 验证
    'django.contrib.contenttypes', # 内容类型框架
    'django.contrib.sessions', # session框架
    'django.contrib.messages', # messageing framework
    'django.contrib.staticfiles', # 静态文件管理框架
]

```

需要运行命令为这些数据建立数据表`python manage.py migrate`。这个命令会查找INSTALLED_APPS设置，创建所需的数据表

- 创建数据模型polls/models.py

```
from django.db import models

# Create your models here.

class Question(models.Model):
	def __str__(self): #打印对象的字符串表达
		return self.question_text
	question_text = models.CharField(max_length=200)
	pub_date = models.DateTimeField('date published')

class Choice(models.Model):
	def __Str__(self):
		return self.choice_text
	#ForeignKey(Class_Name, limit_choices_to={‘key_name’: value}, on_delete=models.SET_NULL)
	#绑定一个其他类的元素(类名、可以是 ‘self’, 对于目标的属性的限制条件，关联目标被删除时的操作)
	question = models.ForeignKey(Question, on_delete=models.CASCADE)
	choice_text = models.CharField(max_length=200)
	votes = models.IntegerField(default=0)
```
- 将polls添加到INSTALLED_APPS中，在polls/apps.py中有pollsConfig，将polls.apps.pollsConfig添加到INSTALLED_APPS中，执行`python manage.py makemigrations polls`创建模型。`执行python manage.py sqlmigrate polls 0001`查看上述操作返回的SQL语句

```
	C:\Users\Administrator\Desktop\online\mysite>python manage.py makemigrations polls
	Migrations for 'polls':
	  0001_initial.py:
	    - Create model Choice
	    - Create model Question
	    - Add field question to choice

	C:\Users\Administrator\Desktop\online\mysite>python manage.py sqlmigrate polls 0001
	BEGIN;
	--
	-- Create model Choice
	--
	CREATE TABLE "polls_choice" (
		"id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
		"choice_text" varchar(200) NOT NULL, 
		"votes" integer NOT NULL);
	--
	-- Create model Question
	--
	CREATE TABLE "polls_question" (
		"id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, 
		"question_text" varchar(200) NOT NULL, 
		"pub_date" datetime NOT NULL);
	--
	-- Add field question to choice
	--
	ALTER TABLE "polls_choice" RENAME TO "polls_choice__old";
	CREATE TABLE "polls_choice" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "choice_text" varchar(200) NOT NULL, "votes" integer NOT NULL, "question_id" integer NOT NULL REFERENCES "polls_question" ("id"));
	INSERT INTO "polls_choice" ("choice_text", "question_id", "votes", "id") SELECT "choice_text", NULL, "votes", "id" FROM "polls_choice__old";
	DROP TABLE "polls_choice__old";
	CREATE INDEX "polls_choice_7aa0f6ee" ON "polls_choice" ("question_id");

	COMMIT;

```
- `python manage.py migrate`更新
- 使用python shell操作数据库`python manage.py shell`

- `python manage.py createsuperuser`创建登录用户，`http://localhost:8000/admin/`登录系统，发现并没有创建的Questiob和Choice

- 注册所创建的模型polls/admin.py,刷新页面，出现Question和Choice

```
	from django.contrib import admin
	from .models import Question, Choice
	# Register your models here.

	admin.site.register(Question)
	admin.site.register(Choice)
```
## 第三部分-视图
- 添加视图函数polls/views.py

```
	def detail(request,id):
		return HttpResponse("you are looking at %s" %id)

	def results(request,id):
		res = "your are looking at the results of question %s"
		return HttpResponse(res % id)

	def vote(request,id):
		return HttpResponse("you are voting on question %s" % id)
```
- 添加路由

```
	urlpatterns = [
		url(r'^$',views.hello,name="hello"),
		url(r'^(?P<id>[0-9]+)/$', views.detail, name='detail'),
	    # ex: /polls/5/results/
	    url(r'^(?P<id>[0-9]+)/results/$', views.results, name='results'),
	    # ex: /polls/5/vote/
	    url(r'^(?P<id>[0-9]+)/vote/$', views.vote, name='vote'),
	]
```
- 在管理界面添加测试数据，打开`http://localhost:8000/polls/1/`,`http://localhost:8000/polls/1/results`,`http://localhost:8000/polls/1/vote`查看显示效果。
视图中的id来自Url正则表达式中的id。使用括号包含内容，捕获和传递参数到视图函数
P<id>定义匹配模式参数的名称。
- 修改视图让它自动做一些事情
```
	from .models import Question
	def hello(request):
		latest_question_list = Question.objects.order_by('-pub_date')[:5]
		output = ", ".join([q.question_text for q in latest_question_list])
		return HttpResponse(output)
```
这种做法的缺点是页面硬编码在视图函数中，页面不易修改。改善方法---使用模板加载

- 新建模板polls/templates/polls/index.html

```
	{% if latest_question_list %}
	    <	ul>
	    {% for question in latest_question_list %}
	        <	li><	a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
	    {% endfor %}
	    <	/ul>
	{% else %}
	    <	p>No polls are available.<	/p>
	{% endif %}
```
- 修改视图函数，使用loader加载模板
```
	def hello(request):
		latest_question_list = Question.objects.order_by('-pub_date')[:5]
		# output = ", ".join([q.question_text for q in latest_question_list])
		# return HttpResponse(output)
		template = loader.get_template('polls/index.html')
		context = {
			'latest_question_list': latest_question_list
		}
		return HttpResponse(template.render(context,request))
```
- 更简便的方法：render()
```
	from django.shortcuts import render
	from django.http import HttpResponse
	from django.template import loader
	# Create your views here.
	from .models import Question
	def hello(request):
		latest_question_list = Question.objects.order_by('-pub_date')[:5]
		# output = ", ".join([q.question_text for q in latest_question_list])
		# return HttpResponse(output)
		# template = loader.get_template('polls/index.html')
		context = {
			'latest_question_list': latest_question_list
		}
		# return HttpResponse(template.render(context,request))
		return render(request,'polls/index.html',context)#三个参数，请求对象，模板，字典（可选）

```
- 处理404，修改视图函数
```
	from django.http import Http404
	def detail(request,id):
		try:
			question = Question.objects.get(pk=id)
		except Question.DoesNotExist:
			raise Http404("question does not exist")
		return HttpResponse("you are looking at %s" %id)
```
- 更简便的方法：get_object_or_404()
```
	from django.shortcuts import render,get_object_or_404
	def detail(request,id):
		# try:
		# 	question = Question.objects.get(pk=id)
		# except Question.DoesNotExist:
		# 	raise Http404("question does not exist")
		question = get_object_or_404(Question,pk=id)
		return HttpResponse("you are looking at %s" %question)
```
- 使用模板 polls/templates/polls/detail.html,修改detail的视图函数

```
< h1>{{ question.question_text }}< /h1>
< ul>
{% for choice in question.choice_set.all %}
    < li>{{ choice.choice_text }}</ li>
{% endfor %}
</ ul>
--

```
- 修改模板中的硬编码url

<pre><code>
<li><a href="{% url 'detail' question.id %}">{{ question.question_text }}</a></li>
</code></pre>

好处:方便路由管理

- 添加命名空间，区分url

1    在polls/urls.py中添加app_name="polls"

2    修改<pre><code><li>&lt;a href="{% url 'polls:detail' question.id %}"&gt;{{ question.question_text }}&lt;	/a&gt;</li></code></pre>

## 第四部分

- 处理表单,polls/templates/polls/detail.html
```
<h1>{{ question.question_text }}</h1>

{% if error_message %}<	p><	strong>{{ error_message }}<	/strong><	/p>{% endif %}

< form action="{% url 'polls:vote' question.id %}" method="post">
{% csrf_token %}
{% for choice in question.choice_set.all %}
    < input type="radio" name="choice" id="choice{{ forloop.counter }}" value="{{ choice.id }}" />
    < label for="choice{{ forloop.counter }}">{{ choice.choice_text }}< /label>< br />
{% endfor %}
< input type="submit" value="Vote" />
< /form>
```
- 修改视图函数vote
```
	from django.shortcuts import get_object_or_404, render
	from django.http import HttpResponseRedirect, HttpResponse
	from django.core.urlresolvers import reverse

	from .models import Choice, Question
	# ...
	def vote(request, id):
	    question = get_object_or_404(Question, pk=id)
	    try:
	        selected_choice = question.choice_set.get(pk=request.POST['choice'])
	    except (KeyError, Choice.DoesNotExist):
	        # Redisplay the question voting form.
	        return render(request, 'polls/detail.html', {
	            'question': question,
	            'error_message': "You didn't select a choice.",
	        })
	    else:
	        selected_choice.votes += 1
	        selected_choice.save()
	        # Always return an HttpResponseRedirect after successfully dealing
	        # with POST data. This prevents data from being posted twice if a
	        # user hits the Back button.
	        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))
```
## 第五部分--编写测试文件

## 第六部分--定制app样式

写在页面或者link加载
```
{% load staticfiles %}

< link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
```