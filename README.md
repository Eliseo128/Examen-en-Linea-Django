# Examen-en-Linea-Django
Proyecto en django
¡Excelente! He analizado en detalle tu solicitud, incluyendo la descripción funcional, los modelos, las URLs y las imágenes. Con toda esta información, puedo generar el proyecto completo "Sistema de Exámenes en Línea" siguiendo tus especificaciones.

A continuación, te proporciono una guía paso a paso para crear el proyecto desde cero, seguida de todo el código necesario para cada archivo.

---

### **1. Creando el Proyecto (Guía de Configuración)**

Sigue estos pasos en tu terminal o línea de comandos.

**Paso 1: Crear la carpeta del proyecto**
```bash
mkdir Sist_examenes_EnLinea
cd Sist_examenes_EnLinea
```

**Paso 2: Crear y activar el entorno virtual**
*   **En Windows:**
    ```bash
    python -m venv venv
    .\venv\Scripts\activate
    ```
*   **En macOS/Linux:**
    ```bash
    python3 -m venv venv
    source venv/bin/activate
    ```

**Paso 3: Instalar Django y las dependencias**
```bash
pip install django pillow django-widget-tweaks
```

**Paso 4: Crear el proyecto Django**
(Asegúrate de incluir el punto `.` al final para crearlo en el directorio actual)
```bash
django-admin startproject Examen_en_linea .
```

**Paso 5: Crear las aplicaciones**
```bash
python manage.py startapp app_examen
python manage.py startapp app_estudiante
python manage.py startapp app_maestro
```

**Paso 6: Crear las carpetas `static` y `templates`**
Dentro de la carpeta raíz `Sist_examenes_EnLinea`, crea estas carpetas:
```bash
mkdir templates
mkdir static
```
Dentro de la carpeta `templates`, crea las subcarpetas:
```bash
cd templates
mkdir examen
mkdir estudiante
mkdir maestro
cd ..
```
La estructura de tu proyecto debería verse así:
```
Sist_examenes_EnLinea/
├── Examen_en_linea/
├── app_examen/
├── app_estudiante/
├── app_maestro/
├── static/
├── templates/
│   ├── examen/
│   ├── estudiante/
│   └── maestro/
├── manage.py
└── venv/
```

---

### **2. Configuración del Proyecto**

**`Examen_en_linea/settings.py`**

Abre este archivo y realiza las siguientes modificaciones:

1.  **Registra las aplicaciones y `widget_tweaks`:**
    ```python
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',

        # Mis aplicaciones
        'app_examen',
        'app_estudiante',
        'app_maestro',

        # Dependencias
        'widget_tweaks',
    ]
    ```

2.  **Configura la carpeta de plantillas (`templates`):**
    Busca la variable `TEMPLATES` y modifica la línea `DIRS`:
    ```python
    TEMPLATES = [
        {
            'BACKEND': 'django.template.backends.django.DjangoTemplates',
            'DIRS': [os.path.join(BASE_DIR, 'templates')], # <-- Añade esta línea
            'APP_DIRS': True,
            'OPTIONS': {
                'context_processors': [
                    'django.template.context_processors.debug',
                    'django.template.context_processors.request',
                    'django.contrib.auth.context_processors.auth',
                    'django.contrib.messages.context_processors.messages',
                ],
            },
        },
    ]
    ```

3.  **Configura los archivos estáticos y de medios (`static` y `media`):**
    Añade esto al final del archivo.
    ```python
    # Configuración de archivos estáticos
    STATIC_URL = '/static/'
    STATICFILES_DIRS = [
        os.path.join(BASE_DIR, 'static'),
    ]

    # Configuración de archivos de medios (para fotos de perfil)
    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR, 'media')

    # URLs de redirección de inicio de sesión
    LOGIN_REDIRECT_URL = '/despues-inicio-sesion'
    LOGIN_URL = 'admin-iniciar-sesion'
    ```

---

### **3. Creación de Modelos (models.py)**

Copia el siguiente contenido en los archivos `models.py` de cada aplicación.

