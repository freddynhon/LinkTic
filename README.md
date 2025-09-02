# LinkTic
Prueba técnica - líder técnico

# 🛒 Microservicios: Productos e Inventarios

Este proyecto implementa dos microservicios en **Spring Boot** que se comunican entre sí mediante **HTTP + JSON API** y están protegidos con **API Key Authentication**.  

- **productos-service** → Gestiona productos (creación, consulta, actualización).  
- **inventarios-service** → Administra el stock disponible de los productos y se integra con `productos-service`.  

---

## 📦 1. Instalación y ejecución

### 🔹 Requisitos previos
- **Java 17+**
- **Maven 3.9+**
- **Docker & Docker Compose** (para ejecutar en contenedores)

### 🔹 Ejecución local (sin Docker)
1. Clonar el repositorio.
2. Entrar en cada microservicio:
   ```bash
   cd productos-service
   mvn spring-boot:run

En otra terminal:
   cd inventarios-service
    mvn spring-boot:run

### 🔹 Documentación en Swagger:
Productos → http://localhost:8083/swagger-ui.html
Inventarios → http://localhost:8084/swagger-ui.html

🔹 Ejecución con Docker
Desde la carpeta raíz del proyecto:
    docker-compose up --build

Los servicios quedarán disponibles en:
productos-service → puerto 8083
inventarios-service → puerto 8084

## 2. Arquitectura
La arquitectura sigue un modelo de microservicios independientes con comunicación HTTP.
Cada servicio tiene su propia base de datos H2.
La comunicación entre inventarios-service y productos-service se realiza vía REST con API Key como autenticación básica.
Swagger/OpenAPI está habilitado en ambos servicios para documentación interactiva.
La API-KEY para que respondan los servicios en Swagger es SECRET_TEST

## ⚖️ 3. Decisiones técnicas y justificaciones
Spring Boot 3.2.7 → Versión estable con soporte OSS vigente.
H2 Database → Base de datos en memoria, ideal para pruebas rápidas.
REST + JSON API → Simplicidad y compatibilidad.
API Key Authentication → Mecanismo básico para seguridad entre servicios sin necesidad de OAuth/JWT.
Docker → Garantiza portabilidad y despliegue homogéneo.
Swagger (springdoc-openapi) → Facilita pruebas y documentación de endpoints.
Pruebas unitarias con JUnit + Mockito → Garantizan calidad en operaciones críticas:
Creación y actualización de productos.
Comunicación entre microservicios.
Manejo de errores.

## 🔄 4. Diagrama de interacción entre servicios
             ┌──────────────────┐
             │                  │
             │  productos-service│
             │                  │
             └───────▲──────────┘
                     │
    (API Key Auth)   │  Consultar producto por ID
                     │
             ┌───────┴──────────┐
             │                  │
             │ inventarios-service
             │                  │
             └───────▲──────────┘
                     │
                     │  Cliente (Swagger, curl, etc.)
                     │
             ┌───────┴──────────┐
             │                  │
             │   Usuario/Cliente │
             │                  │
             └──────────────────┘
- El cliente puede consultar y modificar inventarios mediante inventarios-service.
- inventarios-service consulta información de productos en productos-service.
- La comunicación está autenticada mediante API Key compartida.

## 📚 5. Endpoints principales
Productos (localhost:8083)
POST /api/productos → Crear producto
GET /api/productos/{id} → Consultar producto por ID
PUT /api/productos/{id} → Actualizar producto
Inventarios (localhost:8084)
POST /api/inventarios → Registrar stock inicial
GET /api/inventarios/{productoId} → Consultar cantidad disponible
PUT /api/inventarios/{productoId}/actualizar → Actualizar inventario después de compra

## 🧪 6. Pruebas unitarias
Ejecutar con:
    mvn test
Las pruebas cubren:
Creación y actualización de productos.
Validación de comunicación entre servicios con MockRestTemplate.
Manejo de errores (404 producto no encontrado, 500 error comunicación).

## 7. Recomendaciones de versionamiento
Se recomienda usar versionado en la URL junto con versionado semántico (SemVer) en la documentación.
URL → Permite a cualquier persona apuntar claramente a la versión deseada:
    /api/v1/productos
    /api/v1/inventarios

## Nota:
    Con los pasos anteriormente descritos, cualquier persona puede seguir un patron de diseño de aplicaciones claro basado en la estructura de microservicios que se construyó para este proyecto.

## 8. Propuesta de mejoras futuras para robustecer la aplicación y volverla más escalable:
    - Poner un modelo de autoscaling en la implementación final.
    - Cambiar el modelo de autenticación por uno más robusto con OAuth 2.1
    - Implementar JWT Tokens firmados para autenticación entre microservicios.
    - Implementar un modelo de monitoreo de los microservicios en la infraestructura elegida.
    - A nivel de base de datos ya no debería estar en momeoria. Recomendaría un modelo con bases de datos no relacional que va a permitir una amplia escalabilidad y buen uso de la información.
