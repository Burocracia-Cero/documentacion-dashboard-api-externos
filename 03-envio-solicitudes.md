# 3. Envío de Solicitudes con Cambios de Estado

## 3.1 Requerimientos de envío de datos

El sistema debe enviar la información correspondiente a **todas las solicitudes que hayan cambiado de estado durante el día actual**.  
Esto incluye:

- Todas las solicitudes cuyo estado haya cambiado en el día en curso.
- El historial completo de cambios de estado (logs) que hayan ocurrido durante el día para esas solicitudes.

## 3.2 Endpoints Necesarios
### Enviar Solicitudes

```http
POST /services-data/requests
```

**Descripción:** Endpoint para enviar las solicitudes que han cambiado de estado.

**Esquemas de Datos:**

- **RequestArrayDto**

```json
{
  "requests": [
    {
      "service_id": "string",
      "request_id": "string",
      "opening_date": "2025-05-14T12:34:56Z",
      "status_id": 1,
      "last_modified_date": "2025-05-14T12:34:56Z"
    }
  ]
}
```

**Descripción de las propiedades:**

- `service_id`: ID del servicio asignado por Burocracia Cero.
- `request_id`: ID único de la solicitud generado por la institución.
- `opening_date`: Fecha y hora en que se inició la solicitud (formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)).
- `status_id`: Estado de la solicitud (ver tabla de estados).
- `last_modified_date`: Fecha y hora del cambio de estado de la solicitud (formato [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601)).

**Respuesta esperada:**

```json
{
    "code": 201,
    "success": true,
    "message": "Todos los registros se han creado exitosamente.."
}
```

### Lista de Estados Disponible

A continuación se presenta la lista completa de estados disponibles en el sistema:

| id  | name                               |
|-----|------------------------------------|
| 1   | Generado                           |
| 2   | En revisión                        |
| 3   | Aprobado                           |
| 4   | En aprobación                      |
| 5   | En espera de pago                  |
| 6   | Devuelto                           |
| 7   | Completado                         |
| 8   | Rechazado                          |
| 9   | Recibido                           |
| 10  | Cancelado                          |
| 11  | Temporal                           |
| 12  | Atraso por el cliente              |
| 13  | Disponible Para Entrega            |
| 14  | Entregado                          |
| 15  | En espera de acción requerida      |
| 16  | Pagado/No Actualizado              |
| 17  | Queja/Reclamación Abierta          |
| 18  | Queja/Reclamación en proceso       |
| 19  | Queja/Reclamación Cerrada          |
| 20  | Queja/Reclamación Descartada       |
| 21  | En proceso                         |


## 3.3 Opciones de envío:
1. **Enviar peticiones separadas por servicio o trámite**, donde cada petición incluya todas las solicitudes afectadas de ese servicio, junto con su historial de cambios del día. Esto puede facilitar el procesamiento y optimizar la transferencia de datos.
2. **Enviar toda la información consolidada en una única petición**, agrupando todas las solicitudes y sus respectivos logs del día.

La institución puede elegir libremente la opción que mejor se adapte a su arquitectura o flujo de integración.


### Ejemplos de envío de información

A continuación se presentan dos ejemplos válidos de cómo estructurar el envío de datos, según las dos opciones permitidas.

---

#### ✅ Opción 1: Peticiones separadas por servicio

Cada petición contiene **solo las solicitudes de un servicio específico** (en este caso `service_id = "123"`):

```json
{
  "requests": [
    {
      "service_id": "123",
      "request_id": "REQ-001",
      "opening_date": "2025-06-13T08:00:00Z",
      "status_id": 2,
      "last_modified_date": "2025-06-13T10:15:00Z"
    },
    {
      "service_id": "123",
      "request_id": "REQ-001",
      "opening_date": "2025-06-13T08:00:00Z",
      "status_id": 3,
      "last_modified_date": "2025-06-13T12:15:00Z"
    },
    {
      "service_id": "123",
      "request_id": "REQ-002",
      "opening_date": "2025-06-12T09:30:00Z",
      "status_id": 3,
      "last_modified_date": "2025-06-13T11:45:00Z"
    }
  ]
}
```
#### ✅ Opción 2: Petición consolidada con múltiples servicios

Una sola petición contiene solicitudes de distintos servicios:


```json
{
  "requests": [
    {
      "service_id": "123",
      "request_id": "REQ-001",
      "opening_date": "2025-06-13T08:00:00Z",
      "status_id": 2,
      "last_modified_date": "2025-06-13T10:15:00Z"
    },
    {
      "service_id": "456",
      "request_id": "REQ-003",
      "opening_date": "2025-06-10T14:20:00Z",
      "status_id": 4,
      "last_modified_date": "2025-06-13T12:05:00Z"
    },
    {
      "service_id": "789",
      "request_id": "REQ-004",
      "opening_date": "2025-06-11T16:45:00Z",
      "status_id": 1,
      "last_modified_date": "2025-06-13T13:30:00Z"
    }
  ]
}
```

---

**[⬅️ Atrás](02-comunicar-burocracia-cero.md) | [Siguiente ➡️](04-envio-informacion-intervalos.md)**
