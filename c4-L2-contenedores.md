# C4 Nivel 2: Diagrama de Contenedores

[⬅️ Volver al índice](./index-c4.md) | [⬆️ Nivel anterior: Contexto](./c4-L1-contexto.md)

---

## 📖 Descripción

El **Diagrama de Contenedores** descompone el sistema Road Warrior en sus contenedores principales: aplicaciones cliente, servicios backend, bases de datos y sistemas de integración. Cada contenedor es una unidad desplegable independiente.

### Alcance

- **Sistema:** Road Warrior Trip Management Dashboard
- **Enfoque:** Arquitectura de contenedores y tecnologías principales
- **Nivel de detalle:** Contenedores desplegables y sus interacciones

---

## 🗺️ Diagrama de Contenedores

```mermaid
graph TB
    %% Usuario
    User[👤 Viajero]

    %% Aplicaciones Cliente
    WebApp["💻 Aplicación Web<br/>[React SPA]<br/><br/>Interfaz web responsive para<br/>gestión de viajes"]
    MobileApp["📱 Aplicación Móvil<br/>[React Native]<br/><br/>App nativa iOS/Android para<br/>gestión de viajes en movimiento"]

    %% API Gateway
    APIGateway["🚪 API Gateway<br/>[Kong / AWS API Gateway]<br/><br/>Punto de entrada único,<br/>autenticación, rate limiting"]

    %% Servicios Backend
    UserService["👥 User Service<br/>[Node.js / Express]<br/><br/>Gestión de usuarios,<br/>autenticación y perfiles"]
    TripService["🧳 Trip Management Service<br/>[Java / Spring Boot]<br/><br/>Gestión de viajes,<br/>agrupación de reservas"]
    BookingService["📋 Booking Service<br/>[Java / Spring Boot]<br/><br/>Gestión de reservas individuales<br/>(vuelos, hoteles, autos)"]
    IntegrationService["🔌 Integration Service<br/>[Python / FastAPI]<br/><br/>Integración con sistemas<br/>externos de proveedores"]
    SharingService["🔗 Sharing Service<br/>[Node.js / Express]<br/><br/>Compartir itinerarios<br/>en redes sociales"]
    NotificationService["🔔 Notification Service<br/>[Node.js / Express]<br/><br/>Envío de notificaciones<br/>y recordatorios"]

    %% Almacenamiento
    UserDB[("👤 User Database<br/>[PostgreSQL]<br/><br/>Usuarios, perfiles,<br/>credenciales")]
    TripDB[("🧳 Trip Database<br/>[PostgreSQL]<br/><br/>Viajes y agrupaciones<br/>de reservas")]
    BookingDB[("📋 Booking Database<br/>[PostgreSQL]<br/><br/>Reservas de vuelos,<br/>hoteles y autos")]
    Cache["⚡ Cache<br/>[Redis]<br/><br/>Cache de sesiones,<br/>datos de proveedores"]
    MessageQueue["📬 Message Queue<br/>[RabbitMQ / AWS SQS]<br/><br/>Comunicación asíncrona<br/>entre servicios"]

    %% Sistemas Externos
    ExtSystems["🌐 Sistemas Externos<br/>(Aerolíneas, Hoteles, Autos)"]
    SocialMedia["📱 Redes Sociales<br/>(Facebook, Twitter, Instagram)"]
    EmailService["📧 Email Service<br/>(SendGrid / AWS SES)"]

    %% Relaciones - Cliente
    User -->|HTTPS| WebApp
    User -->|HTTPS| MobileApp

    WebApp -->|JSON/HTTPS| APIGateway
    MobileApp -->|JSON/HTTPS| APIGateway

    %% Relaciones - API Gateway a Servicios
    APIGateway -->|REST API| UserService
    APIGateway -->|REST API| TripService
    APIGateway -->|REST API| BookingService
    APIGateway -->|REST API| SharingService

    %% Relaciones - Servicios a Bases de Datos
    UserService -->|SQL| UserDB
    TripService -->|SQL| TripDB
    BookingService -->|SQL| BookingDB

    %% Relaciones - Cache
    UserService -.->|Lee/Escribe| Cache
    TripService -.->|Lee/Escribe| Cache
    BookingService -.->|Lee/Escribe| Cache
    IntegrationService -.->|Cache de datos<br/>de proveedores| Cache

    %% Relaciones - Message Queue
    TripService -->|Publica eventos| MessageQueue
    BookingService -->|Publica eventos| MessageQueue
    IntegrationService -->|Consume eventos| MessageQueue
    NotificationService -->|Consume eventos| MessageQueue

    %% Relaciones - Integration Service
    IntegrationService -->|APIs REST/SOAP| ExtSystems
    BookingService -->|Solicita sincronización| IntegrationService

    %% Relaciones - Sharing Service
    SharingService -->|OAuth 2.0| SocialMedia

    %% Relaciones - Notification Service
    NotificationService -->|SMTP/API| EmailService

    %% Links a componentes (Nivel 3)
    click IntegrationService "./c4-L3-01-integration-service.md" "Ver componentes del Integration Service"
    click TripService "./c4-L3-02-trip-management.md" "Ver componentes del Trip Management Service"
    click BookingService "./c4-L3-03-booking-service.md" "Ver componentes del Booking Service"
    click UserService "./c4-L3-04-user-service.md" "Ver componentes del User Service"
    click SharingService "./c4-L3-05-sharing-service.md" "Ver componentes del Sharing Service"

    %% Estilos
    classDef frontend fill:#438dd5,stroke:#2e6295,color:#ffffff
    classDef backend fill:#85bbf0,stroke:#5d9dd5,color:#000000
    classDef database fill:#2e7d32,stroke:#1b5e20,color:#ffffff
    classDef infrastructure fill:#ff9800,stroke:#e68900,color:#000000
    classDef external fill:#999999,stroke:#6b6b6b,color:#ffffff

    class WebApp,MobileApp frontend
    class APIGateway,UserService,TripService,BookingService,IntegrationService,SharingService,NotificationService backend
    class UserDB,TripDB,BookingDB database
    class Cache,MessageQueue infrastructure
    class ExtSystems,SocialMedia,EmailService external
```

