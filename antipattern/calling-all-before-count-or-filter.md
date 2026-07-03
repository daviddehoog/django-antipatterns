% calling `all()` before `count()` or `filter()` etc.
---
severity: 2
type: antipattern
typefa: "fas fa-ban"
tags: [queryset]
layers: [views, orm]
solinks: []
---

Often when one wants to determine how many objects there are in a Django model, they do so with:

<pre class="python"><code>from .models import Project

def my_view(request):
    project_count = Project.objects.all().count()
    return render(request, '<i>name-of-some-template.html</i>', {'count': project_count})</code></pre>

Or filter objects based on a given condition like so:

<pre class="python"><code>from .models import Project

def my_view(request):
    projects = Project.objects.all().filter(user=request.user)
    return render(request, '<i>name-of-some-template.html</i>', {'projects': projects})</code></pre>

# Why is it a problem?

When you call `all()` before `filter()`, you are actually constructing your queryset twice - when one will do. `filter()` returns a new queryset that alreadt contains objects matching the condition, so `all()` is not needed.

Similarly, calling `all()` before `count()` is not neccessary, because `count()` already does a `SELECT COUNT(*)` at the database level.

# What can be done to resolve the problem?

Simply get rid of the preceding `all()`. So:

<pre class="python"><code>from .models import Project

def my_view(request):
    project_count = Project.objects.count()
    return render(request, '<i>name-of-some-template.html</i>', {'count': project_count})</code></pre>

Or filter objects based on a given codition like so:

<pre class="python"><code>from .models import Project

def my_view(request):
    projects = Project.objects.filter(user=request.user)
    return render(request, '<i>name-of-some-template.html</i>', {'projects': projects})</code></pre>
 