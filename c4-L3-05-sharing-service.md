# C4 Nivel 3: Componentes - Sharing Service

[⬅️ Volver al índice](./index-c4.md) | [⬆️ Nivel anterior: Contenedores](./c4-L2-contenedores.md)

---

## 📖 Descripción

El **Sharing Service** permite a los usuarios compartir sus itinerarios de viaje en redes sociales y generar links públicos. Gestiona la integración OAuth con plataformas sociales y la generación de vistas compartibles de los viajes.

### Alcance

- **Contenedor:** Sharing Service
- **Tecnología:** Node.js / Express
- **Responsabilidad:** Compartir itinerarios en redes sociales y generar links públicos

---

## 🗺️ Diagrama de Componentes

```mermaid
graph TB
    %% Componentes externos
    APIGateway["🚪 API Gateway"]
    TripService["🧳 Trip Management Service"]
    BookingService["📋 Booking Service"]
    SocialMedia["📱 Redes Sociales<br/>(Facebook, Twitter, Instagram)"]
    Cache["⚡ Cache"]

    %% Componentes del Sharing Service
    subgraph SharingService["🔗 Sharing Service"]

        %% API Controllers
        ShareController["📤 Share Controller<br/>[Express Router]<br/><br/>Endpoints para compartir<br/>itinerarios"]

        PublicLinkController["🔗 Public Link Controller<br/>[Express Router]<br/><br/>Endpoints para generar<br/>links públicos"]

        OAuthController["🔐 OAuth Controller<br/>[Express Router]<br/><br/>Callbacks de OAuth<br/>de redes sociales"]

        %% Application Services
        SharingAppService["🎯 Sharing Application Service<br/>[Service Class]<br/><br/>Orquestación de compartir<br/>en redes sociales"]

        PublicLinkService["🔗 Public Link Service<br/>[Service Class]<br/><br/>Generación y gestión<br/>de links públicos"]

        %% Domain Services
        ItineraryFormatter["📋 Itinerary Formatter<br/>[Domain Service]<br/><br/>Formateo de itinerarios<br/>para compartir"]

        SocialMediaPublisher["📱 Social Media Publisher<br/>[Domain Service]<br/><br/>Publicación en<br/>redes sociales"]

        %% Social Media Adapters
        subgraph SocialAdapters["Adaptadores de Redes Sociales"]
            FacebookAdapter["📘 Facebook Adapter<br/>[Adapter]<br/><br/>Publicación en Facebook"]

            TwitterAdapter["🐦 Twitter Adapter<br/>[Adapter]<br/><br/>Publicación en Twitter (X)"]

            InstagramAdapter["📷 Instagram Adapter<br/>[Adapter]<br/><br/>Publicación en Instagram"]
        end

        %% OAuth Management
        OAuthManager["🔐 OAuth Manager<br/>[Service]<br/><br/>Gestión de flujo OAuth 2.0<br/>y tokens de acceso"]

        TokenStore["🎫 Token Store<br/>[Repository]<br/><br/>Almacenamiento de<br/>access tokens sociales"]

        %% Content Generation
        ImageGenerator["🖼️ Image Generator<br/>[Service]<br/><br/>Genera imágenes de<br/>itinerarios (PNG/JPG)"]

        TemplateEngine["📝 Template Engine<br/>[Service]<br/><br/>Renderiza templates<br/>de itinerarios (HTML)"]

        %% Link Management
        ShortLinkGenerator["🔗 Short Link Generator<br/>[Service]<br/><br/>Genera URLs cortas<br/>para compartir"]

        PublicLinkRepository["💾 Public Link Repository<br/>[Repository]<br/><br/>Persistencia de<br/>links públicos"]

        %% Privacy & Security
        PrivacyFilter["🔒 Privacy Filter<br/>[Service]<br/><br/>Filtra datos sensibles<br/>antes de compartir"]

        AccessValidator["✅ Access Validator<br/>[Validator]<br/><br/>Valida acceso a<br/>links públicos"]

        %% Utilities
        CacheManager["⚡ Cache Manager<br/>[Cache Service]<br/><br/>Cache de itinerarios<br/>renderizados"]
    end

    %% Relaciones externas
    APIGateway -->|POST /share/social| ShareController
    APIGateway -->|POST /share/link| PublicLinkController
    APIGateway -->|GET /share/public/:token| PublicLinkController
    APIGateway -->|GET /share/oauth/callback| OAuthController

    %% Controllers a Services
    ShareController --> SharingAppService
    PublicLinkController --> PublicLinkService
    OAuthController --> OAuthManager

    %% Application Services
    SharingAppService --> ItineraryFormatter
    SharingAppService --> SocialMediaPublisher
    SharingAppService --> OAuthManager
    PublicLinkService --> ItineraryFormatter
    PublicLinkService --> ShortLinkGenerator

    %% Itinerary Formatter obtiene datos
    ItineraryFormatter -->|GET /trips/{id}| TripService
    ItineraryFormatter -->|GET /bookings| BookingService

    %% Social Media Publisher
    SocialMediaPublisher --> FacebookAdapter
    SocialMediaPublisher --> TwitterAdapter
    SocialMediaPublisher --> InstagramAdapter

    %% Adapters a redes sociales
    FacebookAdapter -->|OAuth 2.0 + Graph API| SocialMedia
    TwitterAdapter -->|OAuth 2.0 + API v2| SocialMedia
    InstagramAdapter -->|OAuth 2.0 + Graph API| SocialMedia

    %% OAuth
    OAuthManager --> TokenStore
    FacebookAdapter -.->|Usa tokens| OAuthManager
    TwitterAdapter -.->|Usa tokens| OAuthManager
    InstagramAdapter -.->|Usa tokens| OAuthManager

    %% Content Generation
    ItineraryFormatter --> TemplateEngine
    ItineraryFormatter --> ImageGenerator
    ItineraryFormatter --> PrivacyFilter

    %% Link Management
    ShortLinkGenerator --> PublicLinkRepository
    PublicLinkService --> AccessValidator

    %% Cache
    ItineraryFormatter --> CacheManager
    CacheManager -.->|Lee/Escribe| Cache

    %% Estilos
    classDef controller fill:#85bbf0,stroke:#5d9dd5,color:#000000
    classDef appservice fill:#ffa726,stroke:#f57c00,color:#000000
    classDef domainservice fill:#66bb6a,stroke:#43a047,color:#000000
    classDef adapter fill:#a5d6a7,stroke:#66bb6a,color:#000000
    classDef oauth fill:#e91e63,stroke:#c2185b,color:#ffffff
    classDef content fill:#9575cd,stroke:#7e57c2,color:#ffffff
    classDef utility fill:#90caf9,stroke:#42a5f5,color:#000000
    classDef external fill:#999999,stroke:#6b6b6b,color:#ffffff

    class ShareController,PublicLinkController,OAuthController controller
    class SharingAppService,PublicLinkService appservice
    class ItineraryFormatter,SocialMediaPublisher domainservice
    class FacebookAdapter,TwitterAdapter,InstagramAdapter adapter
    class OAuthManager,TokenStore oauth
    class ImageGenerator,TemplateEngine,ShortLinkGenerator,PublicLinkRepository,PrivacyFilter,AccessValidator content
    class CacheManager utility
    class APIGateway,TripService,BookingService,SocialMedia,Cache external
```

