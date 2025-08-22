# Clase 3: Conexión Frontend y Backend

En esta clase, pondremos en práctica los conocimientos de las clases anteriores para establecer una conexión real entre nuestra aplicación frontend, desarrollada con Angular, y el backend que construimos con **Spring Boot**. El objetivo es que nuestra interfaz de usuario sea dinámica, segura y escalable.

## 1. Integración entre Backend y Frontend

### a. CORS (Cross-Origin Resource Sharing)

Para que nuestra aplicación de Angular, que se ejecuta en un dominio (`localhost:4200`), pueda comunicarse con nuestra API de backend, que se ejecuta en otro dominio (`localhost:8080`), necesitamos habilitar CORS. Sin esta configuración, el navegador bloqueará las peticiones por seguridad.

El backend debe configurar las cabeceras `Access-Control-Allow-Origin` para permitir las peticiones del frontend.

### b. Configuración de CORS en Spring Boot

Para habilitar CORS en nuestro backend de Spring Boot, la práctica recomendada es definir un `CorsFilter` como un bean en nuestra configuración. Esto asegura que la configuración de CORS se aplique a todas las peticiones de manera uniforme.

- **Archivo**: `src/main/java/.../config/WebConfig.java`

  ```java
  package com.miaplicacion.config;

  import org.springframework.context.annotation.Bean;
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.cors.CorsConfiguration;
  import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
  import org.springframework.web.filter.CorsFilter;

  @Configuration
  public class WebConfig {

      @Bean
      public CorsFilter corsFilter() {
          UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
          CorsConfiguration config = new CorsConfiguration();
          config.setAllowCredentials(true);
          config.addAllowedOrigin("http://localhost:4200"); // Permite peticiones desde el origen de Angular
          config.addAllowedHeader("*");
          config.addAllowedMethod("*"); // Permite todos los métodos HTTP (GET, POST, PUT, DELETE, etc.)
          source.registerCorsConfiguration("/**", config);
          return new CorsFilter(source);
      }
  }
  ```

Este código crea un filtro de CORS que se aplicará a todas las rutas (`/**`). Le indica a Spring Boot que debe aceptar peticiones del origen `http://localhost:4200` y permite todos los métodos y cabeceras necesarios para la comunicación.

**Prompt para IA**:

> Crea una clase de configuración llamada `WebConfig` en el paquete `config`. Dentro de esta clase, genera un bean llamado `corsFilter` que devuelva una nueva instancia de `CorsFilter`. Configura este filtro para permitir peticiones del origen `http://localhost:4200` y para que acepte todos los métodos y cabeceras.

### c. Consumo de APIs HTTP en Angular

Utilizaremos el servicio `HttpClient` para manejar todas nuestras peticiones a la API. Este servicio es fundamental para la comunicación con el backend y maneja las peticiones de forma asíncrona usando Observables.

#### Paso 1: Configurar `HttpClientModule` y el Enrutamiento

Antes de poder usar el servicio `HttpClient`, debemos registrarlo como un proveedor en nuestra aplicación. Como estamos usando componentes standalone, el proveedor se agrega directamente en el archivo de configuración principal de la aplicación.

Además, con la incorporación de un componente de login, es esencial configurar el enrutamiento para gestionar la navegación entre las diferentes vistas de la aplicación.

- **Archivo: `src/app/app.config.ts`**

  ```typescript
  import { ApplicationConfig } from '@angular/core';
  import { provideRouter } from '@angular/router';
  import { provideHttpClient, withInterceptors } from '@angular/common/http';
  import { authInterceptor } from './auth.interceptor';
  import { routes } from './app.routes';

  import { routes } from './app.routes';

  export const appConfig: ApplicationConfig = {
    providers: [
      provideRouter(routes), // Registramos las rutas
      provideHttpClient(withInterceptors([authInterceptor])) // Lo agregamos como un proveedor en la configuración
    ]
  };
  ```

- **Archivo: `src/app/app.routes.ts`**

  ```typescript
  import { Routes } from '@angular/router';
  import { LoginComponent } from './login/login.component';
  import { ProductoComponent } from './producto/producto.component';
  import { authGuard } from './auth.guard'; // Importamos el guard

  export const routes: Routes = [
      { path: '', redirectTo: '/login', pathMatch: 'full' },
      { path: 'login', component: LoginComponent },
      { path: 'productos', component: ProductoComponent, canActivate: [authGuard] }
  ];
  ```

