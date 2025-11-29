"# marketplace_main" 

# Djanfo forms

```Python 
from django import forms
from django.contrib.auth.forms import UserCreationForm, AuthenticationForm
from django.contrib.auth.models import User

from .models import Item

class LoginForm(AuthenticationForm):
    username = forms.CharField(widget=forms.TextInput(
        attrs={
            'placeholder': 'Tu username',
            'class': 'form-control'
        }
    ))

    password = forms.CharField(widget=forms.PasswordInput(
        attrs={
            'placeholder': 'Tu password',
            'class': 'form-control'
        }
    ))

class SignupForm(UserCreationForm):
    class Meta:
        model = User
        fields = ['username', 'email', 'password1', 'password2' ]

    username = forms.CharField(widget=forms.TextInput(
        attrs={
            'placeholder': 'Tu Username',
            'class': 'form-control'
        }
    ))

    email = forms.CharField(widget=forms.EmailInput(
        attrs={
            'placeholder': 'Tu Email',
            'class': 'form-control'
        }
    ))

    password1 = forms.CharField(widget=forms.PasswordInput(
        attrs={
            'placeholder': 'Password',
            'class': 'form-control'
        }
    ))

    password2 = forms.CharField(widget=forms.PasswordInput(
        attrs={
            'placeholder': 'Repite Tu Password',
            'class': 'form-control'
        }
    ))
```

# Registro de usuarios en views.py

```Python
from django.shortcuts import render, get_object_or_404, redirect
from django.contrib.auth import logout
from .models import Item, Category

from .forms import SignupForm
 

# Create your views here.
def home(request):
    items = Item.objects.filter(is_sold=False)
    categories = Category.objects.all()

    context = {
        'items': items,
        'categories': categories
    }
    return render(request, 'store/home.html', context)

def contact(request):
    context = {
        'msg': 'Quieres otros productos contactame!'
    }

    return render(request, 'store/contact.html', context)

def detail(request, pk):
    item = get_object_or_404(Item, pk=pk)
    related_items = Item.objects.filter(category=item.category, 
                                        is_sold=False).exclude(pk=pk)[0:3]

    context={
        'item': item,
        'related_items': related_items
    }

    return render(request, 'store/item.html', context)

def register(request):
    if request.method == 'POST':
        form = SignupForm(request.POST)

        if form.is_valid():
            form.save()
            return redirect('login')
    else:
        form = SignupForm()
    
    context = {
        'form': form
    }

    return render(request, 'store/signup.html', context) 
```

# Template de login 

```Python
{% extends 'store/base.html' %}

{% block title %}Login | {% endblock %}

{% block content %}
<div class="row p-4">
    <div class="col-6 bg-light p-4">
        <h4 class="mb-6 text-center">Login</h4>
        <hr>
        <form action="." method="POST">
            {% csrf_token %}
            <div class="form-floating mb-3">
                <h6>Username:</h6>
                {{ form.username }}
            </div>
 
            <div class="form-floating mb-3">
                <h6>Password:</h6>
                {{ form.password }}
            </div>

            {% if form.errors or form.non_field_errors %}
                <div class="mb-4 p-6 bg-danger">
                    {% for field in form %}
                        {{fiels.errors}}
                    {% endfor %}

                    {{ form.non_field_errors }}
                </div>
            {% endif %}
            <button class="btn btn-primary mb-6">Login</button>

        </form>
    </div>
</div>
{% endblock %}
```

# Template de register 

```Python
{% extends 'store/base.html' %}

{% block title %}Registro | {% endblock %}

{% block content %}
<div class="row p-4">
    <div class="col-6 bg-light p-4">
        <h4 class="mb-6 text-center">Registro</h4>
        <hr>
        <form action="." method="POST">
            {% csrf_token %}
            <div class="form-floating mb-3">
                <h6>Username:</h6>
                {{ form.username }}
            </div>
            <div class="form-floating mb-3">
                <h6>Email:</h6>
                {{ form.email }}
            </div>
            <div class="form-floating mb-3">
                <h6>Password:</h6>
                {{ form.password1 }}
            </div>
            <div class="form-floating mb-3">
                <h6>Repite Password:</h6>
                {{ form.password2 }}
            </div>

            {% if form.errors or form.non_field_errors %}
                <div class="mb-4 p-6 bg-danger">
                    {% for field in form %}
                        {{fiels.errors}}
                    {% endfor %}

                    {{ form.non_field_errors }}
                </div>
            {% endif %}
            <button class="btn btn-primary mb-6">Register</button>

        </form>
    </div>
</div>
{% endblock %}
```

# Rutas en URL's para login y registro de usuarios

```Python
from django.urls import path
from django.contrib.auth import views as auth_views

from .views import contact, detail, register

from .forms import LoginForm

urlpatterns = [
    path('contact/', contact, name='contact'),
    path('register/', register, name='register'),
    path('login/', auth_views.LoginView.as_view(template_name='store/login.html', authentication_form=LoginForm), name='login'),
    path('detail/<int:pk>/', detail, name='detail'),
]
```


# Práctica Evaluatoria — Marketplace (Django)

