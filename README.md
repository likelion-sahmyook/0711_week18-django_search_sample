# 멋쟁이 사자처럼 7기 세션

### 장고 ORM queryset 검색기능 구현

1. 검색기능 구현(search)

- home.html
```python
    <form class="form-inline" action="{% url 'search' %}" method="get">
        <div class="input-group">
            <select name="search_filter" class="form-control" style="margin-right: 10px;">
                <option value="title">제목</option>
                <option value="body">내용</option>
            </select>
            <input type="text" class="form-control" name='q' style="margin-right: 10px;" required="true">
            <button class="btn btn-secondary" type="submit">검색</button>
        </div>
    </form>
```

- urls.py
```python
urlpatterns = [

    path('search', views.search, name="search"),

]
```

- views.py
```python
def search(request):
    if request.GET.get('q'):
            que = request.GET.get('q')
            variable_column = request.GET.get('search_filter')
            search_type = 'contains'
            filter = variable_column + '__' + search_type
            posts = Post.objects.filter(**{ filter: request.GET.get('q') }).order_by('-pub_date') 
    else:
        return redirect('home')
    
    return render(request, 'result.html', {'posts': posts, 'que': que})
```


2. 검색결과 페이지(result)

- result.html
```python
{% extends 'base.html' %}
{% load staticfiles %}
{% block content %}

<div class="container">
    <h1>"{{que}}"검색결과 ({{posts.count}})</h1>
    
    {% if posts.count == 0 %}
        <div style="text-align: center; margin: 50px auto" >
            <h2>검색결과가 없습니다! :-)</h2>
        </div>
        
    {% endif %}
    
    {% for post in posts %}
        <div class="col-md-12">
            <a href="{% url 'detail' post.id %}" class="card-link">
            <div class="card">
                <div class="card-body">
                    <h5 class="card-title">{{post.title}}</h5>
                    <p class="card-text">작성자 : {{post.writer}} | {{post.pub_date}}</p>
                </div>
            </div>
            </a>    
        </div>
    {% endfor %}
    
</div>
{% endblock %}
```

- urls.py
```python
urlpatterns = [
    path('search', views.search, name="search"),
    path('result', views.result, name="result"),
]
```

- views.py
```python
def result(request):
    return render(request, 'result.html')
```