**Prompt para IA**:

> Actualiza el archivo `src/app/app.config.ts`. El código debe importar `ApplicationConfig` de `@angular/core`, `provideRouter` de `@angular/router`, y `provideHttpClient` junto con `withInterceptors` de `@angular/common/http`. También debe importar el interceptor funcional `authInterceptor` desde `./auth.interceptor` y el arreglo de rutas `routes` desde `./app.routes`.En la propiedad `providers` debe registrar `provideRouter(routes)` y `provideHttpClient(withInterceptors([authInterceptor]))`.

#### Paso 2: Actualizar el `ProductoService` con todos los métodos HTTP

Ahora, modificaremos nuestro servicio para incluir los cuatro tipos de peticiones HTTP principales: `GET` para obtener datos, `POST` para crear, `PUT` para actualizar y `DELETE` para eliminar.

```typescript
// src/app/producto.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';
import { Producto } from './producto';

@Injectable({
  providedIn: 'root'
})
export class ProductoService {
  private apiUrl = 'http://127.0.0.1:8080/api/productos';

  constructor(private http: HttpClient) { }

  // Obtener todos los productos
  getProductos(): Observable<Producto[]> {
    return this.http.get<Producto[]>(this.apiUrl);
  }

  // Crear un nuevo producto
  addProducto(producto: Producto): Observable<Producto> {
    return this.http.post<Producto>(this.apiUrl, producto);
  }

  // Actualizar un producto existente
  updateProducto(producto: Producto): Observable<any> {
    return this.http.put(`${this.apiUrl}/${producto.id}`, producto);
  }

  // Eliminar un producto por su ID
  deleteProducto(id: number): Observable<any> {
    return this.http.delete(`${this.apiUrl}/${producto.id}`);
  }
}
```

**Prompt para IA**:

> Actualiza un servicio de Angular llamado `ProductoService` para que utilice `HttpClient` e implemente los cuatro métodos de peticiones REST: `getProductos()` (GET), `addProducto()` (POST), `updateProducto()` (PUT) y `deleteProducto()` (DELETE). Asegúrate de que las peticiones se dirijan a la URL `http://127.0.0.1:8080/api/productos` y de que los métodos manejen el paso del ID del producto para las peticiones de actualización y eliminación.

## 2. Asegurando APIs

La seguridad es fundamental para cualquier aplicación web, y se basa en dos pilares principales: autenticación y autorización. La autenticación es el proceso de verificar la identidad de un usuario (¿quién eres?), mientras que la autorización determina a qué recursos puede acceder ese usuario (¿qué puedes hacer?). Esto se logra comúnmente con JSON Web Tokens (JWT).

### a. Backend (Spring Security)

En el backend, **Spring Security** es el framework de facto para manejar la seguridad de las aplicaciones. Nos permite proteger los endpoints de la API, asegurando que solo las peticiones de usuarios autenticados y autorizados puedan acceder a ellos. La lógica de Spring Security intercepta las peticiones entrantes, valida el JWT y, basándose en la información del token (por ejemplo, los roles del usuario), decide si la petición debe ser procesada o rechazada con un error de acceso denegado.

#### Implementación Completa de Seguridad con JWT en Spring Boot

Para que la seguridad funcione, necesitas varios componentes que interactúen entre sí. Aquí están los archivos y su implementación.

1. **Modelo de Usuario (`User.java`)**: Un modelo simple para representar a un usuario. En una aplicación real, este modelo se conectaría a una base de datos.

    ```java
    package com.miaplicacion.models;

    import java.util.Collections;
    import java.util.List;
    import org.springframework.security.core.GrantedAuthority;
    import org.springframework.security.core.authority.SimpleGrantedAuthority;
    import org.springframework.security.core.userdetails.UserDetails;
    import java.util.Collection;

    public class User implements UserDetails {

        private String username;
        private String password;

        public User(String username, String password) {
            this.username = username;
            this.password = password;
        }

        @Override
        public Collection<? extends GrantedAuthority> getAuthorities() {
            return List.of(new SimpleGrantedAuthority("ROLE_USER"));
        }

        @Override
        public String getPassword() {
            return password;
        }

        @Override
        public String getUsername() {
            return username;
        }

        @Override
        public boolean isAccountNonExpired() { return true; }

        @Override
        public boolean isAccountNonLocked() { return true; }

        @Override
        public boolean isCredentialsNonExpired() { return true; }

        @Override
        public boolean isEnabled() { return true; }
    }
    ```

