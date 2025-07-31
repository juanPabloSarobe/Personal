# Manual Técnico: Proyecto “Técnico Virtual en Seguridad de Conducción con IA y Mensajería”

---

## 1. Resumen del Proyecto

Desarrollar un sistema SaaS para empresas (petroleras, transporte, logística) que:
- Analice eventos de conducción relevantes generados por dispositivos GPS/telemáticos.
- Utilice IA (OpenAI/Claude) para interpretar y analizar los eventos.
- Implemente acciones inmediatas (ej: alertas por WhatsApp) y un dashboard para análisis y reportes.
- Permita la interacción directa con el conductor vía mensajería, con IA participando del chat.
- Gestione reincidencias, historial de eventos y respuestas para auditoría y reportes personalizados.

---

## 2. Flujo Técnico General

### 2.1. Captura y Pre-filtrado de Eventos
- Origen: Dispositivos GPS/telemáticos envían datos a un backend propio vía API REST.
- Pre-filtrado: El backend clasifica los eventos según relevancia (reglas simples o ML básico) para reducir costos y carga de IA.

### 2.2. Procesamiento con IA
- Eventos relevantes se envían a un microservicio que interactúa con modelos de IA (Claude/OpenAI).
- Se pueden usar modelos diferentes por tipo de evento (ej: GPT-3.5 para eventos simples, GPT-4o/Claude Sonnet para eventos críticos).

### 2.3. Acción Inmediata
- Si corresponde, el sistema dispara una acción automática:
  - Mensaje por WhatsApp (Twilio, Meta Cloud API, Telegram, SMS).
  - Interacción en tiempo real con el conductor (chat bidireccional, IA responde).

### 2.4. Registro y Exposición
- Todos los eventos, respuestas y acciones se almacenan en la base de datos.
- El dashboard web expone eventos, reincidencias, historial de chat y reportes.

---

## 3. Arquitectura Sugerida

```
[GPS/Telemática] ---> [Backend/API] ---> [Microservicio IA + Chat]
                             |                     |
                             v                     v
                  [Base de Datos] <--------- [API Mensajería]
                             |
                             v
                        [Dashboard]
```
- Microservicio IA + Chat: Independiente para escalar, mantener y evolucionar la lógica conversacional y conexión con APIs de IA y mensajería.

- Base de Datos: Relacional (PostgreSQL/MySQL) o NoSQL (MongoDB), con tablas para eventos, conductores, historial de chat, reincidencias.

---

## 4. Módulos de Software

### A. Backend principal
- Recepción de eventos.
- Pre-filtrado y clasificación.
- Orquestación entre componentes.

### B. Microservicio IA + Chat
- Lógica conversacional para entablar chats con conductores.
- Integración con APIs de IA (Claude/OpenAI).
- Mantenimiento de historial de chat por conductor/evento.
- Validación de respuestas (“el GPS anda mal”, etc.).

### C. Microservicio de Mensajería
- Integración con Twilio, Meta Cloud API, Telegram, SMS.
- Webhooks para recepción de mensajes entrantes y salientes.

### D. Dashboard Web
- Visualización de eventos, reincidencias, historial de chat y reportes.
- Panel de administración de reglas, usuarios, reportes.

### E. Motor de Reportes
- Generación de resúmenes y reportes personalizados (PDF/Excel).
- Estadísticas de reincidencia y respuestas.

---

## 5. Aspectos Clave para Implementación

- Pre-filtrado: Reduce el costo de IA usando reglas simples o ML ligero para decidir cuáles eventos procesar en profundidad.
- Microservicio chat IA: Permite conversaciones reales con conductores, manteniendo el contexto y el historial para auditoría y mejora continua.
- Reincidencia y excusas: Registrar cuántas veces cada conductor comete la misma infracción y analizar patrones de respuesta/miente.
- Dashboard y reportes: Deben ser personalizables y mostrar estadísticas claras y útiles para el cliente.
- Seguridad y privacidad: Manejar datos sensibles con las mejores prácticas (GDPR, consentimientos, etc.).
- Escalabilidad: Arquitectura orientada a microservicios con contenedores (Docker/Kubernetes).

---

## 6. Esquema de Base de Datos (simplificado)

```
Conductor
- id
- nombre
- empresa

EventoRiesgo
- id
- conductor_id
- vehiculo_id
- tipo_infraccion
- fecha_hora
- ubicacion
- reincidente (boolean)
- veces_reincidente (int)

ChatIntervencion
- id
- evento_id
- conductor_id
- mensaje_ia
- respuesta_conductor
- fecha_hora
- tipo_excusa_detectada (optional)
```

---

## 7. Sistemas y APIs sugeridas

- IA: OpenAI GPT-3.5 Turbo (económico), GPT-4o/Claude Sonnet (premium).
- Mensajería: Twilio API, Meta Cloud WhatsApp API, Telegram Bot API.
- Backend: Node.js (Express/Nest), Python (FastAPI/Flask).
- Frontend: React, Angular o Vue para dashboard.
- Base de datos: PostgreSQL, MySQL, MongoDB.

---

## 8. Próximos pasos sugeridos

1. Definir taxonomía de eventos y reglas de pre-filtrado.
2. Prototipar el microservicio de chat IA y la integración con WhatsApp.
3. Diseñar el esquema de base de datos y los endpoints principales.
4. Crear el dashboard mínimo viable (MVP).
5. Testear flujo completo con datos reales de GPS.
6. Medir costos, ajustar prompts y lógica según performance y precio.
7. Validar con clientes piloto y ajustar el modelo de negocio.

---

## 9. Referencias de conversación

- Estrategia de costos y precios.
- Enfoque mixto para reducir uso de IA costosa.
- Validación y registro de reincidencia/excusas.
- Arquitectura orientada a microservicios.
- Interacción real y contextual con conductores vía mensajería + IA.

---

## 10. Notas para desarrollo futuro

- Mantener modularidad: cada componente debe poder evolucionar o cambiarse por separado.
- Documentar bien los endpoints, la lógica de negocio y los prompts usados para IA.
- Implementar auditoría y logging detallado para seguimiento de conversaciones y decisiones.
- Prever integración con otros canales de mensajería a futuro.
