# Clase 1: Introducción al Backend y la Arquitectura Empresarial

En esta primera sesión, sentaremos las bases de nuestra aplicación inteligente, centrándonos en la parte **backend**. Exploraremos por qué **Spring Boot** es una herramienta esencial para el desarrollo moderno y cómo podemos estructurar nuestras aplicaciones de manera profesional y escalable.

## 0. Requisitos y Herramientas

Para seguir este curso, necesitarás tener instaladas algunas herramientas esenciales. Una configuración adecuada desde el principio te ahorrará mucho tiempo y problemas.

### a. JDK (Java Development Kit)

El JDK es el kit de desarrollo de Java. Es indispensable para compilar y ejecutar aplicaciones Java. Piensa en el JDK como la "caja de herramientas" que te permite escribir y ejecutar código Java.

- **Instalación**: Descarga la versión de [Adoptium JDK](https://adoptium.net/es/temurin/releases) o de [Oracle JDK]. Se recomienda la versión 17 o superior, ya que son versiones de soporte a largo plazo (LTS). Sigue las instrucciones de instalación para tu sistema operativo (Windows, macOS o Linux).
- **Verificación**: Abre tu terminal o línea de comandos y escribe `java -version`. Deberías ver la versión de Java instalada. Esto confirma que el sistema operativo puede encontrar la herramienta de Java.

### b. Visual Studio Code (VSCode)

Aunque puedes usar otros IDEs (como IntelliJ IDEA o Eclipse), VSCode es ligero y muy popular en el desarrollo web. Es altamente extensible, lo que lo hace perfecto para nuestro proyecto.

- **Instalación**: Descarga e instala [Visual Studio Code](https://code.visualstudio.com/).
- **Extensiones recomendadas**:
  - **Extension Pack for Java**: Es una colección de extensiones que incluye herramientas clave como un depurador, un gestor de proyectos Maven, y soporte para el lenguaje Java. Simplifica la gestión de dependencias y el ciclo de vida del proyecto.
  - **Spring Boot Extension Pack**: Proporciona un soporte avanzado para proyectos Spring Boot, incluyendo atajos para crear archivos, sugerencias de código para propiedades de Spring y una vista de los beans de la aplicación.

### c. Maven

Maven es una herramienta de automatización de construcción. Simplifica la gestión de dependencias, la compilación de código, y la generación de paquetes de la aplicación. En lugar de descargar cada biblioteca manualmente, Maven se encarga de todo por nosotros.

- **Instalación**: Si usas VSCode con la extensión de Java, Maven suele estar incluido. Si no, puedes descargarlo desde el [sitio oficial](https://maven.apache.org/download.cgi).
- **Verificación**: En la terminal, ejecuta `mvn -version`.

### d. PostgreSQL

PostgreSQL es un potente sistema de gestión de bases de datos relacionales, conocido por su fiabilidad y robustez. Lo usaremos para almacenar los datos de nuestra aplicación.

- **Instalación**: Descarga el instalador desde el [sitio oficial de PostgreSQL](https://www.postgresql.org/download/). Sigue las instrucciones para tu sistema operativo.
- **Herramienta de gestión**: Se recomienda instalar también **pgAdmin**, una herramienta gráfica que viene con el instalador de PostgreSQL, para gestionar y visualizar fácilmente las bases de datos.

## 1. Introducción a Spring Boot

### ¿Qué es Spring Boot?

**Spring Boot** es un _framework_ de código abierto que simplifica la creación de aplicaciones Java. Es una extensión del popular _framework_ Spring, pero con un enfoque en la **convención sobre la configuración**. Esto significa que hace la mayor parte del trabajo pesado por nosotros, permitiéndonos concentrarnos en la lógica de negocio en lugar de en la configuración tediosa.

**Ventajas clave**:

- **Desarrollo rápido y sencillo**: Reduce drásticamente el tiempo de configuración. Por ejemplo, si Spring Boot detecta una dependencia de base de datos como `PostgreSQL Driver`, automáticamente configura un `DataSource` por nosotros, sin que tengamos que escribir una línea de código para ello.
- **Servidor embebido**: Incluye un servidor web (como Tomcat) listo para usar, lo que elimina la necesidad de descargar, configurar y desplegar la aplicación en un servidor externo. Simplemente construyes tu aplicación y la ejecutas como un archivo JAR.
- **Dependencias simplificadas**: Gestiona las dependencias de manera automática con "starter dependencies" (ej. `spring-boot-starter-web`). Estos "starters" son conjuntos de dependencias que agrupan todas las bibliotecas comunes necesarias para un tipo de proyecto específico. Esto evita que tengas que buscar y gestionar cada una de las dependencias manualmente.

### Creación de un proyecto con Spring Initializr

La forma más fácil de comenzar es a través de [Spring Initializr](https://start.spring.io/). Esta herramienta web genera el esqueleto de un proyecto con las dependencias y la estructura de directorios necesarias.

**Selecciones recomendadas para esta clase**:

- **Project: Maven o Gradle**. Son los gestores de dependencias y herramientas de construcción más populares. Maven es muy común y fácil de entender.
- **Language: Java**.
- **Spring Boot**: La última versión estable.
- **Project Metadata**: Define los metadatos de tu proyecto, como el grupo (ej. `com.miempresa`) y el artefacto (ej. `aplicacioninteligente`).
- **Dependencies**: Son las bibliotecas que nuestra aplicación necesita. Para este proyecto, agregaremos:
  - `Spring Web`: Nos permite crear la API RESTful.
  - `Spring Data JPA`: Simplifica la interacción con la base de datos usando Java Persistence API.
  - `PostgreSQL Driver`: El conector que permite a Spring Boot comunicarse con la base de datos PostgreSQL.

## 2. Arquitectura Empresarial

Para construir aplicaciones robustas, es fundamental seguir un modelo arquitectónico que separe las responsabilidades. El patrón más común es la arquitectura de **capas**. Esta separación de responsabilidades, también conocida como "separación de preocupaciones", hace que el código sea más organizado, fácil de mantener y probar.

### Las 3 Capas Principales

Una aplicación empresarial suele dividirse en tres capas lógicas.

1. **Capa de Presentación (Controller)**:
    - Es el punto de entrada de la aplicación. Su única responsabilidad es recibir las peticiones HTTP (GET, POST, etc.) del cliente y delegarlas a la capa de servicio. También se encarga de devolver una respuesta HTTP al cliente.
    - **No contiene lógica de negocio**. Piensa en el controlador como un simple "portero" que dirige el tráfico. Un ejemplo de su función es mapear una URL como `/api/productos` a un método específico, como `getAllProductos()`.

2. **Capa de Servicio (Service)**:
    - Contiene la **lógica de negocio** de la aplicación. Aquí se encuentra el "cerebro" de la aplicación.
    - Coordina las operaciones y puede interactuar con múltiples repositorios para realizar una tarea compleja. Por ejemplo, un servicio podría tomar los datos del controlador, validar que el precio del producto sea mayor a cero, y luego interactuar con el repositorio para guardar el producto.
3. **Capa de Datos (Repository)**:
    - Se encarga de la persistencia y la recuperación de datos. Su responsabilidad es puramente la comunicación con la base de datos.
    - La interfaz `JpaRepository` de Spring es una herramienta poderosa que nos proporciona métodos para realizar operaciones CRUD (Crear, Leer, Actualizar, Eliminar) de forma automática. Simplemente creamos una interfaz y Spring se encarga de la implementación por nosotros, eliminando la necesidad de escribir sentencias SQL manuales para operaciones básicas.

## 3. Creación de una API RESTful con PostgreSQL usando IA

Ahora, pasaremos a la práctica. Crearemos una API REST para gestionar una entidad, por ejemplo, `Producto`, que nos permitirá realizar operaciones básicas de CRUD.

### Paso 1: Configurar la Conexión a la Base de Datos

En el archivo `src/main/resources/application.properties`, agregamos las propiedades de configuración para nuestra base de datos.

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/mi_basedatos
spring.datasource.username=mi_usuario
spring.datasource.password=mi_contraseña
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

- `spring.datasource.url`: Indica la ubicación de la base de datos (protocolo, host, puerto, nombre de la base de datos).
- `spring.datasource.username` y `spring.datasource.password`: Son las credenciales de acceso.
- `spring.jpa.hibernate.ddl-auto=update`: Le dice a Hibernate (la implementación de JPA) que cree o actualice el esquema de la base de datos automáticamente basándose en las entidades Java que definamos. Advertencia: Esta configuración es excelente para el desarrollo, pero no se recomienda para entornos de producción.
- `spring.jpa.show-sql=true`: Imprime las sentencias SQL generadas por JPA en la consola, lo que es muy útil para depurar.

**Prompt para IA**:

> Crea la configuración para la conexión a la base de datos postgresql de nombre 'mi_basededatos' con el usuario 'mi_usuario' y contraseña 'mi_contraseña'. Habilita la actualización del esquema automático en hibernate e imprime las sentencias sql de JPA en consola.

### Paso 2: Crear el Modelo (`Entity`)

Esta clase representa una tabla en nuestra base de datos. Le indicaremos a Spring que la trate como una entidad con la anotación `@Entity`. Podemos usar la **IA para generar el esqueleto** de esta clase, dándole los campos que necesitamos.

```java
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Producto {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String nombre;
    private Double precio;

    // Getters y Setters
    // (Puedes usar la IA para generarlos o la función de tu IDE)
}
```

- `@Entity`: Le indica a Spring que esta clase es un modelo de datos y debe mapearse a una tabla en la base de datos.
- `@Id`: Marca la clave primaria de la tabla.
- `@GeneratedValue`: Le indica a la base de datos que genere automáticamente un valor para la clave primaria. `GenerationType.IDENTITY` usa la columna de auto-incremento de la base de datos.

**Prompt para IA**:

> Genera la clase Java `Producto` como una entidad de JPA en Spring Boot. La clase debe tener un `id` de tipo Long con autogeneración, un campo `nombre` de tipo String y un campo `precio` de tipo Double. Incluye los getters y setters.

### Paso 3: Crear el Repositorio (`Repository`)

Esta interfaz nos dará acceso directo a las operaciones de la base de datos. No necesitamos escribir ninguna implementación, ya que Spring Data JPA lo hace por nosotros.

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface ProductoRepository extends JpaRepository<Producto, Long> {
    // Spring Data JPA proporciona métodos como save(), findById(), findAll(), deleteById(), etc.
    // Simplemente al heredar de JpaRepository, ya tenemos acceso a todas estas funcionalidades.
}
```

**Prompt para IA**:

> Genera una interfaz de Spring Data JPA llamada `ProductoRepository` que se conecte a la entidad `Producto` y use un tipo de ID de `Long`.

### Paso 4: Crear la Capa de Servicio (`Service`)

La clase de servicio contendrá la lógica de negocio. Separar el servicio del controlador es una buena práctica porque nos permite reutilizar la lógica de negocio en diferentes partes de la aplicación.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;
import java.util.Optional;

@Service
public class ProductoService {

    @Autowired
    private ProductoRepository productoRepository;

    public List<Producto> findAll() {
        return productoRepository.findAll();
    }

    public Optional<Producto> findById(Long id) {
        return productoRepository.findById(id);
    }

    public Producto save(Producto producto) {
        // Aquí podríamos añadir lógica de negocio, como validaciones
        // if (producto.getPrecio() <= 0) { throw new IllegalArgumentException("El precio debe ser positivo"); }
        return productoRepository.save(producto);
    }

    public void deleteById(Long id) {
        productoRepository.deleteById(id);
    }
}
```

- `@Service`: Anotación que marca esta clase como un componente de servicio de Spring.
- `@Autowired`: Permite a Spring inyectar automáticamente una instancia de `ProductoRepository`.

**Prompt para IA**:

> Escribe la clase de servicio `ProductoService` para Spring Boot. Debe usar el repositorio `ProductoRepository` y tener métodos para encontrar todos los productos, encontrar un producto por ID, guardar un producto y eliminar un producto por ID.

### Paso 5: Crear el Controlador (`Controller`)

El controlador expone nuestros endpoints RESTful para que el frontend pueda interactuar con la API.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;
import java.util.Optional;

@RestController
@RequestMapping("/api/productos")
public class ProductoController {

    @Autowired
    private ProductoService productoService;

    // GET /api/productos -> Obtiene todos los productos
    @GetMapping
    public List<Producto> getAllProductos() {
        return productoService.findAll();
    }

    // GET /api/productos/{id} -> Obtiene un producto por su ID
    @GetMapping("/{id}")
    public ResponseEntity<Producto> getProductoById(@PathVariable Long id) {
        Optional<Producto> producto = productoService.findById(id);
        return producto.map(ResponseEntity::ok)
                       .orElseGet(() -> ResponseEntity.notFound().build());
    }

    // POST /api/productos -> Crea un nuevo producto
    // El cuerpo de la petición (JSON) se mapea automáticamente a la clase Producto
    @PostMapping
    public ResponseEntity<Producto> createProducto(@RequestBody Producto producto) {
        Producto newProducto = productoService.save(producto);
        return new ResponseEntity<>(newProducto, HttpStatus.CREATED);
    }

    // PUT /api/productos/{id} -> Actualiza un producto existente
    @PutMapping("/{id}")
    public ResponseEntity<Producto> updateProducto(@PathVariable Long id, @RequestBody Producto productoDetails) {
        Optional<Producto> optionalProducto = productoService.findById(id);
        if (optionalProducto.isPresent()) {
            Producto producto = optionalProducto.get();
            producto.setNombre(productoDetails.getNombre());
            producto.setPrecio(productoDetails.getPrecio());
            Producto updatedProducto = productoService.save(producto);
            return ResponseEntity.ok(updatedProducto);
        } else {
            return ResponseEntity.notFound().build();
        }
    }

    // DELETE /api/productos/{id} -> Elimina un producto por su ID
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteProducto(@PathVariable Long id) {
        productoService.deleteById(id);
        return ResponseEntity.noContent().build();
    }
}
```

**Prompt para IA**:

> Genera una clase de controlador REST de Spring Boot para la entidad `Producto`. La clase debe tener los endpoints para realizar las operaciones CRUD (GET, POST, PUT, DELETE) y mapearlos a los métodos de la clase `ProductoService`.
