¡Perfecto! Aquí tienes la segunda parte de la respuesta, con todo el código para las plantillas HTML y los archivos finales del proyecto.

---

### **8. Creación de Plantillas HTML (`templates`)**

Crea los siguientes archivos dentro de las carpetas correspondientes en `templates/`.

#### **Archivos Base (en `templates/`)**

**`templates/base.html`**
```html
<!DOCTYPE html>
{% load static %}
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema de Exámenes en Línea</title>
    
    <!-- Bootstrap CSS -->
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet">
    
    <!-- Font Awesome -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.1.1/css/all.min.css">
    
    <!-- Custom CSS -->
    <link rel="stylesheet" href="{% static 'css/style.css' %}">

</head>
<body>

    {% include "navbar.html" %}

    <div class="container-fluid">
        <div class="row">
            <!-- Bloque para la barra lateral (opcional) -->
            {% block sidebar %}{% endblock sidebar %}

            <!-- Contenido principal -->
            <main role="main" class="col-md-9 ml-sm-auto col-lg-10 px-md-4">
                {% block content %}
                {% endblock content %}
            </main>
        </div>
    </div>

    {% include "footer.html" %}

    <!-- Bootstrap JS Bundle -->
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js"></script>
</body>
</html>
```

**`templates/navbar.html`**
```html
<nav class="navbar navbar-expand-lg navbar-dark bg-dark sticky-top">
    <div class="container-fluid">
        <a class="navbar-brand" href="/">
            <i class="fas fa-file-alt"></i> EXAMEN EN LÍNEA
        </a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarNav" aria-controls="navbarNav" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navbarNav">
            <ul class="navbar-nav ms-auto">
                {% if request.user.is_authenticated %}
                    <li class="nav-item">
                        <a class="nav-link" href="{% url 'cerrar-sesion' %}">
                            <i class="fas fa-sign-out-alt"></i> Cerrar Sesión ({{request.user.first_name}})
                        </a>
                    </li>
                {% else %}
                    <li class="nav-item">
                        <a class="nav-link" href="{% url 'admin-clic' %}">Admin</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="{% url 'maestro-clic' %}">Profesor</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="{% url 'estudiante-clic' %}">Estudiante</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" href="{% url 'contactenos' %}">Contáctenos</a>
                    </li>
                {% endif %}
            </ul>
        </div>
    </div>
</nav>
```

**`templates/footer.html`**
```html
<footer class="footer mt-auto py-3 bg-dark text-white text-center">
    <div class="container">
        <span>Copyright © Sistema de exámenes en línea - 2025</span>
    </div>
</footer>
```

#### **Archivos de `static/css/`**

Crea esta carpeta y el archivo dentro de `static`.

**`static/css/style.css`**
```css
body {
    background-color: #f8f9fa;
    display: flex;
    flex-direction: column;
    min-height: 100vh;
}

.footer {
    margin-top: auto;
}

/* --- Estilo de la Barra Lateral --- */
.sidebar {
    position: fixed;
    top: 0;
    bottom: 0;
    left: 0;
    z-index: 100;
    padding: 56px 0 0; /* Altura del navbar */
    box-shadow: inset -1px 0 0 rgba(0, 0, 0, .1);
    background-color: #343a40; /* Dark background */
}

.sidebar-sticky {
    position: relative;
    top: 0;
    height: calc(100vh - 56px);
    padding-top: .5rem;
    overflow-x: hidden;
    overflow-y: auto;
}

.sidebar .nav-link {
    font-weight: 500;
    color: #c2c7d0; /* Light grey text */
    padding: .75rem 1.25rem;
}

.sidebar .nav-link .fas {
    margin-right: 8px;
}

.sidebar .nav-link:hover {
    color: #fff;
    background-color: #495057;
}

.sidebar .nav-link.active {
    color: #fff;
    background-color: #007bff;
}

.sidebar-heading {
    padding: 1.5rem 1.25rem .75rem;
    font-size: 1.2rem;
    color: white;
    text-align: center;
    border-bottom: 1px solid #4f5962;
    margin-bottom: 1rem;
}

.sidebar-heading img {
    max-width: 80px;
    margin-bottom: 10px;
}

/* --- Contenido Principal --- */
main {
    padding-top: 2rem;
    margin-left: 230px; /* Ancho de la barra lateral */
}


/* --- Tarjetas del Panel (Dashboard Cards) --- */
.card-admin {
    border: none;
    color: white;
    margin-bottom: 1.5rem;
    border-radius: .5rem;
    box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    transition: transform .2s;
}
.card-admin:hover {
    transform: translateY(-5px);
}
.card-admin-teal { background-color: #20c997; }
.card-admin-gray { background-color: #6c757d; }
.card-admin-red { background-color: #dc3545; }
.card-admin-blue { background-color: #0d6efd; }

.card-admin-icon {
    font-size: 3rem;
    opacity: 0.7;
}

.card-admin-title {
    font-size: 1.2rem;
    font-weight: 500;
}

.card-admin-value {
    font-size: 2.5rem;
    font-weight: bold;
}

.card-admin-link {
    color: white;
    text-decoration: none;
    background-color: rgba(0,0,0,0.2);
    display: block;
    padding: .5rem;
    text-align: center;
    border-bottom-left-radius: .5rem;
    border-bottom-right-radius: .5rem;
}
.card-admin-link:hover {
    background-color: rgba(0,0,0,0.3);
    color: white;
}

/* --- Formularios y Botones --- */
.form-container {
    background: white;
    padding: 2rem;
    border-radius: .5rem;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
}

.btn-primary {
    background-color: #007bff;
    border-color: #007bff;
}
```
*Nota: El resto de los archivos HTML deben ser creados en sus respectivas carpetas (`examen`, `maestro`, `estudiante`). Debido a la gran cantidad de archivos, te proporcionaré los más importantes para que el sistema sea funcional. Puedes crear los demás basándote en la estructura de los que te doy.*