**`app_estudiante/models.py`**
```python
from django.db import models
from django.contrib.auth.models import User

class Estudiante(models.Model):
    usuario = models.OneToOneField(User, on_delete=models.CASCADE)
    foto_perfil = models.ImageField(upload_to='profile_pic/Estudiante/', null=True, blank=True)
    direccion = models.CharField(max_length=40)
    telefono = models.CharField(max_length=20, null=False)

    @property
    def get_nombre(self):
        return self.usuario.first_name + " " + self.usuario.last_name

    @property
    def get_instancia(self):
        return self

    def __str__(self):
        return self.usuario.first_name
```

**`app_maestro/models.py`**
```python
from django.db import models
from django.contrib.auth.models import User

class Profesor(models.Model):
    usuario = models.OneToOneField(User, on_delete=models.CASCADE)
    foto_perfil = models.ImageField(upload_to='profile_pic/Profesor/', null=True, blank=True)
    direccion = models.CharField(max_length=40)
    telefono = models.CharField(max_length=20, null=False)
    estado = models.BooleanField(default=False)
    salario = models.PositiveIntegerField(null=True)

    @property
    def get_nombre(self):
        return self.usuario.first_name + " " + self.usuario.last_name

    @property
    def get_instancia(self):
        return self

    def __str__(self):
        return self.usuario.first_name
```

**`app_examen/models.py`**
*(Nota: He corregido la referencia en `Resultado` para que apunte correctamente a `app_estudiante.Estudiante`)*.
```python
from django.db import models
from app_estudiante.models import Estudiante

class Curso(models.Model):
    nombre_curso = models.CharField(max_length=50)
    numero_preguntas = models.PositiveIntegerField()
    puntaje_total = models.PositiveIntegerField()

    def __str__(self):
        return self.nombre_curso

class Pregunta(models.Model):
    curso = models.ForeignKey(Curso, on_delete=models.CASCADE)
    puntaje = models.PositiveIntegerField()
    pregunta = models.CharField(max_length=600)
    opcion1 = models.CharField(max_length=200)
    opcion2 = models.CharField(max_length=200)
    opcion3 = models.CharField(max_length=200)
    opcion4 = models.CharField(max_length=200)
    
    categoria = (
        ('opcion1', 'Opción 1'),
        ('opcion2', 'Opción 2'),
        ('opcion3', 'Opción 3'),
        ('opcion4', 'Opción 4'),
    )
    respuesta = models.CharField(max_length=200, choices=categoria)
    
    def __str__(self):
        return self.pregunta

class Resultado(models.Model):
    estudiante = models.ForeignKey(Estudiante, on_delete=models.CASCADE)
    examen = models.ForeignKey(Curso, on_delete=models.CASCADE)
    puntaje_obtenido = models.PositiveIntegerField()
    fecha = models.DateTimeField(auto_now=True)

    def __str__(self):
        return f"{self.estudiante} - {self.examen}: {self.puntaje_obtenido}"
```

---

### **4. Migraciones**

Ahora que los modelos están definidos, crea y aplica las migraciones.

```bash
python manage.py makemigrations
python manage.py migrate
```

### **5. Creación de un Superusuario**

Necesitarás un administrador para acceder al panel.

```bash
python manage.py createsuperuser
```
Sigue las instrucciones para crear tu cuenta de administrador.

---

### **6. Configuración de URLs (`urls.py`)**

