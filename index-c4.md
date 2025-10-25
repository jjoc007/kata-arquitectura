# 🏗️ Diagramas C4 - Road Warrior Trip Management Dashboard

**Autores:** Pilar Mass — Juan Orjuela

---

## 📋 Descripción del Sistema

El **Road Warrior Trip Management Dashboard** es un sistema de gestión de viajes de próxima generación diseñado para permitir a los viajeros ver y administrar todas sus reservas (vuelos, hoteles y autos) organizadas por viaje, tanto desde la web como desde dispositivos móviles.

### Características Principales

- **Integración automática** con aerolíneas, hoteles y empresas de alquiler de autos
- **Ingreso manual** de reservas no detectadas automáticamente
- **Agrupación inteligente** de reservas por viaje
- **Eliminación automática** de viajes completados
- **Compartir itinerarios** a través de redes sociales
- **Interfaz multiplataforma** (web y móvil)

### Atributos de Calidad Priorizados

1. **Interoperabilidad** - Integración fluida con múltiples proveedores
2. **Disponibilidad** - Acceso 24/7 a nivel global
3. **Seguridad** - Protección de datos personales y financieros
4. **Escalabilidad** - Soporte para picos de demanda en temporada alta

---

## 🗂️ Estructura de Diagramas C4

### Nivel 1: Contexto del Sistema

- [**C4-L1: Diagrama de Contexto**](./c4-L1-contexto.md)
  Vista general del sistema Road Warrior y su interacción con usuarios y sistemas externos.

---

### Nivel 2: Contenedores

- [**C4-L2-01: Contenedores del Sistema Road Warrior**](./c4-L2-contenedores.md)
  Arquitectura de contenedores principales: aplicaciones cliente, servicios backend, bases de datos e integraciones.

---

### Nivel 3: Componentes

#### Componentes de la Capa de Integración
- [**C4-L3-01: Componentes del Integration Service**](./c4-L3-01-integration-service.md)
  Componentes para integración con aerolíneas, hoteles y autos.

#### Componentes de la Capa de Servicios Backend
- [**C4-L3-02: Componentes del Trip Management Service**](./c4-L3-02-trip-management.md)
  Componentes para gestión de viajes, agrupación y organización de reservas.

- [**C4-L3-03: Componentes del Booking Service**](./c4-L3-03-booking-service.md)
  Componentes para manejo de reservas individuales (vuelos, hoteles, autos).

- [**C4-L3-04: Componentes del User Service**](./c4-L3-04-user-service.md)
  Componentes para gestión de usuarios, autenticación y perfiles de viajero.

- [**C4-L3-05: Componentes del Sharing Service**](./c4-L3-05-sharing-service.md)
  Componentes para compartir itinerarios en redes sociales.

---

## 📖 Convenciones de los Diagramas

- **Nivel 1 (Contexto):** Muestra el sistema como una caja negra y sus interacciones con usuarios y sistemas externos
- **Nivel 2 (Contenedores):** Descompone el sistema en contenedores (aplicaciones, servicios, bases de datos)
- **Nivel 3 (Componentes):** Descompone cada contenedor en componentes arquitectónicos

Cada diagrama incluye:
- Descripción del nivel y alcance
- Diagrama Mermaid interactivo con links
- Descripción de cada elemento
- Relaciones y flujos de datos principales

---

## 🔗 Navegación

Use los links en los diagramas Mermaid para navegar entre niveles. Cada elemento clickeable lo llevará al siguiente nivel de detalle.