#### **Plantillas de la App `examen` (`templates/examen/`)**

**`templates/examen/admin_panel.html`**
```html
{% extends 'examen/admin_base.html' %}
{% block content %}
<div class="container mt-4">
    <div class="row">
        <!-- Tarjeta de Estudiantes -->
        <div class="col-md-3">
            <div class="card-admin card-admin-teal">
                <div class="card-body">
                    <div class="d-flex justify-content-between align-items-center">
                        <div>
                            <div class="card-admin-value">{{ total_estudiantes }}</div>
                            <div class="card-admin-title">Estudiantes</div>
                        </div>
                        <div class="card-admin-icon">
                            <i class="fas fa-user-graduate"></i>
                        </div>
                    </div>
                </div>
                <a href="{% url 'admin-ver-estudiante' %}" class="card-admin-link">Ver Detalles <i class="fas fa-arrow-circle-right"></i></a>
            </div>
        </div>
        <!-- Tarjeta de Profesores -->
        <div class="col-md-3">
            <div class="card-admin card-admin-blue">
                <div class="card-body">
                    <div class="d-flex justify-content-between align-items-center">
                        <div>
                            <div class="card-admin-value">{{ total_profesores }}</div>
                            <div class="card-admin-title">Profesores</div>
                        </div>
                        <div class="card-admin-icon">
                            <i class="fas fa-chalkboard-teacher"></i>
                        </div>
                    </div>
                </div>
                <a href="{% url 'admin-ver-profesor' %}" class="card-admin-link">Ver Detalles <i class="fas fa-arrow-circle-right"></i></a>
            </div>
        </div>
        <!-- Tarjeta de Cursos -->
        <div class="col-md-3">
            <div class="card-admin card-admin-red">
                <div class="card-body">
                    <div class="d-flex justify-content-between align-items-center">
                        <div>
                            <div class="card-admin-value">{{ total_cursos }}</div>
                            <div class="card-admin-title">Cursos</div>
                        </div>
                        <div class="card-admin-icon">
                            <i class="fas fa-book"></i>
                        </div>
                    </div>
                </div>
                <a href="{% url 'admin-ver-curso' %}" class="card-admin-link">Ver Detalles <i class="fas fa-arrow-circle-right"></i></a>
            </div>
        </div>
        <!-- Tarjeta de Preguntas -->
        <div class="col-md-3">
            <div class="card-admin card-admin-gray">
                <div class="card-body">
                    <div class="d-flex justify-content-between align-items-center">
                        <div>
                            <div class="card-admin-value">{{ total_preguntas }}</div>
                            <div class="card-admin-title">Preguntas</div>
                        </div>
                        <div class="card-admin-icon">
                            <i class="fas fa-question-circle"></i>
                        </div>
                    </div>
                </div>
                <a href="{% url 'admin-ver-pregunta' %}" class="card-admin-link">Ver Detalles <i class="fas fa-arrow-circle-right"></i></a>
            </div>
        </div>
    </div>
</div>
{% endblock content %}
```