**`Examen_en_linea/urls.py`** (Archivo principal de URLs)
*Copia y reemplaza el contenido de este archivo.*
```python
from django.contrib import admin
from django.urls import path, include
from app_examen import views
from django.contrib.auth.views import LogoutView, LoginView
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('maestro/', include('app_maestro.urls')),
    path('estudiante/', include('app_estudiante.urls')),

    path('', views.home_vista, name=''),
    path('cerrar-sesion', LogoutView.as_view(template_name='examen/logout.html'), name='cerrar-sesion'),
    path('contactenos', views.contactenos_vista, name='contactenos'),
    path('despues-inicio-sesion', views.despues_inicio_sesion_vista, name='despues-inicio-sesion'),

    # URLs del administrador
    path('admin-clic', views.admin_clic_vista, name='admin-clic'),
    path('admin-iniciar-sesion', LoginView.as_view(template_name='examen/adminlogin.html'), name='admin-iniciar-sesion'),
    path('admin-panel', views.admin_panel_vista, name='admin-panel'),
    
    # Gestión de profesores
    path('admin-profesor', views.admin_profesor_vista, name='admin-profesor'),
    path('admin-ver-profesor', views.admin_ver_profesor_vista, name='admin-ver-profesor'),
    path('actualizar-profesor/<int:pk>', views.actualizar_profesor_vista, name='actualizar-profesor'),
    path('borrar-profesor/<int:pk>', views.borrar_profesor_vista, name='borrar-profesor'),
    path('admin-ver-profesor-pendiente', views.admin_ver_profesor_pendiente_vista, name='admin-ver-profesor-pendiente'),
    path('admin-ver-salario-profesor', views.admin_ver_salario_profesor_vista, name='admin-ver-salario-profesor'),
    path('aprobar-profesor/<int:pk>', views.aprobar_profesor_vista, name='aprobar-profesor'),
    path('rechazar-profesor/<int:pk>', views.rechazar_profesor_vista, name='rechazar-profesor'),

    # Gestión de estudiantes
    path('admin-estudiante', views.admin_estudiante_vista, name='admin-estudiante'),
    path('admin-ver-estudiante', views.admin_ver_estudiante_vista, name='admin-ver-estudiante'),
    path('admin-ver-calificaciones-estudiante', views.admin_ver_calificaciones_estudiante_vista, name='admin-ver-calificaciones-estudiante'),
    path('ver-calificaciones/<int:pk>', views.ver_calificaciones_vista, name='ver-calificaciones'),
    path('actualizar-estudiante/<int:pk>', views.actualizar_estudiante_vista, name='actualizar-estudiante'),
    path('borrar-estudiante/<int:pk>', views.borrar_estudiante_vista, name='borrar-estudiante'),

    # Gestión de cursos
    path('admin-curso', views.admin_curso_vista, name='admin-curso'),
    path('admin-agregar-curso', views.admin_agregar_curso_vista, name='admin-agregar-curso'),
    path('admin-ver-curso', views.admin_ver_curso_vista, name='admin-ver-curso'),
    path('borrar-curso/<int:pk>', views.borrar_curso_vista, name='borrar-curso'),

    # Gestión de preguntas
    path('admin-pregunta', views.admin_pregunta_vista, name='admin-pregunta'),
    path('admin-agregar-pregunta', views.admin_agregar_pregunta_vista, name='admin-agregar-pregunta'),
    path('admin-ver-pregunta', views.admin_ver_pregunta_vista, name='admin-ver-pregunta'),
    path('ver-pregunta/<int:pk>', views.ver_pregunta_vista, name='ver-pregunta'),
    path('borrar-pregunta/<int:pk>', views.borrar_pregunta_vista, name='borrar-pregunta'),
]

# Para servir archivos de medios en desarrollo
if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

**`app_maestro/urls.py`**
*Crea este archivo dentro de la carpeta `app_maestro`.*
```python
from django.urls import path
from . import views
from django.contrib.auth.views import LoginView

urlpatterns = [
    path('maestro-clic', views.maestro_clic_vista, name='maestro-clic'),
    path('maestro-iniciar-sesion', LoginView.as_view(template_name='maestro/maestrologin.html'), name='maestro-iniciar-sesion'),
    path('maestro-registrarse', views.maestro_registrarse_vista, name='maestro-registrarse'),
    path('maestro-panel', views.maestro_panel_vista, name='maestro-panel'),
    path('maestro-ver-curso', views.maestro_ver_curso_vista, name='maestro-ver-curso'),
    path('maestro-agregar-curso', views.maestro_agregar_curso_vista, name='maestro-agregar-curso'),
    path('borrar-curso-maestro/<int:pk>', views.borrar_curso_maestro_vista, name='borrar-curso-maestro'),
    path('maestro-ver-pregunta', views.maestro_ver_pregunta_vista, name='maestro-ver-pregunta'),
    path('maestro-agregar-pregunta', views.maestro_agregar_pregunta_vista, name='maestro-agregar-pregunta'),
    path('borrar-pregunta-maestro/<int:pk>', views.borrar_pregunta_maestro_vista, name='borrar-pregunta-maestro'),
]
```

**`app_estudiante/urls.py`**
*Crea este archivo dentro de la carpeta `app_estudiante`.*
```python
from django.urls import path
from . import views
from django.contrib.auth.views import LoginView