---

## 🔍 Elementos del Diagrama

### Aplicaciones Cliente

| Contenedor | Tecnología | Descripción | Responsabilidades |
|------------|------------|-------------|-------------------|
| **Aplicación Web** | React SPA | Single Page Application responsive | - Interfaz de usuario web<br/>- Gestión de viajes y reservas<br/>- Visualización de itinerarios |
| **Aplicación Móvil** | React Native | Aplicación nativa multiplataforma | - Interfaz móvil iOS/Android<br/>- Notificaciones push<br/>- Acceso offline |

### Capa de API

| Contenedor | Tecnología | Descripción | Responsabilidades |
|------------|------------|-------------|-------------------|
| **API Gateway** | Kong / AWS API Gateway | Puerta de entrada a los servicios | - Enrutamiento de requests<br/>- Autenticación JWT<br/>- Rate limiting<br/>- SSL/TLS termination |

### Servicios Backend (Microservicios)

| Contenedor | Tecnología | Descripción | Responsabilidades |
|------------|------------|-------------|-------------------|
| **Trip Management Service** | Java / Spring Boot | Gestión de viajes | - Crear y organizar viajes<br/>- Agrupar reservas por viaje<br/>- Eliminar viajes completados<br/>[Ver componentes →](./c4-L3-02-trip-management.md) |
| **Integration Service** | Python / FastAPI | Integración con proveedores | - Conectores para aerolíneas<br/>- Conectores para hoteles<br/>- Conectores para alquiler de autos<br/>- Mapeo de datos heterogéneos<br/>[Ver componentes →](./c4-L3-01-integration-service.md) |

### Almacenamiento de Datos

| Contenedor | Tecnología | Descripción | Datos Almacenados |
|------------|------------|-------------|-------------------|
| **User Database** | PostgreSQL | Base de datos de usuarios | - Usuarios y credenciales<br/>- Perfiles de viajero<br/>- Programas de fidelización |
| **Trip Database** | PostgreSQL | Base de datos de viajes | - Viajes y su estado<br/>- Agrupaciones de reservas<br/>- Fechas de inicio/fin |
| **Booking Database** | PostgreSQL | Base de datos de reservas | - Reservas de vuelos<br/>- Reservas de hoteles<br/>- Reservas de autos |

### Infraestructura

| Contenedor | Tecnología | Descripción | Uso |
|------------|------------|-------------|-----|
| **Cache** | Redis | Cache en memoria | - Sesiones de usuario<br/>- Datos de proveedores<br/>- Respuestas de APIs externas |
| **Message Queue** | RabbitMQ / AWS SQS | Cola de mensajes | - Eventos de dominio<br/>- Integración asíncrona<br/>- Procesamiento en segundo plano |

---

## 🔄 Flujos de Datos Principales

### 1. Login y Autenticación
```
Usuario → Web/Mobile App → API Gateway → User Service → User DB
                                                ↓
                                              Cache (JWT Token)
```