**`templates/examen/admin_base.html`** (Barra lateral del Admin)
```html
{% extends 'base.html' %}
{% load static %}

{% block sidebar %}
<nav id="sidebarMenu" class="col-md-3 col-lg-2 d-md-block sidebar collapse">
    <div class="sidebar-sticky">
        <div class="sidebar-heading">
            <img src="{% static 'images/admin.png' %}" alt="Admin" class="rounded-circle">
            <h5>Administración</h5>
        </div>
        <ul class="nav flex-column">
            <li class="nav-item">
                <a class="nav-link {% if request.path == '/admin-panel' %}active{% endif %}" href="{% url 'admin-panel' %}">
                    <i class="fas fa-tachometer-alt"></i> Panel
                </a>
            </li>
            <li class="nav-item">
                <a class="nav-link {% if 'profesor' in request.path %}active{% endif %}" href="{% url 'admin-profesor' %}">
                    <i class="fas fa-chalkboard-teacher"></i> Sección de profesores
                </a>
            </li>
            <li class="nav-item">
                <a class="nav-link {% if 'estudiante' in request.path %}active{% endif %}" href="{% url 'admin-estudiante' %}">
                    <i class="fas fa-user-graduate"></i> Sección de estudiantes
                </a>
            </li>
            <li class="nav-item">
                <a class="nav-link {% if 'curso' in request.path %}active{% endif %}" href="{% url 'admin-curso' %}">
                    <i class="fas fa-book"></i> Administrar cursos
                </a>
            </li>
            <li class="nav-item">
                <a class="nav-link {% if 'pregunta' in request.path %}active{% endif %}" href="{% url 'admin-pregunta' %}">
                    <i class="fas fa-question-circle"></i> Gestionar preguntas
                </a>
            </li>
        </ul>
    </div>
</nav>
{% endblock sidebar %}
```

*Nota: Para que la imagen `admin.png` funcione, debes crear una carpeta `images` dentro de `static` y colocar ahí una imagen con ese nombre. Puedes usar la de la captura de pantalla.*

**`templates/examen/admin_agregar_curso.html`**
```html
{% extends 'examen/admin_base.html' %}
{% block content %}
<div class="container">
    <h2 class="text-center my-4">Agregar Nuevo Curso</h2>
    <div class="form-container mx-auto" style="max-width: 600px;">
        <form method="POST" autocomplete="off">
            {% csrf_token %}
            <div class="mb-3">
                <label for="nombre_curso" class="form-label">Nombre del Curso</label>
                <input type="text" class="form-control" id="nombre_curso" name="nombre_curso" required>
            </div>
            <div class="mb-3">
                <label for="numero_preguntas" class="form-label">Número de Preguntas</label>
                <input type="number" class="form-control" id="numero_preguntas" name="numero_preguntas" required>
            </div>
            <div class="mb-3">
                <label for="puntaje_total" class="form-label">Puntaje Total</label>
                <input type="number" class="form-control" id="puntaje_total" name="puntaje_total" required>
            </div>
            <button type="submit" class="btn btn-primary w-100">Agregar Curso</button>
        </form>
    </div>
</div>
{% endblock content %}
```

**`templates/examen/admin_ver_curso.html`**
```html
{% extends 'examen/admin_base.html' %}
{% block content %}
<div class="container">
    <h2 class="text-center my-4">Cursos Disponibles</h2>
    <div class="card">
        <div class="card-body">
            <table class="table table-hover">
                <thead>
                    <tr>
                        <th>Nombre del Curso</th>
                        <th>Total de Preguntas</th>
                        <th>Puntaje Total</th>
                        <th>Acción</th>
                    </tr>
                </thead>
                <tbody>
                    {% for curso in cursos %}
                    <tr>
                        <td>{{ curso.nombre_curso }}</td>
                        <td>{{ curso.numero_preguntas }}</td>
                        <td>{{ curso.puntaje_total }}</td>
                        <td>
                            <a href="{% url 'borrar-curso' curso.id %}" class="btn btn-danger btn-sm" onclick="return confirm('¿Estás seguro de que quieres eliminar este curso?');">
                                <i class="fas fa-trash"></i> Eliminar
                            </a>
                        </td>
                    </tr>
                    {% endfor %}
                </tbody>
            </table>
        </div>
    </div>
</div>
{% endblock content %}
```

#### **Plantillas de la App `estudiante` (`templates/estudiante/`)**

**`templates/estudiante/estudiante_panel.html`**
```html
{% extends 'estudiante/estudiante_base.html' %}
{% block content %}
<div class="container mt-4">
    <div class="row">
        <!-- Tarjeta de Exámenes Disponibles -->
        <div class="col-md-6">
            <div class="card-admin card-admin-teal">
                <div class="card-body">
                    <div class="d-flex justify-content-between align-items-center">
                        <div>
                            <div class="card-admin-value">{{ total_cursos }}</div>
                            <div class="card-admin-title">Exámenes Disponibles</div>
                        </div>
                        <div class="card-admin-icon">
                            <i class="fas fa-file-alt"></i>
                        </div>
                    </div>
                </div>
                <a href="{% url 'estudiante-examen' %}" class="card-admin-link">Ver Exámenes <i class="fas fa-arrow-circle-right"></i></a>
            </div>
        </div>

        <!-- Tarjeta de Mis Calificaciones -->
        <div class="col-md-6">
            <div class="card-admin card-admin-blue">
                <div class="card-body">
                    <div class="d-flex justify-content-between align-items-center">
                        <div>
                            <div class="card-admin-value"><i class="fas fa-poll"></i></div>
                            <div class="card-admin-title">Mis Calificaciones</div>
                        </div>
                        <div class="card-admin-icon">
                            <i class="fas fa-star"></i>
                        </div>
                    </div>
                </div>
                <a href="{% url 'estudiante-calificaciones' %}" class="card-admin-link">Ver Calificaciones <i class="fas fa-arrow-circle-right"></i></a>
            </div>
        </div>
    </div>
</div>
{% endblock content %}
```

