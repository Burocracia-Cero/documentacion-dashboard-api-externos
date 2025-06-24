# Documentación Tablero de Monitoreo - APIs Externos

## ¿Qué es el Tablero de Monitoreo?

El tablero de monitoreo es una herramienta centralizada de monitoreo que permite a las instituciones públicas reportar y visualizar el estado de los servicios ciudadanos. Esta plataforma facilita la transparencia y seguimiento de las solicitudes de servicios, mejorando la gestión y la experiencia del ciudadano.

## Objetivo de esta documentación

Esta guía está diseñada para desarrolladores e implementadores técnicos que necesitan integrar sus sistemas institucionales con el Dashboard Monitor. A través de nuestras APIs externas, podrá enviar información sobre solicitudes de servicios y sus cambios de estado de manera segura utilizando la plataforma X-ROAD.

## Requisitos previos

- Acceso a la plataforma X-ROAD y credenciales correspondientes
- Conocimientos básicos de APIs REST y formato JSON
- Sistema capaz de realizar peticiones HTTP
- Información de servicios y solicitudes estructurada según los modelos especificados

## Contenido de la documentación

Esta documentación está organizada de forma secuencial, guiándole paso a paso en el proceso de integración:

| Documento | Descripción |
| --- | --- |
| [1. Instalación y Configuración de X-ROAD](01-instalacion-xroad.md) | Guía para instalar y configurar X-ROAD como plataforma de intercambio seguro de datos |
| [2. Comunicar a Burocracia Cero para Obtener Acceso](02-comunicar-burocracia-cero.md) | Solicitud de service_id y autorización de subsistemas para poder utilizar la API |
| [3. Envío de Solicitudes con Cambios de Estado](03-envio-solicitudes.md) | Detalle de endpoints, esquemas de datos y ejemplos prácticos para reportar solicitudes |
| [4. Envío de Información por Intervalos](04-envio-informacion-intervalos.md) | Estrategias para implementar envíos periódicos de información cada hora |
| [5. Consideraciones Adicionales](05-consideraciones-adicionales.md) | Mejores prácticas para seguridad, manejo de errores, monitoreo y escalabilidad |

> **Navegación:** Cada documento incluye enlaces en su parte inferior para facilitar el movimiento entre las diferentes secciones de la documentación.

## Flujo de integración simplificado

1. **Configurar X-ROAD** - Establecer la conexión segura entre sistemas
2. **Comunicar a Burocracia Cero** - Solicitar acceso y autorizaciones necesarias
3. **Implementar API de envío** - Desarrollar la lógica para reportar solicitudes
4. **Configurar envíos periódicos** - Establecer mecanismos para reportes horarios
5. **Aplicar buenas prácticas** - Optimizar la integración siguiendo recomendaciones

## Soporte y contacto

Si encuentra problemas durante la implementación o tiene preguntas adicionales, contacte al equipo de soporte técnico:

- **Email:** monitoreo@burocraciacero.gob.do

---

**Versión:** 1.0.3

**Autores:** Equipo de Burocracia Cero

**Última actualización:** 24/06/2025