---

## 🔍 Componentes Detallados

### Capa de Presentación (API Controllers)

| Componente | Responsabilidad | Endpoints Clave |
|------------|-----------------|-----------------|
| **Share Controller** | - Compartir en redes sociales<br/>- Validación de requests | - `POST /share/social` - Compartir itinerario<br/>- `POST /share/preview` - Preview de compartir<br/>- `GET /share/social/status/{id}` - Estado de compartir |
| **Public Link Controller** | - Generar links públicos<br/>- Acceso a itinerarios públicos | - `POST /share/link` - Generar link público<br/>- `GET /share/public/:token` - Ver itinerario público<br/>- `DELETE /share/link/:id` - Revocar link<br/>- `GET /share/links` - Listar links del usuario |
| **OAuth Controller** | - Manejar callbacks de OAuth<br/>- Gestionar conexiones sociales | - `GET /share/oauth/callback` - Callback OAuth<br/>- `POST /share/oauth/connect` - Conectar red social<br/>- `DELETE /share/oauth/disconnect` - Desconectar |

### Capa de Aplicación

| Componente | Responsabilidad | Métodos Clave |
|------------|-----------------|---------------|
| **Sharing Application Service** | - Orquesta proceso de compartir<br/>- Coordina múltiples servicios<br/>- Maneja errores de publicación | - `shareToSocialMedia(tripId, platforms)`<br/>- `previewShare(tripId)`<br/>- `getShareStatus(shareId)` |
| **Public Link Service** | - Genera links públicos<br/>- Gestiona acceso y expiración<br/>- Revoca links | - `generatePublicLink(tripId, options)`<br/>- `getPublicItinerary(token)`<br/>- `revokeLink(linkId)`<br/>- `listUserLinks(userId)` |