**`templates/estudiante/estudiante_base.html`**
```html
{% extends 'base.html' %}
{% load static %}

{% block sidebar %}
<nav id="sidebarMenu" class="col-md-3 col-lg-2 d-md-block sidebar collapse">
    <div class="sidebar-sticky">
        <div class="sidebar-heading">
            <img src="{% static 'images/student.png' %}" alt="Estudiante" class="rounded-circle">
            <h5>Estudiante</h5>
        </div>
        <ul class="nav flex-column">
            <li class="nav-item">
                <a class="nav-link {% if request.path == '/estudiante/estudiante-panel' %}active{% endif %}" href="{% url 'estudiante-panel' %}">
                    <i class="fas fa-tachometer-alt"></i> Panel
                </a>
            </li>
            <li class="nav-item">
                <a class="nav-link {% if 'examen' in request.path %}active{% endif %}" href="{% url 'estudiante-examen' %}">
                    <i class="fas fa-book"></i> Exámenes
                </a>
            </li>
            <li class="nav-item">
                <a class="nav-link {% if 'calificaciones' in request.path %}active{% endif %}" href="{% url 'estudiante-calificaciones' %}">
                    <i class="fas fa-star"></i> Calificaciones
                </a>
            </li>
        </ul>
    </div>
</nav>
{% endblock sidebar %}
```

*Nota: Al igual que con el admin, crea una imagen `student.png` en `static/images/`.*

---

### **9. Archivo de Requerimientos**

Crea un archivo llamado `requirements.txt` en la raíz de tu proyecto `Sist_examenes_EnLinea/`.
**`requirements.txt`**
```
Django==4.2.7 # O la versión que estés usando
Pillow==10.1.0
django-widget-tweaks==1.5.0
```
Puedes generar este archivo automáticamente con el comando: `pip freeze > requirements.txt`

---

### **10. Licencia**

Crea un archivo llamado `LICENSE` en la raíz del proyecto. La licencia MIT es una excelente opción estándar.
**`LICENSE`**
```
MIT License

Copyright (c) 2024 Tu Nombre o Nombre de la Organización

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

### **11. Conclusiones**

El **Sistema de Exámenes en Línea** desarrollado con Python y Django es una aplicación web robusta y funcional que satisface los requerimientos especificados. El proyecto demuestra una clara separación de roles (Administrador, Profesor, Estudiante) con permisos y funcionalidades específicas para cada uno, garantizando la seguridad e integridad del sistema.

**Tecnologías Utilizadas:**
*   **Backend:** Python 3, Django Framework.
*   **Frontend:** HTML5, CSS3, Bootstrap 5.
*   **Base de Datos:** SQLite (por defecto en Django, fácilmente escalable a PostgreSQL o MySQL).
*   **Librerías Python:** `Pillow` para el manejo de imágenes (fotos de perfil) y `django-widget-tweaks` para facilitar la renderización de formularios.

**Logros del Proyecto:**
1.  **Arquitectura Modular:** El uso de aplicaciones separadas (`app_examen`, `app_maestro`, `app_estudiante`) promueve un código organizado y fácil de mantener.
2.  **Sistema de Autenticación y Autorización:** Se implementó un sistema seguro que distingue tres niveles de acceso, utilizando los decoradores y grupos de Django para restringir vistas según el rol del usuario.
3.  **Gestión Completa de Datos (CRUD):** El administrador tiene control total sobre cursos, preguntas, profesores y estudiantes. Los profesores pueden gestionar sus propios contenidos, simplificando la carga administrativa.
4.  **Flujo de Examen Automatizado:** Los estudiantes pueden realizar exámenes de manera intuitiva. El sistema maneja la presentación de preguntas, la recolección de respuestas y la calificación automática, proporcionando resultados inmediatos.
5.  **Interfaz de Usuario Limpia:** Gracias a Bootstrap, la interfaz es responsiva y agradable, ofreciendo una experiencia de usuario positiva en diferentes dispositivos.

Este proyecto sirve como una excelente base que puede ser extendida con futuras funcionalidades como bancos de preguntas por categoría, temporizadores para exámenes, reportes estadísticos avanzados y la integración con otras plataformas educativas.