urlpatterns = [
    path('estudiante-clic', views.estudiante_clic_vista, name='estudiante-clic'),
    path('estudiante-iniciar-sesion', LoginView.as_view(template_name='estudiante/estudiantelogin.html'), name='estudiante-iniciar-sesion'),
    path('estudiante-registrarse', views.estudiante_registrarse_vista, name='estudiante-registrarse'),
    path('estudiante-panel', views.estudiante_panel_vista, name='estudiante-panel'),
    path('estudiante-examen', views.estudiante_examen_vista, name='estudiante-examen'),
    path('realizar-examen/<int:pk>', views.realizar_examen_vista, name='realizar-examen'),
    path('iniciar-examen/<int:pk>', views.iniciar_examen_vista, name='iniciar-examen'),
    path('calcular-calificaciones', views.calcular_calificaciones_vista, name='calcular-calificaciones'),
    path('ver-resultado', views.ver_resultado_vista, name='ver-resultado'),
    path('verificar-calificaciones', views.verificar_calificaciones_vista, name='verificar-calificaciones'),
    path('estudiante-calificaciones', views.estudiante_calificaciones_vista, name='estudiante-calificaciones'),
]
```

---

### **7. Lógica de Vistas (`views.py`)**

Aquí está el corazón de la aplicación. Copia el código en los archivos `views.py` correspondientes.

*Debido a la gran cantidad de código, lo dividiré en secciones. Si esto excede el límite de caracteres, lo continuaré en una segunda respuesta.*

**`app_examen/views.py` (Vistas del Administrador)**
```python
from django.shortcuts import render, redirect, reverse
from django.contrib.auth.decorators import login_required, user_passes_test
from django.contrib.auth.models import Group, User
from django.http import HttpResponseRedirect
from django.db.models import Sum

from app_estudiante.models import Estudiante
from app_maestro.models import Profesor
from .models import Curso, Pregunta, Resultado

# --- Vistas de Autenticación y Navegación ---

def home_vista(request):
    if request.user.is_authenticated:
        return HttpResponseRedirect('despues-inicio-sesion')
    return render(request, 'examen/index.html')

def es_admin(user):
    return user.is_superuser

def despues_inicio_sesion_vista(request):
    if es_admin(request.user):
        return redirect('admin-panel')
    elif request.user.groups.filter(name='PROFESOR').exists():
        profesor = Profesor.objects.get(usuario_id=request.user.id)
        if profesor.estado:
            return redirect('maestro-panel')
        else:
            # Aquí puedes redirigir a una página de "pendiente de aprobación"
            return render(request, 'maestro/maestro_espera.html')
    elif request.user.groups.filter(name='ESTUDIANTE').exists():
        return redirect('estudiante-panel')
    return redirect('admin-iniciar-sesion') # Fallback por si acaso

def admin_clic_vista(request):
    if request.user.is_authenticated:
        return HttpResponseRedirect('despues-inicio-sesion')
    return HttpResponseRedirect('admin-iniciar-sesion')

def contactenos_vista(request):
    return render(request, 'examen/contactenos.html')

# --- Panel de Administrador ---

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_panel_vista(request):
    dict = {
        'total_estudiantes': Estudiante.objects.all().count(),
        'total_profesores': Profesor.objects.all().count(),
        'total_cursos': Curso.objects.all().count(),
        'total_preguntas': Pregunta.objects.all().count(),
    }
    return render(request, 'examen/admin_panel.html', context=dict)

