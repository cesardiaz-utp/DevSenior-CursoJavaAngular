# Clase 2: Desarrollo Frontend con Angular 20

En esta clase, nos sumergiremos en el mundo del **desarrollo frontend** y exploraremos cómo Angular nos permite crear interfaces de usuario modernas y dinámicas. Nos enfocaremos en su arquitectura más reciente, utilizando componentes standalone para simplificar la estructura del proyecto, y veremos cómo la Inteligencia Artificial puede acelerar la creación de nuestra interfaz de usuario.

## 1. Introducción al Desarrollo Web

### a. Fundamentos (HTML, CSS, JavaScript)

Antes de Angular, es crucial entender los pilares de la web. Estos tres lenguajes forman la base sobre la cual se construye cualquier sitio o aplicación web, desde el más simple blog hasta el servicio de streaming más complejo.

- **HTML (HyperText Markup Language)**: Es el lenguaje de marcado que define la **estructura** y el contenido de una página web. Piensa en HTML como el esqueleto de tu aplicación. Define las partes lógicas del contenido, como títulos, párrafos, tablas, listas y enlaces. Sin HTML, una página web sería solo texto sin formato.
- **CSS (Cascading Style Sheets)**: Es el lenguaje de estilos que define **la apariencia y el diseño** de los elementos HTML. Con CSS, puedes dar color, formato y organizar el diseño de tu sitio. Controla aspectos como colores, tipografía, espaciado, y la posición de los elementos. Es lo que transforma un esqueleto básico en una interfaz visualmente atractiva.
- **JavaScript**: Es el lenguaje de programación que añade **interactividad y lógica** a una página web. Permite que la página reaccione a las acciones del usuario (como clics o movimientos del mouse), valide formularios, o incluso cargue nuevos datos sin recargar la página completa. JavaScript es el "músculo" que hace que la experiencia de usuario sea dinámica y receptiva.

### b. ¿Por qué Angular?

En las aplicaciones web modernas, una página simple ya no es suficiente. Los usuarios esperan experiencias dinámicas y rápidas, similares a las de las aplicaciones de escritorio. Aquí es donde entran los **Single Page Applications (SPA)** y frameworks como Angular.

Un **SPA** es una aplicación web que se carga en una sola página HTML, y el contenido se reescribe dinámicamente a medida que el usuario interactúa. Esto hace que la navegación sea mucho más fluida y rápida, ya que el navegador no tiene que recargar toda la página. Angular es un framework de Google que nos ayuda a construir SPAs a gran escala. Proporciona una estructura clara, herramientas robustas y un conjunto de principios de diseño que permiten a los equipos de desarrollo colaborar de manera eficiente y mantener el código base a lo largo del tiempo.

## 2. Fundamentos de Angular

### a. TypeScript

Angular está construido sobre **TypeScript**, un superconjunto de JavaScript que añade tipado estático. Esto significa que podemos definir tipos de datos para nuestras variables, lo que ayuda a prevenir errores comunes y hace que el código sea más predecible y fácil de mantener. A diferencia de JavaScript, donde una variable puede cambiar de tipo (`let x = 10; x = "hola";`), TypeScript lo impide, obligando a una mayor consistencia.

- **Ventaja**: Mayor legibilidad y un sistema de validación que te avisa de los errores antes de ejecutar el código. Por ejemplo, si intentas asignar una cadena a una variable que esperas que sea un número, TypeScript te lo señalará de inmediato en tu editor.

### b. Estructura de la Aplicación y Componentes

En las versiones más recientes de Angular, el enfoque principal son los componentes **standalone** (autónomos). Esto simplifica la estructura, eliminando la necesidad de los módulos tradicionales. Ahora, cada componente es independiente y gestiona sus propias dependencias, lo que hace que el código sea más legible y más fácil de organizar.

- **Componentes (`Components`)**: Son el bloque de construcción fundamental de una aplicación Angular. Cada componente controla una parte de la pantalla (ej. un encabezado, una tabla de productos o un formulario). Un componente está compuesto por:
  - **Un archivo de TypeScript (lógica)**: Contiene la clase del componente y define las propiedades y métodos.
  - **Un archivo de HTML (plantilla)**: Contiene la estructura de la interfaz de usuario.
  - **Un archivo de CSS (estilos)**: Contiene las reglas de estilo para ese componente en particular.
