---
title: django学习之http-request
date: 2017-06-10 01:05:18
tags:djangp
---

# django处理http请求(未完，待续)
执行`python manage.py startapp httpRequest`

## url设计
1 django如何处理请求

当用户从django站点发起一个请求，以下是算法系统接下来决定去执行什么python代码

-	django决定使用的root URLconf。通常，这个值由ROOT_URLCONF设置。但是，如果接下来的HttpRequest对象有urlconf属性(由middleware request processing设置)，这个值将替代ROOT_URLconf设置

-	django加载Python模块并寻找可变的urlpatterns--应该是一个django.conf.urls.url()的实例python列表

-	django按顺序执行每一个url模式并在第一个匹配的url停止

-	一旦正则匹配，django将暴露并执行所给的视图函数。视图传递以下参数

	1	HttpRequest实例

	2	如果匹配的正则表达式没有返回任何命名组，那么来自正则表达式的匹配将作为位置参数提供。

	3	关键字参数由正则表达式匹配的任何命名组提供，通过django.conf.urls.url()中的可选择的关键字参数来覆盖(翻译不准确)

-	如果没有匹配项或者在此过程中出现异常，django调用适当的异常处理视图。详情[error handling](https://docs.djangoproject.com/en/1.9/topics/http/urls/#error-handling)

### Example
详见httpRequest/views.py,httpRequest/urls.py
### named groups和no-named groups正则表达式匹配/分类的规则
1 如果存在任何命名参数，将使用命名参数，忽略无命名参数

2 其他情况，将无命名参数作为位置参数传递

### URLconf搜索的内容
URLconf搜索请求的url，将它作为Python字符串解析，不包含POST或者GET参数或者域名
### 捕获的参数总是字符串
每个捕获到的参数总是作为Python字符串发送到视图，不管正则表达式中所确定的类型
### 处理错误
django默认一些处理错误的模块

- handler400 django.conf.urld.handler400

- handler403 django.conf.urls.handler403
- handler404 django.cof.urls.handler404

- handler500 django.conf.urls.handler500

### include other URLconfs
在任何情况，你的urlpatterns可以'include'其它url

# 编写视图
一个view函数是一个携带web请求和返回的Python函数。实现一个页面的过程
- 编写view函数
- 指定url到view
- view函数处理错误
- 可选，定制错误的view视图
# 视图装饰器
django提供一些装饰器，可应用于html个一些特性
## 支持的http方法
django.views.decorators.http中的装饰器可以限制对视图访问的请求方式。如果条件不符合，他将返回[django.http.HttpResponseNotAllowed](https://docs.djangoproject.com/en/1.9/ref/request-response/#django.http.HttpResponseNotAllowed)

- require_http_methods

```
	from django.views.decoratord.http import require_http_methods
	@require_http_methods(['GET','POST'])
	#请求方式必须大写
	def my_view(request):
		#仅通过post或者get请求
		pass
```

- require_GET()

- reuire_POST()

- require_safe()

装饰器要求视图只接受GET和HEAD方法。 这些方法通常被认为是“安全的”，因为除了检索所请求的资源之外，它们不应该具有采取行动的意思。
## conditional view processing
接下来的在django.views.decorators.http中的装饰器可以控制个别视图的缓存

- condition(etag_func=None, last_modified_func=None)

- last_modified(last_modified_func)

These decorators can be used to generate ETag and Last-Modified headers; see conditional view processing.
## GZip compression
The decorators in django.views.decorators.gzip control content compression on a per-view basis.

### gzip_page()
This decorator compresses content if the browser allows gzip compression. It sets the Vary header accordingly, so that caches will base their storage on the Accept-Encoding header.

## vary headers
The decorators in django.views.decorators.vary can be used to control caching based on specific request headers.

### vary_on_cookie(func)
### vary_on_headers(*headers)
- The Vary header defines which request headers a cache mechanism should take into account when building its cache key.

- See using vary headers.

## Caching
The decorators in django.views.decorators.cache control server and client-side caching

### never_cache(view_func)
This decorator adds a Cache-Control: max-age=0, no-cache, no-store, must-revalidate header to a response to indicate that a page should never be cached.

# 文件上传[link](https://docs.djangoproject.com/en/1.9/topics/http/file-uploads/)

# django shortcut function

## render()
render(request, template_name, context=None, context_instance=_context_instance_undefined, content_type=None, status=None, current_app=_current_app_undefined, dirs=_dirs_undefined, using=None)

### 必须参数
- request(请求对象)

- 模板名称
### 可选参数
- context(添加到模板环境的字典数据)
- context_instance
- content_type
- status
- current_app
- using

## render_to_response()
render_to_response(template_name, context=None, context_instance=_context_instance_undefined, content_type=None, status=None, dirs=_dirs_undefined, using=None)
### 必须参数
- template_name
### 可选参数
- context
- context_instance
- content_type
- status
- using
## redirect()
## get_object_or_404()
## get_list_or_404()