2. **Servicio de Detalles de Usuario (`UserDetailsServiceImpl.java`)**: Este servicio carga los detalles del usuario durante la autenticación. En este ejemplo simple, simulamos un usuario en memoria.

    ```java
    package com.miaplicacion.services;

    import com.miaplicacion.models.User;
    import org.springframework.security.core.userdetails.UserDetails;
    import org.springframework.security.core.userdetails.UserDetailsService;
    import org.springframework.security.core.userdetails.UsernameNotFoundException;
    import org.springframework.stereotype.Service;

    @Service
    public class UserDetailsServiceImpl implements UserDetailsService {

        @Override
        public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
            // En un proyecto real, buscarías al usuario en una base de datos.
            // Aquí, simulamos un usuario en memoria para la demostración.
            if ("admin".equals(username)) {
                return new User("admin", "{noop}password"); // {noop} es un prefijo para que Spring sepa que no hay encriptación.
            }
            throw new UsernameNotFoundException("Usuario no encontrado con username: " + username);
        }
    }
    ```

3. **Utilidad para JWT (`JwtUtil.java`)**: Una clase para generar, validar y extraer información de los tokens JWT.

    ```java
    package com.miaplicacion.security;

    import io.jsonwebtoken.Claims;
    import io.jsonwebtoken.Jwts;
    import io.jsonwebtoken.SignatureAlgorithm;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.security.core.userdetails.UserDetails;
    import org.springframework.stereotype.Component;
    import java.util.Date;
    import java.util.HashMap;
    import java.util.Map;
    import java.util.function.Function;

    @Component
    public class JwtUtil {

        @Value("${jwt.secret}")
        private String secret;

        @Value("${jwt.expiration}")
        private long expiration;

        public String generateToken(UserDetails userDetails) {
            Map<String, Object> claims = new HashMap<>();
            return createToken(claims, userDetails.getUsername());
        }

        private String createToken(Map<String, Object> claims, String subject) {
            return Jwts.builder()
                    .setClaims(claims)
                    .setSubject(subject)
                    .setIssuedAt(new Date(System.currentTimeMillis()))
                    .setExpiration(new Date(System.currentTimeMillis() + expiration * 1000))
                    .signWith(SignatureAlgorithm.HS256, secret)
                    .compact();
        }

        public boolean validateToken(String token, UserDetails userDetails) {
            final String username = extractUsername(token);
            return (username.equals(userDetails.getUsername()) && !isTokenExpired(token));
        }

        public String extractUsername(String token) {
            return extractClaim(token, Claims::getSubject);
        }

        public Date extractExpiration(String token) {
            return extractClaim(token, Claims::getExpiration);
        }

        public <T> T extractClaim(String token, Function<Claims, T> claimsResolver) {
            final Claims claims = extractAllClaims(token);
            return claimsResolver.apply(claims);
        }

        private Claims extractAllClaims(String token) {
            return Jwts.parser().setSigningKey(secret).parseClaimsJws(token).getBody();
        }

        private boolean isTokenExpired(String token) {
            return extractExpiration(token).before(new Date());
        }
    }
    ```