- **Modelos (`Models`)**: Son clases o interfaces que definen la estructura de los datos que vamos a usar en la aplicación. Por ejemplo, la clase Producto que definimos en el backend, que nos asegura que los datos que recibimos y enviamos tienen el formato correcto.
- **Servicios (`Services`)**: Contienen lógica que se puede compartir entre diferentes componentes, como la lógica para comunicarse con el backend a través de APIs, realizar cálculos complejos o gestionar el estado de la aplicación. Son una excelente forma de mantener la lógica de negocio separada de la lógica de la interfaz de usuario.

## 3. Creación de una Aplicación para la Gestión de Productos

Vamos a crear una interfaz de usuario básica para gestionar los productos de nuestra API.

### Paso 1: Configuración Inicial del Proyecto

- Abre tu terminal y crea un nuevo proyecto Angular: `ng new mi-app-productos`
- Navega al directorio del proyecto: `cd mi-app-productos`
- Inicia el servidor de desarrollo: `ng serve --open`

### Paso 2: Crear el Modelo `Producto`

Este modelo nos ayudará a tipar los datos que recibiremos de la API. Hemos añadido el atributo `imagenUrl` para poder mostrar imágenes de cada producto.

```typescript
// src/app/producto.ts
export interface Producto {
  id: number;
  nombre: string;
  precio: number;
  imagenUrl: string;
}
```

**Prompt para IA**:

> Crea una interfaz de TypeScript llamada `Producto` con los siguientes campos: `id` (número), `nombre` (string), `precio` (número) y `imagenUrl` (string).

### Paso 3: Crear el Servicio de Productos

Vamos a crear un servicio que se encargará de gestionar los datos de los productos. Esto es una buena práctica porque nos permite desacoplar los datos del componente, haciendo que el código sea más reutilizable y fácil de probar.

- **Comando**: `ng generate service producto`
- **Lógica del servicio (`producto.service.ts`)**:

  ```typescript
  import { Injectable } from '@angular/core';
  import { Producto } from './producto';

  @Injectable({
    providedIn: 'root'
  })
  export class ProductoService {
    constructor() { }

    // Datos ficticios. En la siguiente clase, esta lógica se reemplazará por una llamada HTTP.
    getProductos(): Producto[] {
      return [
        { id: 1, nombre: 'Laptop', precio: 1200, imagenUrl: 'https://placehold.co/400x200/cccccc/000000?text=Laptop' },
        { id: 2, nombre: 'Mouse', precio: 25, imagenUrl: 'https://placehold.co/400x200/cccccc/000000?text=Mouse' },
        { id: 3, nombre: 'Teclado Mecánico', precio: 75, imagenUrl: 'https://placehold.co/400x200/cccccc/000000?text=Teclado' },
        { id: 4, nombre: 'Monitor', precio: 300, imagenUrl: 'https://placehold.co/400x200/cccccc/000000?text=Monitor' },
        { id: 5, nombre: 'Cámara Web', precio: 50, imagenUrl: 'https://placehold.co/400x200/cccccc/000000?text=Camara' }
      ];
    }
  }
  ```

**Prompt para IA**:

> Genera un servicio de Angular llamado `ProductoService` con una función llamada `getProductos`. La función debe devolver un array de objetos `Producto` con datos ficticios. Asegúrate de que los objetos incluyan los campos `id`, `nombre`, `precio` y `imagenUrl`.

### Paso 4: Crear una Pantalla de Listado de Productos y un Subcomponente de Tarjeta

En este paso, crearemos un componente principal `(producto-list`) que será el encargado de manejar la lista de productos y un subcomponente (`producto-card`) que mostrará cada producto individualmente como una tarjeta.

- Comando para el componente de listado: `ng generate component producto-list --skip-tests`
- Comando para el componente de tarjeta: `ng generate component producto-card --skip-tests`

#### Componente de Tarjeta (`producto-card`)

Este componente se encargará de la presentación de un solo producto. Recibirá un objeto `Producto` como entrada a través de `@Input`.

- **Lógica del componente (`producto-card.component.ts`)**:

  ```typescript
  import { Component, Input } from '@angular/core';
  import { CommonModule } from '@angular/common';
  import { Producto } from '../producto'; // Importamos la interfaz Producto

  @Component({
    selector: 'app-producto-card',
    standalone: true,
    imports: [CommonModule],
    templateUrl: './producto-card.component.html',
    styleUrl: './producto-card.component.css'
  })
  export class ProductoCardComponent {
    // Con @Input, este componente recibe un objeto 'producto' del componente padre
    @Input() producto!: Producto;
  }
  ```