### Capa de Dominio (Servicios)

| Componente | Responsabilidad | Lógica de Negocio |
|------------|-----------------|-------------------|
| **Itinerary Formatter** | - Obtiene datos de viaje<br/>- Formatea para visualización<br/>- Aplica filtros de privacidad<br/>- Genera múltiples formatos | - Consulta Trip Service y Booking Service<br/>- Filtra datos sensibles (ej: números de confirmación)<br/>- Genera HTML, JSON, imagen<br/>- Personaliza por red social |
| **Social Media Publisher** | - Publica en redes sociales<br/>- Maneja diferentes formatos<br/>- Retry en caso de error | - Selecciona adapter según plataforma<br/>- Formatea contenido específico<br/>- Maneja límites de caracteres<br/>- Adjunta imágenes |

### Adaptadores de Redes Sociales

| Componente | Plataforma | Funcionalidad | API Utilizada |
|------------|------------|---------------|---------------|
| **Facebook Adapter** | Facebook | - Publicar en timeline<br/>- Adjuntar imagen<br/>- Tags de ubicación | - OAuth 2.0<br/>- Graph API v18.0<br/>- Endpoint: `/me/feed` |
| **Twitter Adapter** | Twitter (X) | - Publicar tweet<br/>- Thread de tweets si es largo<br/>- Adjuntar imagen | - OAuth 2.0<br/>- Twitter API v2<br/>- Endpoint: `/tweets` |
| **Instagram Adapter** | Instagram | - Publicar imagen con caption<br/>- Hashtags automáticos | - OAuth 2.0<br/>- Instagram Graph API<br/>- Endpoint: `/media` |

### Gestión de OAuth

| Componente | Responsabilidad | Detalles |
|------------|-----------------|----------|
| **OAuth Manager** | - Flujo OAuth 2.0<br/>- Refresh de tokens<br/>- Gestión de scopes | - **Scopes requeridos:**<br/>  • Facebook: `publish_to_groups`, `publish_video`<br/>  • Twitter: `tweet.write`, `users.read`<br/>  • Instagram: `instagram_content_publish`<br/>- Refresh automático de tokens expirados |
| **Token Store** | - Almacenar access/refresh tokens<br/>- Encriptación de tokens<br/>- Asociación usuario-plataforma | - Tokens encriptados en DB<br/>- TTL por plataforma<br/>- Un token por usuario-plataforma |

### Generación de Contenido

| Componente | Responsabilidad | Tecnología |
|------------|-----------------|------------|
| **Image Generator** | - Genera imagen del itinerario<br/>- Layout responsive<br/>- Branding de Road Warrior | - Librería: Puppeteer (headless Chrome)<br/>- Renderiza HTML → PNG/JPG<br/>- Tamaños optimizados por red social<br/>- Marca de agua "Road Warrior" |
| **Template Engine** | - Renderiza templates HTML<br/>- Personalización por tipo | - Motor: Handlebars / EJS<br/>- Templates:<br/>  • Itinerario completo<br/>  • Resumen para redes sociales<br/>  • Vista pública |
| **Short Link Generator** | - Genera URLs cortas<br/>- Tokens únicos y seguros | - Base62 encoding<br/>- Longitud: 8 caracteres<br/>- Colisión checking<br/>- Formato: `share.roadwarrior.com/abc12345` |

