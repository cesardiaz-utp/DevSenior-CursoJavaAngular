# Construcción de Aplicaciones Inteligentes con Spring Boot, Angular & AI

## [Clase 1](Clase1.md): Introducción al Backend y la Arquitectura Empresarial

**Objetivo**: Sentar las bases del desarrollo backend con Spring Boot y mostrar cómo la IA puede agilizar la creación de APIs.

**Contenido**:

- **Introducción a Spring Boot**:
  - ¿Qué es Spring Boot? (énfasis en la simplicidad y la convención sobre la configuración).
  - Ventajas de usar Spring Boot para el desarrollo rápido de aplicaciones.
  - Configuración inicial de un proyecto con Spring Initializr.
- **Arquitectura Empresarial**:
  - Conceptos clave: capas de la aplicación (presentación, negocio, datos).
  - El patrón MVC (Modelo-Vista-Controlador) y su relación con una API REST.
  - Diseño de APIs RESTful: principios y mejores prácticas (verbos HTTP, códigos de estado, etc.).
- **Creación de una API RESTful con PostgreSQL usando IA**:
  - **Práctica Guiada**: Generación de código para una entidad (por ejemplo, `Producto` o `Estudiante`) con la ayuda de una herramienta de IA (como un generador de código).
  - **Configuración de la conexión a la base de datos**: Uso de `application.properties` para PostgreSQL.
  - **Creación de las capas**:
    - **Entidad (`Entity`)**: Definición del modelo de datos.
    - **Repositorio (`Repository`)**: Uso de JPA para la persistencia de datos.
    - **Servicio (`Service`)**: Lógica de negocio.
    - **Controlador (`Controller`)**: Exposición de los endpoints RESTful (GET, POST, PUT, DELETE).
  - **Demo**: Realizar una prueba de los endpoints con una herramienta como Postman.

## [Clase 2](Clase2.md): Desarrollo Frontend con Angular

**Objetivo**: Introducir los conceptos esenciales de Angular y mostrar cómo la IA puede acelerar la creación de componentes de la interfaz de usuario.

**Contenido**:

- **Introducción al Desarrollo Web**:
  - Breve repaso de los fundamentos: HTML (estructura), CSS (estilo) y JavaScript (interactividad).
  - ¿Por qué necesitamos frameworks como Angular?
- **Introducción a SPA, Angular y TypeScript**:
  - **SPA (Single Page Application)**: Concepto y ventajas.
  - **Angular**: Características principales y por qué es una elección popular para SPAs.
  - **TypeScript**: Introducción al tipado estático y cómo mejora el desarrollo.
- **Estructura y Tipos de Elementos**:
  - **Módulos**: Organización de la aplicación.
  - **Componentes**: El bloque fundamental de construcción de Angular (HTML, CSS y lógica).
  - **Modelos**: Definición de estructuras de datos.
  - **Servicios**: Lógica compartida e inyección de dependencias.
- **Creación de Componentes con IA**:
  - **Creación de una pantalla de listado**: Generación de un componente que consume y muestra datos de una API.
  - **Práctica Guiada**: Uso de la IA para generar el HTML (*ngFor) y el CSS para mostrar una tabla o una lista de elementos.
  - **Creación de un formulario de captura**: Generación de un componente para agregar o editar información.
  - **Práctica Guiada**: Uso de la IA para generar un formulario reactivo con validaciones básicas.

## [Clase 3](Clase3.md): Integración, Seguridad y Mejores Prácticas

**Objetivo**: Conectar el frontend y el backend, asegurar la aplicación y finalizar con recomendaciones clave para un desarrollo profesional.

**Contenido**:

- **Integración entre Backend y Frontend**:
  - **CORS (Cross-Origin Resource Sharing)**: ¿Qué es y por qué es necesario?
  - Configuración en Spring Boot para permitir peticiones desde el frontend de Angular.
  - **Consumo de APIs HTTP en Angular**: Uso del HttpClient para realizar peticiones (GET, POST, PUT, DELETE).
- **Asegurando APIs**:
  - **Backend (Spring Security)**:
    - Introducción a Spring Security y su importancia.
    - Conceptos clave: autenticación y autorización.
    - Configuración básica para proteger los endpoints.
  - **Frontend (Angular)**:
    - Estrategias de seguridad: manejo de tokens (JWT).
    - Uso de `interceptors` para añadir tokens a las peticiones HTTP.
    - Proteger las rutas de la aplicación con `guards`.
- **Mejores Prácticas**:
  - **Manejo de errores**: Capturar y mostrar errores de manera amigable al usuario.
  - **Código limpio**: Estructura del código, nombres de variables, comentarios.
  - **Mantenimiento y escalabilidad**: Consideraciones para el crecimiento de la aplicación.
  - **Futuro de la IA en el desarrollo de software**: Discusión sobre cómo la IA continuará transformando el sector.