- **Plantilla (`producto-card.component.html`)**:

  ```html
  <div class="product-card">
    <img [src]="producto.imagenUrl" alt="Imagen de {{ producto.nombre }}" class="product-image">
    <div class="product-info">
      <h3 class="product-name">{{ producto.nombre }}</h3>
      <p class="product-price">{{ producto.precio | currency:'USD':'symbol':'1.2-2' }}</p>
    </div>
  </div>
  ```

- **Estilos (`producto-card.component.css`)**:

  ```css
  .product-card {
    border: 1px solid #e0e0e0;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    overflow: hidden;
    transition: transform 0.3s ease-in-out;
    background-color: #fff;
  }

  .product-card:hover {
    transform: translateY(-5px);
    box-shadow: 0 6px 12px rgba(0, 0, 0, 0.15);
  }

  .product-image {
    width: 100%;
    height: 200px;
    object-fit: cover;
  }

  .product-info {
    padding: 15px;
  }

  .product-name {
    font-size: 1.2em;
    font-weight: bold;
    margin: 0 0 10px;
    color: #333;
  }

  .product-price {
    font-size: 1em;
    color: #007bff;
    font-weight: 600;
  }
  ```

#### `Componente de Listado (`producto-list`)`

Este componente principal ahora utilizará el nuevo servicio de productos.

- **Lógica del componente (`producto-list.component.ts`)**:

  ```typescript
  import { Component, OnInit } from '@angular/core';
  import { CommonModule } from '@angular/common';
  import { Producto } from '../producto'; // Importamos la interfaz Producto
  import { ProductoCardComponent } from '../producto-card/producto-card.component'; // Importamos el subcomponente
  import { ProductoService } from '../producto.service'; // Importamos el servicio

  @Component({
    selector: 'app-producto-list',
    standalone: true,
    imports: [CommonModule, ProductoCardComponent],
    templateUrl: './producto-list.component.html',
    styleUrl: './producto-list.component.css'
  })
  export class ProductoListComponent implements OnInit {
    // Inicializamos la propiedad como un array vacío
    productos: Producto[] = [];

    // Inyectamos el servicio en el constructor
    constructor(private productoService: ProductoService) {}

    // Usamos ngOnInit para obtener los datos del servicio
    ngOnInit(): void {
      this.productos = this.productoService.getProductos();
    }
  }
  ```

- **Plantilla (`producto-list.component.html`)**:

  ```html
  <div class="list-container">
    <h2>Listado de Productos</h2>

    <div class="product-grid">
      <!-- Usamos el nuevo componente de tarjeta dentro del bucle `@for` -->
      @for (producto of productos; track producto.id) {
        <app-producto-card [producto]="producto" />
      }
      @empty {
        <p class="no-products">No se encontraron productos.</p>
      }
    </div>
  </div>
  ```

- **Estilos (`producto-list.component.css`)**:

  ```css
  .list-container {
    max-width: 1200px;
    margin: 40px auto;
    padding: 20px;
    background-color: #f0f2f5;
    border-radius: 10px;
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.1);
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }

  h2 {
    color: #2c3e50;
    text-align: center;
    margin-bottom: 25px;
    font-size: 2em;
  }

  /* Usamos CSS Grid para un diseño de tarjetas responsivo */
  .product-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
    gap: 20px;
  }

  .no-products {
    text-align: center;
    font-style: italic;
    color: #888;
    grid-column: 1 / -1; /* Ocupa todo el ancho del grid */
  }
  ```

**Prompt para IA**:

> Crea un componente de Angular llamado `producto-list` que se encargue de mostrar una lista de productos. Este componente debe inyectar el `ProductoService` para obtener los datos. También, crea un subcomponente llamado `producto-card` que reciba un objeto `Producto` como `@Input()` para renderizar los detalles del producto de forma individual. La plantilla de `producto-list` debe usar el nuevo flujo de control `@for` para iterar sobre la lista y mostrar una `<app-producto-card>` por cada producto.

### Paso 5: Crear un Formulario para Capturar Información

Necesitaremos un formulario para agregar nuevos productos o editar los existentes. Aquí podemos usar la nueva sintaxis `@if` para mostrar u ocultar elementos de manera condicional.