### 2. Sincronización de Reservas
```
Scheduler → Integration Service → Sistemas Externos (Aerolíneas/Hoteles/Autos)
                ↓
         Message Queue → Booking Service → Booking DB
                                ↓
                         Message Queue → Trip Service → Trip DB
```

### 3. Consulta de Viajes
```
Usuario → Web/Mobile App → API Gateway → Trip Service → Trip DB
                                              ↓
                                         (Check Cache)
                                              ↓
                                      Booking Service → Booking DB
```

### 4. Compartir Itinerario
```
Usuario → Web/Mobile App → API Gateway → Sharing Service → Redes Sociales
                                              ↓
                                         Trip Service → Trip DB
```

### 5. Ingreso Manual de Reserva
```
Usuario → Web/Mobile App → API Gateway → Booking Service → Booking DB
                                              ↓
                                       Message Queue → Trip Service
                                                           ↓
                                                  Auto-asignar a viaje
```

---

## 🏗️ Decisiones Arquitectónicas

### Arquitectura de Microservicios

**Decisión:** Adoptar una arquitectura de microservicios para los servicios backend.

**Razones:**
- **Escalabilidad independiente:** Cada servicio puede escalar según su demanda (ej: Integration Service durante sincronizaciones masivas)
- **Mantenibilidad:** Equipos independientes pueden trabajar en diferentes servicios
- **Tecnología heterogénea:** Permite usar el stack tecnológico más apropiado para cada servicio
- **Resiliencia:** Fallo de un servicio no compromete todo el sistema

### API Gateway

**Decisión:** Implementar un API Gateway como punto de entrada único.

**Razones:**
- **Seguridad centralizada:** Autenticación y autorización en un solo punto
- **Simplicidad para clientes:** Los clientes no necesitan conocer múltiples endpoints
- **Rate limiting:** Protección contra abuso de APIs
- **Monitoreo centralizado:** Observabilidad de todas las peticiones

### Event-Driven con Message Queue

**Decisión:** Usar comunicación asíncrona basada en eventos para integración entre servicios.

**Razones:**
- **Desacoplamiento:** Servicios no necesitan conocerse directamente
- **Resiliencia:** Si un servicio está caído, los mensajes se procesan cuando esté disponible
- **Escalabilidad:** Procesamiento asíncrono de operaciones pesadas
- **Eventual consistency:** Apropiado para el dominio (las reservas no cambian en tiempo real)

### Cache con Redis

**Decisión:** Implementar una capa de cache compartida.

**Razones:**
- **Performance:** Reducir latencia en consultas frecuentes
- **Reducción de carga en bases de datos:** Menos queries a PostgreSQL
- **Cache de datos externos:** Evitar llamadas repetidas a APIs de proveedores
- **Sesiones distribuidas:** Compartir sesiones entre instancias de servicios

### Bases de Datos Separadas por Servicio

**Decisión:** Cada microservicio tiene su propia base de datos.

**Razones:**
- **Independencia de datos:** Cada servicio es dueño de sus datos
- **Escalabilidad independiente:** Cada DB puede optimizarse según su servicio
- **Cambios sin impacto:** Cambios en esquema no afectan otros servicios
- **Mejor alineación con DDD:** Bounded contexts claros

---

## 📊 Atributos de Calidad

### Interoperabilidad
- **Integration Service** centraliza la complejidad de integración con múltiples proveedores
- Adaptadores específicos por proveedor
- Mapeo de datos heterogéneos a modelo unificado

### Disponibilidad
- **API Gateway** con balanceo de carga
- Servicios stateless que pueden replicarse horizontalmente
- **Cache** reduce dependencia de sistemas externos
- **Message Queue** permite procesamiento asíncrono sin bloqueos

### Seguridad
- **API Gateway** maneja autenticación JWT
- **User Service** centraliza gestión de credenciales
- Comunicación interna entre servicios en red privada
- Credenciales de proveedores encriptadas en **User DB**

### Escalabilidad
- Microservicios pueden escalar independientemente
- **Cache** reduce carga en bases de datos
- **Message Queue** permite procesamiento asíncrono de picos de carga
- Bases de datos pueden particionarse por servicio

---

## ➡️ Siguiente Nivel

Para ver los componentes internos de cada servicio, consulte:

### Componentes de Servicios Backend

- [**Integration Service - Componentes**](./c4-L3-01-integration-service.md)
- [**Trip Management Service - Componentes**](./c4-L3-02-trip-management.md)

---

[⬅️ Volver al índice](./index-c4.md) | [⬆️ Nivel anterior: Contexto](./c4-L1-contexto.md)