4. **Filtro para Peticiones JWT (`JwtRequestFilter.java`)**: Este filtro se ejecuta en cada petición HTTP para validar el token y autenticar al usuario si el token es válido.

    ```java
    package com.miaplicacion.security;

    import com.miaplicacion.services.UserDetailsServiceImpl;
    import jakarta.servlet.FilterChain;
    import jakarta.servlet.ServletException;
    import jakarta.servlet.http.HttpServletRequest;
    import jakarta.servlet.http.HttpServletResponse;
    import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
    import org.springframework.security.core.context.SecurityContextHolder;
    import org.springframework.security.core.userdetails.UserDetails;
    import org.springframework.security.web.authentication.WebAuthenticationDetailsSource;
    import org.springframework.stereotype.Component;
    import org.springframework.web.filter.OncePerRequestFilter;
    import java.io.IOException;

    @Component
    public class JwtRequestFilter extends OncePerRequestFilter {

        private final UserDetailsServiceImpl userDetailsService;
        private final JwtUtil jwtUtil;

        public JwtRequestFilter(UserDetailsServiceImpl userDetailsService, JwtUtil jwtUtil) {
            this.userDetailsService = userDetailsService;
            this.jwtUtil = jwtUtil;
        }

        @Override
        protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
                throws ServletException, IOException {

            final String authorizationHeader = request.getHeader("Authorization");

            String username = null;
            String jwt = null;

            if (authorizationHeader != null && authorizationHeader.startsWith("Bearer ")) {
                jwt = authorizationHeader.substring(7);
                username = jwtUtil.extractUsername(jwt);
            }

            if (username != null && SecurityContextHolder.getContext().getAuthentication() == null) {
                UserDetails userDetails = this.userDetailsService.loadUserByUsername(username);

                if (jwtUtil.validateToken(jwt, userDetails)) {
                    UsernamePasswordAuthenticationToken authToken = new UsernamePasswordAuthenticationToken(
                            userDetails, null, userDetails.getAuthorities());
                    authToken.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
                    SecurityContextHolder.getContext().setAuthentication(authToken);
                }
            }
            filterChain.doFilter(request, response);
        }
    }
    ```

5. **Controlador de Autenticación (`AuthController.java`)**: Este es el endpoint que el frontend llamará para iniciar sesión y obtener un token JWT.

    ```java
    package com.miaplicacion.controllers;

    import com.miaplicacion.models.AuthenticationRequestDto;
    import com.miaplicacion.models.AuthenticationResponseDto;
    import com.miaplicacion.security.JwtUtil;
    import com.miaplicacion.services.UserDetailsServiceImpl;
    import org.springframework.http.ResponseEntity;
    import org.springframework.security.authentication.AuthenticationManager;
    import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
    import org.springframework.security.core.userdetails.UserDetails;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestBody;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;

    @RestController
    @RequestMapping("/api/auth")
    public class AuthController {

        private final AuthenticationManager authenticationManager;
        private final UserDetailsServiceImpl userDetailsService;
        private final JwtUtil jwtUtil;

        public AuthController(AuthenticationManager authenticationManager, UserDetailsServiceImpl userDetailsService, JwtUtil jwtUtil) {
            this.authenticationManager = authenticationManager;
            this.userDetailsService = userDetailsService;
            this.jwtUtil = jwtUtil;
        }

        @PostMapping("/login")
        public ResponseEntity<?> createAuthenticationToken(@RequestBody AuthenticationRequestDto authenticationRequestDto) throws Exception {
            authenticationManager.authenticate(new UsernamePasswordAuthenticationToken(
                    authenticationRequestDto.getUsername(), authenticationRequestDto.getPassword()));

            final UserDetails userDetails = userDetailsService.loadUserByUsername(authenticationRequestDto.getUsername());
            final String jwt = jwtUtil.generateToken(userDetails);

            return ResponseEntity.ok(new AuthenticationResponseDto(jwt));
        }
    }
    ```

6. **Clases Auxiliares para Autenticación**: Modelos para las peticiones y respuestas de autenticación.

    ```java
    // src/main/java/.../models/AuthenticationRequestDto.java
    package com.miaplicacion.models;

    import java.io.Serializable;

    public record AuthenticationRequestDto(String username, String password) implements Serializable {}
    ```

    ```java
    // src/main/java/.../models/AuthenticationResponseDto.java
    package com.miaplicacion.models;

    import java.io.Serializable;

    public record AuthenticationResponseDto(String jwt) implements Serializable {}
    ```

