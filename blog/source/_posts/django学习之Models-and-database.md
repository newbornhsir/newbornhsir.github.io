---
title: django学习之Models-and-database
date: 2017-06-09 18:03:52
tags: django
categories: python
---

# Models and database（待完成）
django的models和database学习。在mysite目录下执行`python manage.py startapp modelsanddatabase`,在setting.py的INSTALLED_APPS中添加`'modelsanddatabase.apps.ModelsanddatabaseConfig',`

## Model
模型是单一明确的信息数据来源。它包含基本的字段和你所存储数据的行为。通常，每个模型匹配一个数据表。</br>
基本要素：

- 每个model都是一个Python类，并且是django.db.models.Model的子类
- 每个modle的属性代表一个数据库字段
- django有自动生成数据的API。详情见[Making queries](https://docs.djangoproject.com/en/1.9/topics/db/queries/)

Example:(modelsanddatabase/models.py)
```
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```
将modelsanddatabase添加到settings.py中，执行`python manage.py makemigrations modelsanddatabase`和`python manage.py migrate`。将看到建表提示和添加提示。若想在admin中显示，需在modelsanddatabase/admin.py中注册

## Field
字段是model中最重要的一部分，字段命名不能与models API冲突

1 字段类型

每个字段应该是一个合适字段类的实例[ model field reference](https://docs.djangoproject.com/en/1.9/ref/models/fields/#model-field-types)。django使用字段类型处理以下事情：

-	告诉数据库存储类型
-	当渲染from表单是使用默认的HTML组件
-	用于django管理和自动生成form表单的最小验证需求

2 字段选项

每个字段都需要一组特定于字段的参数。例如CharField需要max_length参数去指定可变字符串数据的存储长度。

3 自动生成的主键