### Privacidad y Seguridad

| Componente | Responsabilidad | Funcionalidades |
|------------|-----------------|-----------------|
| **Privacy Filter** | - Filtra datos sensibles<br/>- Configurable por usuario | - **Oculta por defecto:**<br/>  • Números de confirmación completos<br/>  • Información de pago<br/>  • Números de pasaporte<br/>- **Configurable:**<br/>  • Destinos específicos<br/>  • Fechas exactas<br/>  • Nombres de acompañantes |
| **Access Validator** | - Valida acceso a links públicos<br/>- Maneja expiración<br/>- Rate limiting | - Verifica token válido<br/>- Verifica no expirado<br/>- Verifica no revocado<br/>- Rate limit: 100 views/hora por IP |

### Infraestructura

| Componente | Responsabilidad | Configuración |
|------------|-----------------|---------------|
| **Cache Manager** | - Cache de itinerarios renderizados<br/>- Cache de imágenes generadas | - TTL: 1 hora<br/>- Invalidación en actualización de viaje<br/>- Cache key: `itinerary:{tripId}:{format}` |

---

## 🔄 Flujos de Datos

### 1. Compartir Itinerario en Facebook

```
Usuario → API Gateway → Share Controller
    (POST /share/social con tripId y platform=facebook)
                    ↓
            Sharing Application Service
                    ↓
            OAuth Manager (verificar token de Facebook válido)
                ↓           ↓
            ¿Token válido?  No → Retornar error (re-autenticar)
                ↓
              Sí
                ↓
            Itinerary Formatter
                ↓
            Trip Service (GET /trips/{id})
                ↓
            Booking Service (GET /bookings por trip)
                ↓
            Privacy Filter (ocultar datos sensibles)
                ↓
            Template Engine (renderizar HTML)
                ↓
            Image Generator (HTML → PNG)
                ↓
            Social Media Publisher
                ↓
            Facebook Adapter
                ↓
            Facebook Graph API (POST /me/feed)
                ↓
            Retornar link de publicación
```

### 2. Generar Link Público

```
Usuario → API Gateway → Public Link Controller
    (POST /share/link con tripId y opciones)
                    ↓
            Public Link Service
                    ↓
            Itinerary Formatter
                    ↓
            (Obtener datos del viaje - flujo similar)
                    ↓
            Privacy Filter (aplicar configuración de privacidad)
                    ↓
            Short Link Generator
                    ↓
            Generar token único (8 chars Base62)
                    ↓
            Public Link Repository (guardar en DB)
                ↓
            Retornar URL pública: share.roadwarrior.com/abc12345
```

### 3. Acceder a Itinerario Público

```
Usuario Público → GET share.roadwarrior.com/abc12345
                    ↓
            Public Link Controller
                    ↓
            Access Validator
                ↓           ↓           ↓
        ¿Token existe? ¿No expirado? ¿No revocado?
                ↓
            Todas OK
                ↓
            Cache Manager (check cache)
                ↓
            ¿Hit? → Sí → Retornar HTML cacheado
                ↓
              No
                ↓
            Itinerary Formatter
                ↓
            Template Engine (renderizar vista pública)
                ↓
            Cache Manager (guardar con TTL 1 hora)
                ↓
            Retornar HTML público (sin datos sensibles)
```

### 4. Conectar Cuenta de Red Social (OAuth Flow)

```
Usuario → Web App → Botón "Conectar Facebook"
                    ↓
            Redirect a Facebook OAuth
            (https://facebook.com/oauth/authorize?...)
                    ↓
            Usuario aprueba permisos en Facebook
                    ↓
            Facebook redirect a callback:
            GET /share/oauth/callback?code=...&state=...
                    ↓
            OAuth Controller
                    ↓
            OAuth Manager
                    ↓
            Exchange code por access token
            (POST https://facebook.com/oauth/token)
                    ↓
            Token Store (guardar access + refresh token encriptados)
                    ↓
            Retornar success
```