# --- Gestión de Profesores por el Admin ---

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_profesor_vista(request):
    dict = {
        'total_profesores': Profesor.objects.all().count(),
        'profesores_pendientes': Profesor.objects.filter(estado=False).count(),
        'salario_total': Profesor.objects.filter(estado=True).aggregate(Sum('salario'))['salario__sum'] or 0,
    }
    return render(request, 'examen/admin_profesor.html', context=dict)

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_ver_profesor_vista(request):
    profesores = Profesor.objects.filter(estado=True)
    return render(request, 'examen/admin_ver_profesor.html', {'profesores': profesores})

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_ver_profesor_pendiente_vista(request):
    profesores = Profesor.objects.filter(estado=False)
    return render(request, 'examen/admin_ver_profesor_pendiente.html', {'profesores': profesores})

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def aprobar_profesor_vista(request, pk):
    profesor = Profesor.objects.get(id=pk)
    profesor.estado = True
    profesor.save()
    return redirect(reverse('admin-ver-profesor-pendiente'))

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def rechazar_profesor_vista(request, pk):
    profesor = Profesor.objects.get(id=pk)
    usuario = User.objects.get(id=profesor.usuario_id)
    usuario.delete()
    profesor.delete()
    return redirect('admin-ver-profesor-pendiente')

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_ver_salario_profesor_vista(request):
    profesores = Profesor.objects.filter(estado=True)
    return render(request, 'examen/admin_ver_salario_profesor.html', {'profesores': profesores})

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def actualizar_profesor_vista(request, pk):
    profesor = Profesor.objects.get(id=pk)
    usuario = User.objects.get(id=profesor.usuario_id)
    if request.method == 'POST':
        usuario.first_name = request.POST.get('first_name')
        usuario.last_name = request.POST.get('last_name')
        usuario.username = request.POST.get('username')
        profesor.direccion = request.POST.get('direccion')
        profesor.telefono = request.POST.get('telefono')
        profesor.salario = request.POST.get('salario')
        usuario.save()
        profesor.save()
        return redirect('admin-ver-profesor')
    context = {'profesor': profesor, 'usuario': usuario}
    return render(request, 'examen/actualizar_profesor.html', context)

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def borrar_profesor_vista(request, pk):
    profesor = Profesor.objects.get(id=pk)
    usuario = User.objects.get(id=profesor.usuario_id)
    usuario.delete()
    profesor.delete()
    return redirect('admin-ver-profesor')

# --- Gestión de Estudiantes por el Admin ---

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_estudiante_vista(request):
    dict = {
        'total_estudiantes': Estudiante.objects.all().count(),
    }
    return render(request, 'examen/admin_estudiante.html', context=dict)

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_ver_estudiante_vista(request):
    estudiantes = Estudiante.objects.all()
    return render(request, 'examen/admin_ver_estudiante.html', {'estudiantes': estudiantes})

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def actualizar_estudiante_vista(request, pk):
    estudiante = Estudiante.objects.get(id=pk)
    usuario = User.objects.get(id=estudiante.usuario_id)
    if request.method == 'POST':
        usuario.first_name = request.POST.get('first_name')
        usuario.last_name = request.POST.get('last_name')
        usuario.username = request.POST.get('username')
        estudiante.direccion = request.POST.get('direccion')
        estudiante.telefono = request.POST.get('telefono')
        usuario.save()
        estudiante.save()
        return redirect('admin-ver-estudiante')
    context = {'estudiante': estudiante, 'usuario': usuario}
    return render(request, 'examen/actualizar_estudiante.html', context)

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def borrar_estudiante_vista(request, pk):
    estudiante = Estudiante.objects.get(id=pk)
    usuario = User.objects.get(id=estudiante.usuario_id)
    usuario.delete()
    estudiante.delete()
    return redirect('admin-ver-estudiante')

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_ver_calificaciones_estudiante_vista(request):
    estudiantes = Estudiante.objects.all()
    return render(request, 'examen/admin_ver_calificaciones_estudiante.html', {'estudiantes': estudiantes})

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def ver_calificaciones_vista(request, pk):
    estudiante = Estudiante.objects.get(id=pk)
    resultados = Resultado.objects.filter(estudiante=estudiante)
    return render(request, 'examen/ver_calificaciones.html', {'estudiante': estudiante, 'resultados': resultados})