- **Comando**: `ng generate component producto-form --skip-tests`
- **Lógica del componente (`producto-form.component.ts`)**:

  ```typescript
  import { Component, OnInit } from '@angular/core';
  import { CommonModule } from '@angular/common';
  import { FormBuilder, FormGroup, ReactiveFormsModule, Validators } from '@angular/forms';

  @Component({
    selector: 'app-producto-form',
    standalone: true,
    imports: [CommonModule, ReactiveFormsModule],
    templateUrl: './producto-form.component.html',
    styleUrl: './producto-form.component.css'
  })
  export class ProductoFormComponent implements OnInit {
    productoForm: FormGroup;

    // El FormBuilder simplifica la creación de formularios reactivos
    constructor(private fb: FormBuilder) {
      this.productoForm = this.fb.group({
        nombre: ['', Validators.required],
        precio: [null, [Validators.required, Validators.min(0)]],
        imagenUrl: ['', Validators.required]
      });
    }

    // ngOnInit se ejecuta una vez cuando el componente se inicializa
    ngOnInit(): void {
      // Aquí podríamos precargar datos de un producto si estuviéramos editando
    }

    onSubmit() {
      // Verificamos si el formulario es válido antes de enviar
      if (this.productoForm.valid) {
        console.log('Formulario enviado:', this.productoForm.value);
        // Aquí se enviaría el producto a la API del backend
      }
    }
  }
  ```

- **Plantilla (`producto-form.component.html`)**:

  ```html
  <div class="form-container">
    <h2>Formulario de Producto</h2>

    <form [formGroup]="productoForm" (ngSubmit)="onSubmit()">
      <div class="form-group">
        <label for="nombre">Nombre:</label>
        <input id="nombre" type="text" formControlName="nombre">
        <!-- El `@if` es la nueva sintaxis para mostrar u ocultar elementos -->
        @if (productoForm.get('nombre')?.invalid && productoForm.get('nombre')?.touched) {
          <p class="error-message">El nombre es obligatorio.</p>
        }
      </div>

      <div class="form-group">
        <label for="precio">Precio:</label>
        <input id="precio" type="number" formControlName="precio">
        @if (productoForm.get('precio')?.invalid && productoForm.get('precio')?.touched) {
          <p class="error-message">El precio es obligatorio y debe ser un número positivo.</p>
        }
      </div>

      <div class="form-group">
        <label for="imagenUrl">URL de la Imagen:</label>
        <input id="imagenUrl" type="text" formControlName="imagenUrl">
        @if (productoForm.get('imagenUrl')?.invalid && productoForm.get('imagenUrl')?.touched) {
          <p class="error-message">La URL de la imagen es obligatoria.</p>
        }
      </div>

      <button type="submit" [disabled]="!productoForm.valid">Guardar Producto</button>
    </form>
  </div>
  ```

- **Estilos (`producto-form.component.css`)**:

  ```css
  .form-container {
    max-width: 600px;
    margin: 40px auto;
    padding: 30px;
    background-color: #f9f9f9;
    border-radius: 10px;
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  }

  h2 {
    text-align: center;
    color: #34495e;
    margin-bottom: 25px;
  }

  .form-group {
    margin-bottom: 20px;
  }

  label {
    display: block;
    margin-bottom: 8px;
    font-weight: 600;
    color: #333;
  }

  input[type="text"], input[type="number"] {
    width: 100%;
    padding: 12px;
    border: 1px solid #ccc;
    border-radius: 6px;
    box-sizing: border-box;
    font-size: 16px;
    transition: border-color 0.3s ease, box-shadow 0.3s ease;
  }

  input[type="text"]:focus, input[type="number"]:focus {
    border-color: #007bff;
    box-shadow: 0 0 5px rgba(0, 123, 255, 0.5);
    outline: none;
  }

  .error-message {
    color: #e74c3c;
    font-size: 0.9em;
    margin-top: 5px;
  }

  button[type="submit"] {
    width: 100%;
    background-color: #007bff;
    color: white;
    padding: 15px;
    border: none;
    border-radius: 6px;
    cursor: pointer;
    font-size: 18px;
    font-weight: bold;
    transition: background-color 0.3s ease;
  }

  button[type="submit"]:hover:not([disabled]) {
    background-color: #0056b3;
  }

  button[type="submit"]:disabled {
    background-color: #bdc3c7;
    cursor: not-allowed;
  }
  ```

**Prompt para IA**:

> Genera un formulario HTML para un componente de Angular llamado `producto-form` usando formularios reactivos. Debe tener campos para el nombre (`nombre`), el precio (`precio`) y la URL de la imagen (`imagenUrl`) del producto. Incluye un botón para enviar el formulario. Asegúrate de incluir validaciones básicas para que todos los campos sean obligatorios y usa `@if` para mostrar mensajes de error. Incluye estilos CSS básicos para el formulario.