## Forms.py (LoginForm, SignupForm, NewItemForm)

En las clases recientes se trabajó en el archivo `forms.py` de la aplicación store dentro del proyecto marketplace_main. Este archivo funciona como la frontera entre lo que el usuario escribe y lo que llega al sistema. Los formularios validan, organizan y moldean la información antes de procesarla.

### Código: forms.py

```python
from django import forms
from django.contrib.auth.forms import AuthenticationForm, UserCreationForm
from .models import Item

class LoginForm(AuthenticationForm):
    username = forms.CharField(
        widget=forms.TextInput(attrs={
            "placeholder": "Nombre de usuario",
            "class": "form-control"
        })
    )
    password = forms.CharField(
        widget=forms.PasswordInput(attrs={
            "placeholder": "Contraseña",
            "class": "form-control"
        })
    )

class SignupForm(UserCreationForm):
    username = forms.CharField(
        widget=forms.TextInput(attrs={
            "placeholder": "Nombre de usuario",
            "class": "form-control"
        })
    )
    email = forms.EmailField(
        widget=forms.EmailInput(attrs={
            "placeholder": "Correo electrónico",
            "class": "form-control"
        })
    )
    password1 = forms.CharField(
        widget=forms.PasswordInput(attrs={
            "placeholder": "Contraseña",
            "class": "form-control"
        })
    )
    password2 = forms.CharField(
        widget=forms.PasswordInput(attrs={
            "placeholder": "Repetir contraseña",
            "class": "form-control"
        })
    )

class NewItemForm(forms.ModelForm):
    class Meta:
        model = Item
        fields = ("name", "description", "price", "category", "image")
        widgets = {
            "name": forms.TextInput(attrs={
                "class": "form-control",
                "placeholder": "Nombre del producto"
            }),
            "description": forms.Textarea(attrs={
                "class": "form-control",
                "placeholder": "Descripción"
            }),
            "price": forms.NumberInput(attrs={
                "class": "form-control",
                "placeholder": "Precio"
            }),
            "category": forms.Select(attrs={"class": "form-control"}),
            "image": forms.FileInput(attrs={"class": "form-control"})
        }
```

---

## Views.py (login, logout_user, detail, add_item)

Estas funciones controlan la lógica principal del sistema: iniciar sesión, cerrar sesión, mostrar detalles de productos y agregar nuevos artículos.

### Código: views.py

```python
from django.shortcuts import render, redirect, get_object_or_404
from django.contrib.auth import login, logout
from django.contrib.auth.decorators import login_required
from .forms import LoginForm, SignupForm, NewItemForm
from .models import Item

def login_view(request):
    if request.method == "POST":
        form = LoginForm(data=request.POST)
        if form.is_valid():
            user = form.get_user()
            login(request, user)
            return redirect("index")
    else:
        form = LoginForm()
    return render(request, "login.html", {"form": form})

def logout_user(request):
    logout(request)
    return redirect("login")

def detail(request, pk):
    item = get_object_or_404(Item, pk=pk)
    return render(request, "item.html", {"item": item})

@login_required
def add_item(request):
    if request.method == "POST":
        form = NewItemForm(request.POST, request.FILES)
        if form.is_valid():
            item = form.save()
            return redirect("detail", pk=item.pk)
    else:
        form = NewItemForm()
    return render(request, "form.html", {"form": form})
```

---

## Decorador @login_required

Se usa para restringir vistas a usuarios autenticados. Si un usuario intenta entrar sin iniciar sesión, es redirigido automáticamente al login. Evita repetir validaciones manuales.

---

## urls.py (rutas del proyecto)

Conecta cada acción con su vista correspondiente.

### Código: urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    path("login/", views.login_view, name="login"),
    path("logout/", views.logout_user, name="logout"),
    path("item/<int:pk>/", views.detail, name="detail"),
    path("add-item/", views.add_item, name="add_item"),
]
```

---

## store/templates (item.html, login.html, signup.html, navigation.html, form.html)

Plantillas que muestran la parte visual del sistema.

### Código: item.html

```html
<h1>{{ item.name }}</h1>
<p>Precio: {{ item.price }}</p>
<p>{{ item.description }}</p>
<img src="{{ item.image.url }}" alt="Imagen del producto">
```

---

### Código: login.html

```html
<h2>Iniciar Sesión</h2>
<form method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Entrar</button>
</form>
```

---

### Código: signup.html

```html
<h2>Crear Cuenta</h2>
<form method="POST">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Registrarse</button>
</form>
```

---

### Código: navigation.html

```html
<nav>
    <a href="/">Inicio</a>
    {% if user.is_authenticated %}
        <a href="/add-item/">Agregar artículo</a>
        <a href="/logout/">Cerrar sesión</a>
    {% else %}
        <a href="/login/">Login</a>
        <a href="/signup/">Registro</a>
    {% endif %}
</nav>
```

---

### Código: form.html

```html
<h2>Formulario</h2>
<form method="POST" enctype="multipart/form-data">
    {% csrf_token %}
    {{ form.as_p }}
    <button type="submit">Guardar</button>
</form>
```