# --- Gestión de Cursos por el Admin ---

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_curso_vista(request):
    return render(request, 'examen/admin_curso.html')

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_agregar_curso_vista(request):
    if request.method == 'POST':
        nombre_curso = request.POST.get('nombre_curso')
        numero_preguntas = request.POST.get('numero_preguntas')
        puntaje_total = request.POST.get('puntaje_total')
        curso = Curso(nombre_curso=nombre_curso, numero_preguntas=numero_preguntas, puntaje_total=puntaje_total)
        curso.save()
        return redirect('admin-ver-curso')
    return render(request, 'examen/admin_agregar_curso.html')

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_ver_curso_vista(request):
    cursos = Curso.objects.all()
    return render(request, 'examen/admin_ver_curso.html', {'cursos': cursos})

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def borrar_curso_vista(request, pk):
    curso = Curso.objects.get(id=pk)
    curso.delete()
    return redirect('admin-ver-curso')

# --- Gestión de Preguntas por el Admin ---

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_pregunta_vista(request):
    return render(request, 'examen/admin_pregunta.html')

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_agregar_pregunta_vista(request):
    cursos = Curso.objects.all()
    if request.method == 'POST':
        curso_id = request.POST.get('curso')
        curso = Curso.objects.get(id=curso_id)
        
        pregunta = Pregunta()
        pregunta.curso = curso
        pregunta.pregunta = request.POST.get('pregunta')
        pregunta.puntaje = request.POST.get('puntaje')
        pregunta.opcion1 = request.POST.get('opcion1')
        pregunta.opcion2 = request.POST.get('opcion2')
        pregunta.opcion3 = request.POST.get('opcion3')
        pregunta.opcion4 = request.POST.get('opcion4')
        pregunta.respuesta = request.POST.get('respuesta')
        pregunta.save()
        return redirect('admin-ver-pregunta')
    return render(request, 'examen/admin_agregar_pregunta.html', {'cursos': cursos})

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def admin_ver_pregunta_vista(request):
    cursos = Curso.objects.all()
    return render(request, 'examen/admin_ver_pregunta.html', {'cursos': cursos})

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def ver_pregunta_vista(request, pk):
    curso = Curso.objects.get(id=pk)
    preguntas = Pregunta.objects.filter(curso=curso)
    return render(request, 'examen/ver_pregunta.html', {'preguntas': preguntas, 'curso': curso})

@login_required(login_url='admin-iniciar-sesion')
@user_passes_test(es_admin)
def borrar_pregunta_vista(request, pk):
    pregunta = Pregunta.objects.get(id=pk)
    curso_id = pregunta.curso.id
    pregunta.delete()
    return redirect('ver-pregunta', pk=curso_id)

```

**`app_maestro/views.py`**
```python
from django.shortcuts import render, redirect
from django.contrib.auth.models import User, Group
from django.contrib.auth.decorators import login_required, user_passes_test
from django.http import HttpResponseRedirect
from .models import Profesor
from app_examen.models import Curso, Pregunta

# --- Vistas de Autenticación y Navegación ---
def maestro_clic_vista(request):
    if request.user.is_authenticated:
        return HttpResponseRedirect('despues-inicio-sesion')
    return render(request, 'maestro/maestro_clic.html')

def maestro_registrarse_vista(request):
    if request.method == 'POST':
        # Crear usuario
        first_name = request.POST.get('first_name')
        last_name = request.POST.get('last_name')
        username = request.POST.get('username')
        password = request.POST.get('password')
        usuario = User.objects.create_user(
            username=username, password=password, first_name=first_name, last_name=last_name)
        
        # Asignar a grupo PROFESOR
        grupo_profesor, created = Group.objects.get_or_create(name='PROFESOR')
        usuario.groups.add(grupo_profesor)
        
        # Crear perfil de Profesor
        profesor = Profesor()
        profesor.usuario = usuario
        profesor.direccion = request.POST.get('direccion')
        profesor.telefono = request.POST.get('telefono')
        profesor.salario = request.POST.get('salario')
        profesor.save()

        return redirect('maestro-iniciar-sesion')
    return render(request, 'maestro/maestro_registrarse.html')

def es_profesor_aprobado(user):
    return user.groups.filter(name='PROFESOR').exists() and Profesor.objects.get(usuario_id=user.id).estado

@login_required(login_url='maestro-iniciar-sesion')
@user_passes_test(es_profesor_aprobado)
def maestro_panel_vista(request):
    dict = {
        'total_cursos': Curso.objects.all().count(),
        'total_preguntas': Pregunta.objects.all().count()
    }
    return render(request, 'maestro/maestro_panel.html', context=dict)

