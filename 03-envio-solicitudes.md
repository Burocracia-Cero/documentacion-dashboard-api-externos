# 3. Envío de Solicitudes con Cambios de Estado

## 3.1 Endpoints Necesarios

El sistema debe enviar la data necesaria de todas las solicitudes que han cambiado de estado en el día actual utilizando los siguientes endpoints:

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

---

**[⬅️ Atrás](02-comunicar-burocracia-cero.md) | [Siguiente ➡️](04-envio-informacion-intervalos.md)**