7. **Configuración de Seguridad (`SecurityConfig.java`)**: El archivo de configuración principal de Spring Security, actualizado para usar los componentes anteriores.

    ```java
    package com.miaplicacion.config;

    import com.miaplicacion.security.JwtRequestFilter;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.security.authentication.AuthenticationManager;
    import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.http.SessionCreationPolicy;
    import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
    import org.springframework.security.crypto.password.PasswordEncoder;
    import org.springframework.security.web.SecurityFilterChain;
    import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;

    @Configuration
    @EnableWebSecurity
    public class SecurityConfig {

        private final JwtRequestFilter jwtRequestFilter;

        public SecurityConfig(JwtRequestFilter jwtRequestFilter) {
            this.jwtRequestFilter = jwtRequestFilter;
        }

        @Bean
        public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
            http
                .csrf(csrf -> csrf.disable())
                .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
                .authorizeHttpRequests(authz -> authz
                    .requestMatchers("/api/auth/**").permitAll()
                    .anyRequest().authenticated()
                )
                .addFilterBefore(jwtRequestFilter, UsernamePasswordAuthenticationFilter.class);

            return http.build();
        }

        @Bean
        public PasswordEncoder passwordEncoder() {
            return new BCryptPasswordEncoder();
        }

        @Bean
        public AuthenticationManager authenticationManager(AuthenticationConfiguration authenticationConfiguration) throws Exception {
            return authenticationConfiguration.getAuthenticationManager();
        }
    }
    ```

8. **Configuracion de parámetros de JWT (`src/main/resources/application.properties`)**

    ```properties
    # --- Configuración de seguridad JWT ---
    # Clave secreta para firmar los tokens JWT. Es crucial mantener esta clave segura y en un entorno de producción debe ser una variable de entorno.
    # Genera una clave segura de al menos 256 bits (por ejemplo, usando https://www.allkeysgenerator.com/)
    jwt.secret=tu_clave_secreta_aqui_para_firmar_los_tokens_seguramente

    # Tiempo de expiración del token JWT en segundos.
    # En este ejemplo, el token expira en 3600 segundos (1 hora).
    jwt.expiration=3600
    ```

**Prompt para IA**:

> Implementa la seguridad completa del backend usando **Spring Security** y **JWT**. La implementación debe ser modular y estar dividida en los siguientes archivos y paquetes:
>
> 1. `SecurityConfig.java`: La clase de configuración principal de seguridad. Debe deshabilitar CSRF y configurar la gestión de sesiones como `STATELESS`. Permite el acceso público al endpoint `/api/auth/**` y requiere autenticación para todas las demás peticiones. Inyecta y agrega un filtro JWT (`JwtRequestFilter`) antes del filtro de autenticación de usuario y contraseña.
> 2. `JwtRequestFilter.java`: Un filtro de peticiones que se ejecuta una vez por cada solicitud. Debe extraer el token JWT de la cabecera `Authorization`, validar el token usando `JwtUtil`, y autenticar al usuario en el contexto de seguridad de Spring si el token es válido.
> 3. `JwtUtil.java`: Una clase de utilidad para generar, validar y extraer el nombre de usuario y la fecha de expiración de los tokens JWT. La clave secreta y el tiempo de expiración deben cargarse desde las propiedades de la aplicación (`application.properties`).
> 4. `UserDetailsServiceImpl.java`: Un servicio que implementa `UserDetailsService`. En un método llamado `loadUserByUsername`, simula la carga de un usuario en memoria (`"admin"` con contraseña `"password"`) y, si no lo encuentra, lanza una excepción `UsernameNotFoundException`.
> 5. `AuthController.java`: Un controlador REST con el endpoint `/api/auth/login` que recibe un `AuthenticationRequestDto`, autentica las credenciales, genera un token JWT con `JwtUtil` y lo devuelve en un `AuthenticationResponseDto`.
> 6. **Clases Auxiliares para Autenticación**: Crea las clases `AuthenticationRequestDto` y `AuthenticationResponseDto` usando el patrón de **Java Records** para garantizar concisión e inmutabilidad.

### b. Frontend (Angular)

Desde el frontend, debemos gestionar el ciclo de vida de los tokens de seguridad y asegurar que la experiencia del usuario sea fluida y segura.