# --- Gestión de Cursos y Preguntas por el Profesor ---

@login_required(login_url='maestro-iniciar-sesion')
@user_passes_test(es_profesor_aprobado)
def maestro_ver_curso_vista(request):
    cursos = Curso.objects.all()
    return render(request, 'maestro/maestro_ver_curso.html', {'cursos': cursos})

@login_required(login_url='maestro-iniciar-sesion')
@user_passes_test(es_profesor_aprobado)
def maestro_agregar_curso_vista(request):
    if request.method == 'POST':
        nombre_curso = request.POST.get('nombre_curso')
        numero_preguntas = request.POST.get('numero_preguntas')
        puntaje_total = request.POST.get('puntaje_total')
        curso = Curso(nombre_curso=nombre_curso, numero_preguntas=numero_preguntas, puntaje_total=puntaje_total)
        curso.save()
        return redirect('maestro-ver-curso')
    return render(request, 'maestro/maestro_agregar_curso.html')

@login_required(login_url='maestro-iniciar-sesion')
@user_passes_test(es_profesor_aprobado)
def borrar_curso_maestro_vista(request, pk):
    curso = Curso.objects.get(id=pk)
    curso.delete()
    return redirect('maestro-ver-curso')

@login_required(login_url='maestro-iniciar-sesion')
@user_passes_test(es_profesor_aprobado)
def maestro_ver_pregunta_vista(request):
    cursos = Curso.objects.all()
    return render(request, 'maestro/maestro_ver_pregunta.html', {'cursos': cursos})

@login_required(login_url='maestro-iniciar-sesion')
@user_passes_test(es_profesor_aprobado)
def maestro_agregar_pregunta_vista(request):
    cursos = Curso.objects.all()
    if request.method == 'POST':
        curso_id = request.POST.get('curso')
        curso = Curso.objects.get(id=curso_id)
        
        pregunta = Pregunta()
        pregunta.curso = curso
        pregunta.pregunta = request.POST.get('pregunta')
        pregunta.puntaje = request.POST.get('puntaje')
        pregunta.opcion1 = request.POST.get('opcion1')
        pregunta.opcion2 = request.POST.get('opcion2')
        pregunta.opcion3 = request.POST.get('opcion3')
        pregunta.opcion4 = request.POST.get('opcion4')
        pregunta.respuesta = request.POST.get('respuesta')
        pregunta.save()
        return redirect('maestro-ver-pregunta')
    return render(request, 'maestro/maestro_agregar_pregunta.html', {'cursos': cursos})

@login_required(login_url='maestro-iniciar-sesion')
@user_passes_test(es_profesor_aprobado)
def borrar_pregunta_maestro_vista(request, pk):
    pregunta = Pregunta.objects.get(id=pk)
    pregunta.delete()
    return redirect('maestro-ver-pregunta')

```
**`app_estudiante/views.py`**
```python
from django.shortcuts import render, redirect
from django.contrib.auth.models import User, Group
from django.contrib.auth.decorators import login_required, user_passes_test
from django.http import HttpResponseRedirect
from .models import Estudiante
from app_examen.models import Curso, Pregunta, Resultado

# --- Vistas de Autenticación y Navegación ---
def estudiante_clic_vista(request):
    if request.user.is_authenticated:
        return HttpResponseRedirect('despues-inicio-sesion')
    return render(request, 'estudiante/estudiante_clic.html')

def estudiante_registrarse_vista(request):
    if request.method == 'POST':
        first_name = request.POST.get('first_name')
        last_name = request.POST.get('last_name')
        username = request.POST.get('username')
        password = request.POST.get('password')
        usuario = User.objects.create_user(
            username=username, password=password, first_name=first_name, last_name=last_name)
        
        grupo_estudiante, created = Group.objects.get_or_create(name='ESTUDIANTE')
        usuario.groups.add(grupo_estudiante)

        estudiante = Estudiante()
        estudiante.usuario = usuario
        estudiante.direccion = request.POST.get('direccion')
        estudiante.telefono = request.POST.get('telefono')
        estudiante.save()
        return redirect('estudiante-iniciar-sesion')
    return render(request, 'estudiante/estudiante_registrarse.html')

