---
layout: post
title:  "djangoform"
date:   2020-01-02 11:11:19
categories:  Django
tags: Django
excerpt: 
mathjax: true
---





xadmin的超级管理员重设：createsuperuser   


后端传参给前端   
后端代码，其中RepoUpgrade是model名：
```
def repo_list(request):
    result = RepoUpgrade.objects.all()
    return render(request, 'repo_list.html', {"result": result})

```
前端代码：
```
    {% for item in result %}
    <p>{{ item }}</p>
    {% endfor %}
```

通过ModelForm自动生成表单：
```
class RepoForm(forms.ModelForm):
    class Meta:
        model = RepoUpgrade
        fields = ['repo_name', 'repo_desc', 'repo_type', 'repo_source_type', 'repo_source_address', 'repo_user', 'repo_password', 'server_path', 'server_ip']
        widgets = {
            'repo_name': forms.TextInput(attrs={'class': 'form-control', 'style': 'width:450px;'}),
            'repo_desc': forms.TextInput(attrs={'class': 'form-control', 'style': 'width:450px; height:100px'}),
            'repo_type': forms.Select(attrs={'class': 'form-control', 'style': 'width:450px;'}),
            'repo_source_type': forms.Select(attrs={'class': 'form-control', 'style': 'width:450px;'}),
            'repo_source_address': forms.TextInput(attrs={'class': 'form-control', 'style': 'width:450px;'}),
            'repo_user': forms.TextInput(attrs={'class': 'form-control', 'style': 'width:450px;'}),
            'repo_password': forms.TextInput(attrs={'class': 'form-control', 'type': 'password', 'style': 'width:450px'}),
            'server_path': forms.TextInput(attrs={'class': 'form-control', 'style': 'width:450px;'}),
            'server_ip': forms.TextInput(attrs={'class': 'form-control', 'style': 'width:450px;'}),
        }

def repo_add(request):
    """add new repo"""
    if request.method != 'POST':
        # didn't submit data: create a new form
        form = RepoForm  # RepoForm is imported from ./form.py
    else:
        form = RepoForm(request.POST)
        if form.is_valid():
            form.save()
            # redirect user to topics page
            return HttpResponseRedirect(reverse(''))
           # return HttpResponseRedirect(reverse('learning_logs:topics'))

    context = {'form': form}
    return render(request, 'repo_add.html', context)
```

前端代码：
```
{%extends 'base.html' %}
{% block page_title %}
    test
{% endblock %}

{%block page_header%}
    <h1 class="page-header">版本发布配置</h1>
{%endblock%}

{%block page_content%}
    <form action="/repo_add/" method="post">
        {% csrf_token %}
        {{ form.as_p }}
        <input type="submit" class="btn btn-success" value="提交">
    </form>
{%endblock%}
```
extends引入html的扩展模板文件，这样如果前端有改动，不用每个页面都修改    
block是在模板文件指定块位置插入数据    