### 5. Publicar en Instagram (caso especial)

```
Usuario → Share Controller (POST /share/social platform=instagram)
                    ↓
            Sharing Application Service
                    ↓
            Itinerary Formatter
                    ↓
            Image Generator (genera imagen obligatoria para Instagram)
                    ↓
            Instagram Adapter
                ↓           ↓
        Paso 1: Upload imagen   Paso 2: Crear media container
            (POST /media)           con caption
                ↓
            Paso 3: Publicar container
                ↓
            Instagram Graph API
                ↓
            Retornar link de publicación
```

---

## 📊 Modelo de Datos

### Tabla: social_connections

| Campo | Tipo | Descripción |
|-------|------|-------------|
| id | UUID | PK |
| user_id | UUID | FK a usuarios |
| platform | VARCHAR(50) | FACEBOOK, TWITTER, INSTAGRAM |
| access_token_encrypted | TEXT | Access token encriptado |
| refresh_token_encrypted | TEXT | Refresh token encriptado |
| token_expires_at | TIMESTAMP | Expiración del access token |
| scopes | VARCHAR(255) | Scopes otorgados |
| platform_user_id | VARCHAR(100) | ID del usuario en la plataforma |
| platform_username | VARCHAR(100) | Username en la plataforma |
| connected_at | TIMESTAMP | Timestamp de conexión |
| updated_at | TIMESTAMP | Última actualización |

### Tabla: public_links

| Campo | Tipo | Descripción |
|-------|------|-------------|
| id | UUID | PK |
| user_id | UUID | FK a usuarios |
| trip_id | UUID | FK a viajes |
| token | VARCHAR(8) | Token único (Base62) |
| title | VARCHAR(200) | Título personalizado |
| expires_at | TIMESTAMP | Fecha de expiración (NULL = nunca) |
| view_count | INTEGER | Número de vistas |
| privacy_settings | JSONB | Configuración de privacidad |
| created_at | TIMESTAMP | Timestamp creación |
| revoked | BOOLEAN | Link revocado |
| revoked_at | TIMESTAMP | Timestamp revocación |

### Tabla: share_history

| Campo | Tipo | Descripción |
|-------|------|-------------|
| id | UUID | PK |
| user_id | UUID | FK a usuarios |
| trip_id | UUID | FK a viajes |
| platform | VARCHAR(50) | Plataforma donde se compartió |
| status | VARCHAR(20) | SUCCESS, FAILED, PENDING |
| platform_post_id | VARCHAR(255) | ID del post en la plataforma |
| platform_post_url | VARCHAR(500) | URL del post |
| error_message | TEXT | Mensaje de error (si falló) |
| created_at | TIMESTAMP | Timestamp del share |

### Índices

```sql
CREATE INDEX idx_social_connections_user_platform ON social_connections(user_id, platform);
CREATE UNIQUE INDEX idx_public_links_token ON public_links(token);
CREATE INDEX idx_public_links_user_id ON public_links(user_id);
CREATE INDEX idx_public_links_trip_id ON public_links(trip_id);
CREATE INDEX idx_share_history_user_id ON share_history(user_id);
CREATE INDEX idx_share_history_trip_id ON share_history(trip_id);
```

---

## 🎯 Patrones de Diseño Aplicados

### 1. Adapter Pattern
- **Uso:** FacebookAdapter, TwitterAdapter, InstagramAdapter
- **Beneficio:** Interfaz unificada para diferentes APIs de redes sociales

### 2. Template Method Pattern
- **Uso:** Itinerary Formatter con diferentes templates
- **Beneficio:** Reutilizar lógica común de formateo

### 3. Strategy Pattern
- **Uso:** Privacy Filter con diferentes estrategias de filtrado
- **Beneficio:** Configuración flexible de privacidad

