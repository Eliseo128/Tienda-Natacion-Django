# Tienda-Natacion-Django
Próximos Pasos (para seguir mejorando) Página de Detalle del Producto: Haz que el botón "Ver Producto" lleve a una página individual para cada artículo. Formulario de Contacto: Crea la página de "Contacto" con un formulario funcional. Páginas Estáticas: Crea las plantillas y vistas para "Productos" (con todos los productos) 
¡Excelente! Crear una tienda online de natación con Django es un proyecto fantástico para aprender y poner en práctica los fundamentos del framework. Basándome en la imagen que me has proporcionado, te guiaré paso a paso, desde la instalación hasta tener una página de inicio funcional que se vea como la de la captura.

Este tutorial está diseñado para ser seguido en orden. ¡Empecemos!

---

### **Paso 0: Requisitos Previos**

Antes de empezar, asegúrate de tener instalado:
1.  **Python 3:** Puedes descargarlo desde [python.org](https://www.python.org/downloads/).
2.  **PIP:** El gestor de paquetes de Python (normalmente viene con Python).
3.  **Un editor de código:** Como Visual Studio Code, Sublime Text, o PyCharm.

---

### **Paso 1: Configuración del Entorno y Creación del Proyecto**

Es una buena práctica trabajar en un entorno virtual para cada proyecto.

1.  **Crea una carpeta para el proyecto y navega hacia ella:**
    ```bash
    mkdir proyecto_natacion
    cd proyecto_natacion
    ```

2.  **Crea y activa un entorno virtual:**
    *   En Windows:
        ```bash
        python -m venv venv
        .\venv\Scripts\activate
        ```
    *   En macOS/Linux:
        ```bash
        python3 -m venv venv
        source venv/bin/activate
        ```
    Verás `(venv)` al principio de la línea de tu terminal.

3.  **Instala Django y Pillow (para manejar imágenes):**
    ```bash
    pip install django pillow
    ```

4.  **Crea el proyecto de Django:**
    ```bash
    django-admin startproject tienda_natacion .
    ```
    (El `.` al final crea el proyecto en la carpeta actual).

5.  **Crea la aplicación para la tienda:**
    ```bash
    python manage.py startapp tienda
    ```

6.  **Registra la nueva aplicación en el proyecto:**
    Abre `tienda_natacion/settings.py` y añade `'tienda'` a la lista `INSTALLED_APPS`:

    ```python
    # tienda_natacion/settings.py
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'django.contrib.staticfiles',
        'tienda', # <-- Añade esta línea
    ]
    ```

---

### **Paso 2: Crear el Modelo de Datos (El Producto)**

Vamos a definir cómo se verá un producto en nuestra base de datos.

1.  **Edita el archivo `tienda/models.py`:**

    ```python
    # tienda/models.py
    from django.db import models

    class Producto(models.Model):
        nombre = models.CharField(max_length=200)
        descripcion = models.TextField(blank=True, null=True)
        precio = models.DecimalField(max_digits=10, decimal_places=2)
        imagen = models.ImageField(upload_to='productos/')

        def __str__(self):
            return self.nombre
    ```

2.  **Configura los archivos multimedia (imágenes):**
    Añade estas líneas al final de `tienda_natacion/settings.py` para que Django sepa dónde guardar y encontrar las imágenes que subas.

    ```python
    # tienda_natacion/settings.py
    import os

    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
    ```

3.  **Crea las migraciones y aplícalas:**
    Estos comandos crearán la tabla `Producto` en la base de datos.

    ```bash
    python manage.py makemigrations
    python manage.py migrate
    ```

---

### **Paso 3: Panel de Administración para Añadir Productos**

Para poder añadir productos fácilmente, vamos a usar el panel de administración de Django.

1.  **Crea un superusuario:**
    ```bash
    python manage.py createsuperuser
    ```
    Sigue las instrucciones para crear un nombre de usuario, email y contraseña.

2.  **Registra el modelo `Producto` en el admin:**
    Edita el archivo `tienda/admin.py`:

    ```python
    # tienda/admin.py
    from django.contrib import admin
    from .models import Producto

    admin.site.register(Producto)
    ```

3.  **Añade los productos:**
    *   Inicia el servidor de desarrollo: `python manage.py runserver`.
    *   Abre tu navegador y ve a `http://127.0.0.1:8000/admin/`.
    *   Inicia sesión con el superusuario que creaste.
    *   Busca la sección "Tienda" y haz clic en "Añadir" al lado de "Productos".
    *   **Crea los productos que se ven en la imagen.** Necesitarás descargar imágenes similares para cada uno.
        *   **Producto 1:** Tabla de Natación, $12.99
        *   **Producto 2:** Pull-Boy, $8.99
        *   **Producto 3:** Lentes de Natación, $14.99
        *   **Producto 4:** Aletas de Natación, $18.99

---

### **Paso 4: Crear la Vista y la URL**

Ahora vamos a crear la lógica para mostrar los productos en la página principal.

1.  **Crea la vista en `tienda/views.py`:**
    Esta vista obtendrá todos los productos de la base de datos y los enviará a una plantilla.

    ```python
    # tienda/views.py
    from django.shortcuts import render
    from .models import Producto

    def lista_productos(request):
        productos = Producto.objects.all()
        contexto = {'productos': productos}
        return render(request, 'tienda/lista_productos.html', contexto)
    ```

2.  **Crea las URLs:**
    *   Primero, un archivo de URLs para nuestra app `tienda`. Crea el archivo `tienda/urls.py`:
        ```python
        # tienda/urls.py
        from django.urls import path
        from . import views

        urlpatterns = [
            path('', views.lista_productos, name='lista_productos'),
        ]
        ```
    *   Ahora, conecta estas URLs al proyecto principal. Edita `tienda_natacion/urls.py`:
        ```python
        # tienda_natacion/urls.py
        from django.contrib import admin
        from django.urls import path, include
        from django.conf import settings
        from django.conf.urls.static import static

        urlpatterns = [
            path('admin/', admin.site.urls),
            path('', include('tienda.urls')), # <-- Añade esta línea
        ]

        # Esto es para servir archivos de medios en desarrollo
        if settings.DEBUG:
            urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
        ```

---

### **Paso 5: Crear las Plantillas (Templates) y el Diseño (CSS)**

Esta es la parte visual. Recrearemos el diseño de la imagen.

1.  **Crea la estructura de carpetas para las plantillas y los archivos estáticos:**
    ```
    proyecto_natacion/
    ├── tienda/
    │   ├── templates/
    │   │   └── tienda/
    │   │       ├── base.html
    │   │       └── lista_productos.html
    │   └── static/
    │       └── tienda/
    │           └── css/
    │               └── styles.css
    ├── ...
    ```

2.  **Configura los archivos estáticos en `tienda_natacion/settings.py`:**
    Añade esta línea al final del archivo.
    ```python
    # tienda_natacion/settings.py
    STATIC_URL = '/static/'
    STATICFILES_DIRS = [os.path.join(BASE_DIR, 'tienda/static')]
    ```

3.  **Crea la plantilla base (`base.html`):**
    Esta plantilla contendrá la estructura común (header, footer, etc.).
    *   Crea el archivo `tienda/templates/tienda/base.html`:
    ```html
    {% load static %}
    <!DOCTYPE html>
    <html lang="es">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Tienda de Natación Online</title>
        <link rel="stylesheet" href="{% static 'tienda/css/styles.css' %}">
    </head>
    <body>
        <header>
            <div class="logo">
                <!-- Puedes poner tu logo aquí. Usaré un texto por ahora -->
                <img src="https://i.imgur.com/8Q5R6kR.png" alt="Logo Swimming" style="height: 50px;">
            </div>
            <nav>
                <a href="#">Inicio</a>
                <a href="#">Productos</a>
                <a href="#">Contacto</a>
                <a href="#">Nosotros</a>
            </nav>
        </header>

        <main class="container">
            {% block content %}
            {% endblock %}
        </main>

    </body>
    </html>
    ```

4.  **Crea la plantilla de la lista de productos (`lista_productos.html`):**
    Esta hereda de `base.html` y muestra los productos.
    *   Crea el archivo `tienda/templates/tienda/lista_productos.html`:

    ```html
    {% extends 'tienda/base.html' %}
    
    {% block content %}
    <div class="hero">
        <h1>Tienda de Natación Online</h1>
        <p>Explora nuestros productos</p>
    </div>

    <section class="featured-products">
        <h2>Productos Destacados</h2>
        <div class="product-grid">
            {% for producto in productos %}
            <div class="product-card">
                <img src="{{ producto.imagen.url }}" alt="{{ producto.nombre }}">
                <h3>{{ producto.nombre }}</h3>
                <p class="description">{{ producto.descripcion }}</p>
                <p class="price">${{ producto.precio }}</p>
                <a href="#" class="btn-product">Ver Producto</a>
            </div>
            {% endfor %}
        </div>
    </section>
    {% endblock %}
    ```

5.  **Crea el archivo CSS para el estilo (`styles.css`):**
    *   Crea el archivo `tienda/static/tienda/css/styles.css`:

    ```css
    /* Estilos Generales */
    body {
        font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, "Helvetica Neue", Arial, sans-serif;
        margin: 0;
        background: linear-gradient(to bottom right, #4CAF50, #2E8B57);
        color: #333;
    }

    .container {
        max-width: 1200px;
        margin: 0 auto;
        padding: 20px;
    }

    /* Header y Navegación */
    header {
        display: flex;
        justify-content: space-between;
        align-items: center;
        padding: 10px 40px;
        background-color: rgba(0, 0, 0, 0.1);
    }

    header nav a {
        color: white;
        text-decoration: none;
        margin-left: 20px;
        font-weight: bold;
    }

    /* Hero Section */
    .hero {
        text-align: center;
        color: white;
        padding: 40px 20px;
    }
    .hero h1 {
        font-size: 3em;
        margin-bottom: 10px;
    }
    .hero p {
        font-size: 1.2em;
    }

    /* Productos Destacados */
    .featured-products h2 {
        font-size: 2.5em;
        text-align: left;
        margin-bottom: 30px;
        padding-left: 20px;
        color: #f0f0f0;
    }

    /* Grid de Productos */
    .product-grid {
        display: grid;
        grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
        gap: 25px;
    }

    /* Tarjeta de Producto */
    .product-card {
        background-color: #f4f4f4;
        border-radius: 15px;
        box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
        text-align: center;
        padding: 20px;
        display: flex;
        flex-direction: column;
        justify-content: space-between;
    }
    .product-card img {
        max-width: 100%;
        height: 180px;
        object-fit: contain;
        margin-bottom: 15px;
    }
    .product-card h3 {
        font-size: 1.4em;
        margin: 10px 0;
    }
    .product-card .description {
        font-size: 0.9em;
        color: #666;
        min-height: 40px; /* Para alinear los elementos de abajo */
    }
    .product-card .price {
        font-size: 1.5em;
        font-weight: bold;
        color: #333;
        margin: 15px 0;
    }
    .btn-product {
        background-color: #6c757d;
        color: white;
        padding: 10px 20px;
        border: none;
        border-radius: 5px;
        text-decoration: none;
        cursor: pointer;
        transition: background-color 0.3s;
    }
    .btn-product:hover {
        background-color: #5a6268;
    }
    ```

---

### **Paso 6: ¡Ver el Resultado!**

1.  Asegúrate de haber añadido los productos con sus imágenes a través del panel de admin.
2.  Inicia el servidor de desarrollo si no está corriendo:
    ```bash
    python manage.py runserver
    ```
3.  Abre tu navegador y visita `http://127.0.0.1:8000/`.

¡Deberías ver una página de inicio muy similar a la de tu imagen, mostrando los productos que añadiste a la base de datos!

### **Próximos Pasos (para seguir mejorando)**

*   **Página de Detalle del Producto:** Haz que el botón "Ver Producto" lleve a una página individual para cada artículo.
*   **Formulario de Contacto:** Crea la página de "Contacto" con un formulario funcional.
*   **Páginas Estáticas:** Crea las plantillas y vistas para "Productos" (con todos los productos) y "Nosotros".
*   **Mejorar el Diseño:** Haz el sitio web responsivo para que se vea bien en móviles.
*   **Carrito de Compras:** ¡El gran paso! Añadir la funcionalidad de un carrito para una tienda completa.