- **Estrategia de Seguridad: JWT**: Después de que un usuario se autentica correctamente en el backend, este le devuelve un token JWT. Este token es esencialmente una credencial digital firmada. El frontend lo almacena de forma segura (por ejemplo, en el `localStorage` o `sessionStorage`) y lo envía en cada petición para demostrar que el usuario está logueado y tiene permiso.
- **Uso de `Interceptors`**: En Angular, un **interceptor** es un servicio que se encarga de interceptar y modificar las peticiones HTTP que salen de nuestra aplicación, así como las respuestas que entran. Es un lugar perfecto para adjuntar automáticamente el token de autenticación a la cabecera de cada petición saliente sin tener que hacerlo manualmente en cada servicio.

  ```typescript
  // src/app/auth.interceptor.ts
  import { HttpInterceptorFn } from '@angular/common/http';

  export const authInterceptor: HttpInterceptorFn = (req, next) => {
    const authToken = typeof localStorage !== 'undefined' ? localStorage.getItem('auth_token') : null;

    if (authToken) {
      // Clona la petición y añade el token en la cabecera de autorización.
      const authReq = req.clone({
        headers: req.headers.set('Authorization', `Bearer ${authToken}`)
      });
      return next(authReq);
    }

    // Si no hay token, continúa con la petición original.
    return next(req);
  };
  ```

Para que el usuario pueda interactuar con el sistema de autenticación, necesitamos un servicio que gestione el login y un componente para la interfaz, además de las rutas para navegar entre ellos.

1. **Servicio de Autenticación (`auth.service.ts`)**: Este servicio se encargará de realizar la petición al backend, recibir el token JWT y almacenarlo en el navegador.

    - **Archivo: `src/app/services/auth.service.ts`**

      ```typescript
      import { Injectable } from '@angular/core';
      import { HttpClient } from '@angular/common/http';
      import { Observable } from 'rxjs';
      import { tap } from 'rxjs/operators';

      /**
       * Interfaz para la respuesta del backend tras un login exitoso.
      */
      interface AuthResponse {
        jwt: string;
      }

      @Injectable({
        providedIn: 'root'
      })
      export class AuthService {
        private apiUrl = 'http://localhost:8080/api/auth';

        constructor(private http: HttpClient) {}

        /**
         * Realiza una petición de login al backend.
        * @param username El nombre de usuario.
        * @param password La contraseña.
        * @returns Un Observable con la respuesta del servidor.
        */
        login(username: string, password: string): Observable<AuthResponse> {
          // La petición POST envía un objeto con el username y el password.
          return this.http.post<AuthResponse>(`${this.apiUrl}/login`, { username, password }).pipe(
            tap((response: AuthResponse) => {
              // Almacena el token en el localStorage al recibir una respuesta exitosa
              localStorage.setItem('auth_token', response.jwt);
            })
          );
        }

        /**
         * Verifica si el usuario está autenticado.
        * @returns true si hay un token en el localStorage, de lo contrario false.
        */
        isAuthenticated(): boolean {
          return !!localStorage.getItem('auth_token');
        }

        /**
         * Cierra la sesión del usuario.
        */
        logout(): void {
          localStorage.removeItem('auth_token');
        }
      }
      ```