### 4. OAuth 2.0 Pattern
- **Uso:** OAuth Manager
- **Beneficio:** Autenticación segura con redes sociales

### 5. Cache-Aside Pattern
- **Uso:** Cache de itinerarios renderizados
- **Beneficio:** Reducir tiempo de generación repetida

### 6. Short URL Pattern
- **Uso:** Short Link Generator
- **Beneficio:** URLs amigables para compartir

---

## 📊 Atributos de Calidad

### Usabilidad
- **Un clic** para compartir en múltiples plataformas
- **Preview** antes de compartir
- **Links públicos** fáciles de compartir

### Privacidad
- **Privacy Filter** oculta datos sensibles por defecto
- **Configuración granular** de qué compartir
- **Revocación** de links públicos en cualquier momento

### Interoperabilidad
- **Múltiples plataformas** sociales soportadas
- **OAuth 2.0** estándar
- **Formatos específicos** por plataforma

### Performance
- **Cache** de itinerarios renderizados
- **Generación asíncrona** de imágenes
- **CDN** para servir imágenes generadas

### Escalabilidad
- **Stateless service** permite escalado horizontal
- **Queue** para procesamiento asíncrono de shares
- **Cache distribuido** compartido

---

## 🚀 Extensibilidad

### Agregar Nueva Red Social (ej: LinkedIn)

1. Crear `LinkedInAdapter`
2. Implementar métodos de OAuth
3. Implementar método `publish()`
4. Agregar configuración OAuth en DB
5. Actualizar `SocialMediaPublisher`

**Sin cambios en:** Otros adapters, servicios de dominio

### Agregar Nuevos Formatos de Export

1. Crear nuevo template en Template Engine
2. Actualizar Itinerary Formatter
3. Agregar endpoint en API

---

## ⚙️ Configuración

```yaml
# application.yml
sharing-service:
  social-media:
    facebook:
      app-id: ${FACEBOOK_APP_ID}
      app-secret: ${FACEBOOK_APP_SECRET}
      oauth-redirect-uri: https://api.roadwarrior.com/share/oauth/callback
    twitter:
      api-key: ${TWITTER_API_KEY}
      api-secret: ${TWITTER_API_SECRET}
      oauth-redirect-uri: https://api.roadwarrior.com/share/oauth/callback
    instagram:
      app-id: ${INSTAGRAM_APP_ID}
      app-secret: ${INSTAGRAM_APP_SECRET}
      oauth-redirect-uri: https://api.roadwarrior.com/share/oauth/callback

  public-links:
    base-url: https://share.roadwarrior.com
    default-expiration-days: 30
    max-views-per-hour: 100

  image-generation:
    enabled: true
    format: png
    width: 1200
    height: 630  # Open Graph optimized
    quality: 90

  cache:
    ttl-minutes: 60
    max-size: 10000

  privacy:
    hide-confirmation-numbers: true
    hide-payment-info: true
    hide-passport-numbers: true
```

---

## 📈 Métricas y Monitoreo

### Métricas Clave

- Número de shares por plataforma
- Tasa de éxito de publicaciones
- Número de links públicos activos
- Vistas de links públicos
- Tiempo de generación de imágenes
- Tasa de refresh de tokens OAuth

### Alertas

- Tasa de error en publicación > 10%
- Tokens OAuth expirando sin refresh
- Tiempo de generación de imagen > 5 segundos
- Rate limit alcanzado en plataformas

---

## 🔒 Consideraciones de Seguridad

### OAuth Tokens
- Tokens encriptados en DB (AES-256)
- HTTPS obligatorio en callbacks
- State parameter para prevenir CSRF
- Refresh automático antes de expiración

### Links Públicos
- Tokens de 8 caracteres (62^8 = 218 trillones de combinaciones)
- Rate limiting por IP
- Expiración configurable
- Revocación inmediata

### Datos Compartidos
- Privacy Filter obligatorio
- Auditoría de qué se compartió
- Usuario controla nivel de detalle

---

[⬅️ Volver al índice](./index-c4.md) | [⬆️ Nivel anterior: Contenedores](./c4-L2-contenedores.md)
