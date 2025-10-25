# 🧭 Kata: Road Warrior — Trip Management Dashboard

**Autores:**  
Pilar Mass — Juan Orjuela  

---

## 📖 Definición del problema

Una importante agencia de viajes busca construir el **tablero de gestión de viajes de próxima generación** (“Next-Gen Trip Management Dashboard”), que permita a los viajeros ver y administrar todas sus reservas —vuelos, hoteles y autos— organizadas por viaje, tanto desde la **web** como desde **dispositivos móviles**.

---

## ⚙️ Requisitos funcionales

- **Integración con sistemas existentes:**  
  El sistema debe conectarse con aerolíneas, hoteles y empresas de alquiler de autos.  
  Las reservas se cargarán automáticamente mediante las **cuentas de viajero frecuente, puntos de hotel y recompensas**.

- **Ingreso manual de reservas:**  
  Los usuarios podrán **añadir reservas manualmente** si no son detectadas por las integraciones automáticas.

- **Agrupación de elementos:**  
  Las reservas deben agruparse por **viaje**.  
  Una vez completado el viaje, los elementos deben **eliminarse automáticamente** del tablero.

- **Compartir viajes:**  
  Los usuarios deben poder **compartir su itinerario** con otras personas a través de **redes sociales**.

- **Interfaz rica y multiplataforma:**  
  La UI debe ser **intuitiva, moderna y consistente** en web y móvil.

---

## 🌍 Contexto adicional

- El sistema debe **integrarse sin fricciones** con plataformas de viaje existentes.  
- Se están **negociando alianzas estratégicas** para incluir proveedores preferidos.  
- Debe **funcionar internacionalmente**, soportando distintos idiomas, monedas y zonas horarias.

---

## 🧩 Identificación de atributos de calidad

| **Atributo** | **Tipo** | **Justificación** |
|---------------|-----------|------------------|
| **Interoperabilidad** | Explícito | Clave para integrar vuelos, hoteles y autos. |
| **Disponibilidad** | Explícito | Debe estar accesible 24/7 para usuarios globales. |
| **Seguridad** | Implícito | Protege datos sensibles y genera confianza. |
| **Escalabilidad** | Implícito | Soporta picos de demanda en temporada alta. |
| **Usabilidad** | Implícito | Facilita el uso en web y móvil. |
| **Mantenibilidad** | Implícito | Permite evolución y adición de nuevos proveedores. |
| **Portabilidad** | Implícito | Operación en múltiples plataformas y regiones. |
| **Confiabilidad** | Implícito | Asegura continuidad y recuperación ante fallos. |

---

## 🧱 Priorización de atributos

- **Explícitos:** Derivan directamente de los requerimientos del sistema.  
- **Implícitos:** Nacen del dominio y expectativas tácitas de los usuarios y del negocio.

**Atributos priorizados:**
1. **Interoperabilidad**
2. **Disponibilidad**
3. **Seguridad**
4. **Escalabilidad**

---

## 🔍 Análisis de los atributos clave

### 🔗 Interoperabilidad
- **Motivo:** Garantiza la integración fluida con aerolíneas, hoteles y servicios externos.  
  Permite el intercambio de datos entre sistemas heterogéneos, evita redundancias y asegura una experiencia unificada.  
- **Requisito:** El sistema debe comunicarse con diferentes plataformas sin importar el proveedor o tecnología.

### 🌐 Disponibilidad
- **Motivo:** Los viajeros necesitan acceso continuo a sus reservas.  
  Una alta disponibilidad evita interrupciones críticas y mejora la confianza del usuario.  
- **Requisito:** El sistema debe estar operativo 24/7, independientemente de la hora o ubicación.

### 🔒 Seguridad
- **Motivo:** Imprescindible para proteger datos personales y financieros.  
  Cumple regulaciones internacionales y genera confianza con usuarios y socios.  
- **Requisito:** Debe garantizar la confidencialidad, integridad y autenticación de datos y transacciones.

### 📈 Escalabilidad
- **Motivo:** Fundamental para soportar picos de tráfico en temporadas altas (vacaciones, feriados).  
  También permite expandirse a nivel internacional sin pérdida de rendimiento.  
- **Requisito:** El sistema debe crecer en usuarios, datos y transacciones sin comprometer la performance.

---

## 🧠 Conclusiones

- **Foco arquitectónico:**  
  Interoperabilidad, disponibilidad, seguridad y escalabilidad son los **pilares** del diseño.

- **Equilibrio de trade-offs:**  
  Mejorar seguridad o disponibilidad puede impactar el rendimiento, por lo que el diseño debe mantener un **balance cuidadoso**.

- **Visión evolutiva:**  
  La arquitectura debe ser **flexible y extensible**, permitiendo incorporar nuevos proveedores, servicios y funcionalidades conforme crezca el negocio.

---