2. **Componente de Login (`login.component.ts` y `.html`)**: Este componente proporciona la interfaz de usuario para que el usuario ingrese sus credenciales.

    - **Archivo**: `src/app/login/login.component.ts`

      ```typescript
      import { Component, OnInit } from '@angular/core';
      import { CommonModule } from '@angular/common';
      import { FormsModule } from '@angular/forms';
      import { Router } from '@angular/router';
      import { AuthService } from '../services/auth.service';

      @Component({
        selector: 'app-login',
        standalone: true,
        imports: [CommonModule, FormsModule],
        templateUrl: './login.component.html',
        styleUrl: './login.component.css'
      })
      export class LoginComponent implements OnInit {
        username = '';
        password = '';
        loginError = false;

        constructor(private authService: AuthService, private router: Router) { }

        ngOnInit(): void {
          // Si el usuario ya está autenticado, redirigir a la página principal
          if (this.authService.isAuthenticated()) {
            this.router.navigate(['/productos']);
          }
        }

        onLogin(): void {
          this.loginError = false;
          this.authService.login(this.username, this.password).subscribe({
            next: () => {
              // Redirigir a la página principal tras un login exitoso
              this.router.navigate(['/productos']);
            },
            error: (err) => {
              console.error('Login failed', err);
              this.loginError = true;
            }
          });
        }
      }
      ```

    - **Archivo**: `src/app/login/login.component.html`

      ```html
      <div class="login-container">
        <div class="login-card">
          <h2 class="login-header">Iniciar Sesión</h2>
          <form (ngSubmit)="onLogin()" #loginForm="ngForm">
            <div class="form-group">
              <label for="username" class="form-label">Usuario</label>
              <input type="text" id="username" name="username" [(ngModel)]="username" required
                    class="form-input">
            </div>
            <div class="form-group">
              <label for="password" class="form-label">Contraseña</label>
              <input type="password" id="password" name="password" [(ngModel)]="password" required
                    class="form-input">
            </div>
            <div *ngIf="loginError" class="error-message">
              Credenciales incorrectas. Inténtalo de nuevo.
            </div>
            <div class="form-actions">
              <button type="submit"
                      class="login-button"
                      [disabled]="!loginForm.form.valid">
                Ingresar
              </button>
            </div>
          </form>
        </div>
      </div>
      ```

    - **Archivo**: `src/app/login/login.component.css`

      ```css
      .login-container {
          display: flex;
          align-items: center;
          justify-content: center;
          min-height: 100vh;
          background-color: #f3f4f6;
          padding: 1rem;
      }

      .login-card {
          background-color: #fff;
          padding: 2rem;
          border-radius: 0.5rem;
          box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
          width: 100%;
          max-width: 24rem;
      }

      .login-header {
          font-size: 1.875rem;
          font-weight: bold;
          text-align: center;
          color: #1f2937;
          margin-bottom: 1.5rem;
      }

      .form-group {
          margin-bottom: 1rem;
      }

      .form-label {
          display: block;
          color: #374151;
          font-size: 0.875rem;
          font-weight: 600;
          margin-bottom: 0.5rem;
      }

      .form-input {
          box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
          appearance: none;
          border: 1px solid #d1d5db;
          border-radius: 0.5rem;
          width: 100%;
          padding: 0.5rem 0.75rem;
          color: #4b5563;
          line-height: 1.25;
          transition: all 0.2s;
      }

      .form-input:focus {
          outline: none;
          box-shadow: 0 0 0 2px rgba(99, 102, 241, 0.5);
          border-color: #6366f1;
      }

      .error-message {
          text-align: center;
          font-size: 0.875rem;
          color: #dc2626;
          margin-bottom: 1rem;
      }

      .form-actions {
          display: flex;
          align-items: center;
          justify-content: space-between;
      }

      .login-button {
          width: 100%;
          background-color: #4f46e5;
          color: #fff;
          font-weight: bold;
          padding: 0.5rem 1rem;
          border-radius: 0.5rem;
          transition: background-color 0.2s;
          cursor: pointer;
      }

      .login-button:hover {
          background-color: #4338ca;
      }

      .login-button:disabled {
          opacity: 0.5;
          cursor: not-allowed;
      }
      ```

3. **Guards de Rutas en Angular**

    Los **Guards** (guardianes) son una característica de Angular que controla el acceso a las rutas. Son como porteros que deciden si un usuario puede navegar a una ruta específica. Se ejecutan antes de que se complete la navegación y son ideales para tareas como:

    - Verificar si un usuario está autenticado antes de permitirle ver una página.
    - Confirmar si el usuario desea abandonar una página (por ejemplo, si tiene cambios no guardados en un formulario).
    - Resolver datos de una API antes de que se cargue la ruta.

    Al igual que los interceptores, los guards ahora tienen una sintaxis funcional más moderna, lo que los hace más sencillos de implementar.

    - **Archivo**: `src/app/auth.guard.ts`

      ```typescript
      import { CanActivateFn, Router } from '@angular/router';
      import { inject } from '@angular/core';
      import { AuthService } from './services/auth.service';

      export const authGuard: CanActivateFn = (route, state) => {
        const authService = inject(AuthService);
        const router = inject(Router);

        // Inyectamos el servicio de autenticación
        if (authService.isAuthenticated()) {
          return true; // Si el usuario está autenticado, permite el acceso a la ruta
        } else {
          router.navigate(['/login']); // Si no está autenticado, redirige al login
          return false; // Deniega el acceso
        }
      };
      ```

    - **Archivo**: `src/app/app.routes.ts`

      ```typescript
      import { Routes } from '@angular/router';
      import { LoginComponent } from './login/login.component';
      import { ProductoComponent } from './producto/producto.component';
      import { authGuard } from './auth.guard'; // Importamos el guard

      export const routes: Routes = [
          { path: '', redirectTo: '/login', pathMatch: 'full' },
          { path: 'login', component: LoginComponent },
          { path: 'productos', component: ProductoComponent, canActivate: [authGuard] }
      ];
      ```

    En el archivo `app.routes.ts`, usamos `canActivate: [authGuard]` para proteger la ruta de `/productos`, asegurando que nadie pueda acceder a ella sin haber iniciado sesión.