def es_estudiante(user):
    return user.groups.filter(name='ESTUDIANTE').exists()

@login_required(login_url='estudiante-iniciar-sesion')
@user_passes_test(es_estudiante)
def estudiante_panel_vista(request):
    dict = {
        'total_cursos': Curso.objects.all().count(),
        'total_preguntas': Pregunta.objects.all().count(),
    }
    return render(request, 'estudiante/estudiante_panel.html', context=dict)

# --- Lógica del Examen para el Estudiante ---

@login_required(login_url='estudiante-iniciar-sesion')
@user_passes_test(es_estudiante)
def estudiante_examen_vista(request):
    cursos = Curso.objects.all()
    return render(request, 'estudiante/estudiante_examen.html', {'cursos': cursos})

@login_required(login_url='estudiante-iniciar-sesion')
@user_passes_test(es_estudiante)
def realizar_examen_vista(request, pk):
    curso = Curso.objects.get(id=pk)
    estudiante = Estudiante.objects.get(usuario_id=request.user.id)
    
    # Verificar si el estudiante ya realizó este examen
    if Resultado.objects.filter(estudiante=estudiante, examen=curso).exists():
        return render(request, 'estudiante/examen_ya_realizado.html')

    return render(request, 'estudiante/realizar_examen.html', {'curso': curso})

@login_required(login_url='estudiante-iniciar-sesion')
@user_passes_test(es_estudiante)
def iniciar_examen_vista(request, pk):
    curso = Curso.objects.get(id=pk)
    preguntas = Pregunta.objects.filter(curso=curso).order_by('?') # Orden aleatorio
    return render(request, 'estudiante/iniciar_examen.html', {'curso': curso, 'preguntas': preguntas})

@login_required(login_url='estudiante-iniciar-sesion')
@user_passes_test(es_estudiante)
def calcular_calificaciones_vista(request):
    if request.method == 'POST':
        estudiante = Estudiante.objects.get(usuario_id=request.user.id)
        curso_id = request.POST.get('curso_id')
        curso = Curso.objects.get(id=curso_id)

        puntaje_obtenido = 0
        preguntas = Pregunta.objects.filter(curso=curso)

        for pregunta in preguntas:
            respuesta_enviada = request.POST.get(str(pregunta.id))
            # Construye el nombre de la opción correcta a partir del modelo
            respuesta_correcta = getattr(pregunta, pregunta.respuesta)
            
            # Comparamos la opción enviada con el texto de la opción correcta
            if respuesta_enviada == respuesta_correcta:
                puntaje_obtenido += pregunta.puntaje
        
        resultado = Resultado()
        resultado.estudiante = estudiante
        resultado.examen = curso
        resultado.puntaje_obtenido = puntaje_obtenido
        resultado.save()

        return redirect('ver-resultado')
    return redirect('estudiante-examen')

@login_required(login_url='estudiante-iniciar-sesion')
@user_passes_test(es_estudiante)
def ver_resultado_vista(request):
    estudiante = Estudiante.objects.get(usuario_id=request.user.id)
    cursos = Curso.objects.all()
    return render(request, 'estudiante/ver_resultado.html', {'cursos': cursos, 'estudiante': estudiante})

@login_required(login_url='estudiante-iniciar-sesion')
@user_passes_test(es_estudiante)
def verificar_calificaciones_vista(request):
    estudiante = Estudiante.objects.get(usuario_id=request.user.id)
    resultados = Resultado.objects.filter(estudiante=estudiante)
    return render(request, 'estudiante/verificar_calificaciones.html', {'resultados': resultados})

@login_required(login_url='estudiante-iniciar-sesion')
@user_passes_test(es_estudiante)
def estudiante_calificaciones_vista(request):
    estudiante = Estudiante.objects.get(usuario_id=request.user.id)
    resultados = Resultado.objects.filter(estudiante=estudiante)
    return render(request, 'estudiante/estudiante_calificaciones.html', {'resultados': resultados})

```

**Debido al límite de caracteres, el código de las plantillas HTML, el archivo de requerimientos, la licencia y las conclusiones se proporcionarán en la siguiente respuesta.**