**Prompt para IA**:

> Genera los siguientes componentes de Angular para asegurar la API:
>
> 1. Un servicio **AuthService** que maneje el inicio y cierre de sesión, y que almacene el JWT en el localStorage. Este servicio debe tener un método `isAuthenticated()` para verificar la sesión.
> 2. Un **authInterceptor** que adjunte el JWT al encabezado `Authorization` de todas las peticiones salientes.
> 3. Un **authGuard** para proteger las rutas, redirigiendo al usuario al login si no está autenticado. Utiliza la sintaxis funcional moderna y asegúrate de que el interceptor y el guard se registren correctamente en `app.config.ts`."

## 3. Mejores Prácticas y Futuro

### Pruebas de Software: La base de la calidad

Un software robusto y escalable se construye sobre una base sólida de pruebas. La práctica de **Test-Driven Development (TDD)**, aunque a menudo vista como un proceso complejo, garantiza que cada componente y función se comporte como se espera. Se recomienda implementar tres tipos de pruebas principales:

- **Pruebas unitarias**: Validan el comportamiento de las unidades de código más pequeñas, como funciones o métodos individuales. Herramientas como **JUnit** (en Java) o **Jasmine** y **Jest** (en JavaScript) son esenciales.
- **Pruebas de integración**: Confirman que los diferentes componentes de la aplicación (por ejemplo, un servicio y un controlador) trabajan juntos de manera correcta.
- **Pruebas de extremo a extremo (E2E)**: Simulan el flujo completo de un usuario a través de la aplicación, desde la interfaz de usuario hasta el backend, garantizando que todo el sistema funciona sin problemas.

### Código Limpio, Mantenimiento y Escalabilidad

Mantener un código limpio y una estructura bien definida es clave para el desarrollo profesional. Esto incluye:

- **Modularidad**: Organizar el código en módulos y componentes independientes para reducir la complejidad y facilitar la reutilización.
- **Patrones de diseño**: Utilizar patrones como el **Repository Pattern** para abstraer la capa de datos, lo que hace el código más flexible y fácil de mantener.
- **Documentación**: Comentar el código y los flujos de trabajo complejos para que otros desarrolladores puedan entenderlo rápidamente.

### Seguridad Adicional: Más allá de la autenticación

Además de la seguridad con JWT, una aplicación robusta debe protegerse de otras vulnerabilidades comunes:

- **Validación y sanitización de entradas**: Proteger la aplicación de ataques de inyección (SQL, NoSQL, etc.) validando y limpiando todos los datos que ingresan.
- **Protección contra ataques XSS y CSRF**: Implementar cabeceras de seguridad y tokens anti-CSRF para prevenir que scripts maliciosos o peticiones no deseadas comprometan la seguridad de los usuarios.

### El Futuro de la IA en el Desarrollo de Software

La Inteligencia Artificial ya está transformando el desarrollo de software, pasando de ser una curiosidad a una herramienta indispensable. En el presente, herramientas como los **asistentes de código** (por ejemplo, GitHub Copilot) y los **generadores de pruebas automáticas** aceleran la codificación.

En el futuro, la IA podría tomar un rol más protagónico, ayudando con:

- **Análisis de seguridad avanzado**: Identificar patrones de vulnerabilidad en el código antes de que sean explotados.
- **Refactorización y optimización automatizada**: Sugerir y aplicar mejoras al rendimiento del código de manera inteligente.
- **Diseño de UI/UX asistido**: Generar prototipos funcionales basados en descripciones de lenguaje natural.

Esto no significa que los desarrolladores serán reemplazados, sino que el rol del programador evolucionará. La IA asumirá tareas repetitivas y de baja complejidad, permitiendo a los desarrolladores enfocarse en la arquitectura, la resolución de problemas complejos y la **creatividad**.
