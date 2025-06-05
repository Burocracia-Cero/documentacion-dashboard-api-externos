# 2. Envío de Solicitudes con Cambios de Estado

## 2.1 Endpoints Necesarios

El sistema debe enviar la data necesaria de todas las solicitudes que han cambiado de estado en el día actual utilizando los siguientes endpoints:

### Enviar Solicitudes

```http
POST /services-data/requests
```

**Descripción:** Endpoint para enviar las solicitudes que han cambiado de estado.

**Esquemas de Datos:**

- **RequestDTO**

```json
{
  "service_id": "string",
  "request_id": "string",
  "opening_date": "2025-05-14T12:34:56Z",
  "status_id": 1,
  "last_modified_date": "2025-05-14T12:34:56Z"
}
```

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

```txt
id   name
-------------------------
1   Generado
2   En revisión
3   Aprobado
4   En aprobación
5   En espera de pago
6   Devuelto
7   Completado
8   Rechazado
9   Recibido
10  Cancelado
11  Temporal
12  Atraso por el cliente
13  Disponible Para Entrega
14  Entregado
15  En espera de acción requerida
16  Pagado/No Actualizado
17  Queja/Reclamación Abierta
18  Queja/Reclamación en proceso
19  Queja/Reclamación Cerrada
20  Queja/Reclamación Descartada
21  En proceso
```

## ⚙️ Ejemplos de Uso

### Obtener Servicios

```bash
curl -X GET "/services"
```

**Descripción:** Endpoint para obtener la lista de servicios disponibles en el sistema.

### Procesar Solicitudes

```bash
curl -X POST "/services-data/requests" \
     -H "Content-Type: application/json" \
     -d '{
  "requests": [
    {
      "service_id": "string",
      "request_id": "string",
      "opening_date": "2025-05-14T12:34:56Z",
      "status_id": 1,
      "last_modified_date": "2025-05-14T12:34:56Z"
    }
  ]
}'
```

### Implementación Ejemplo

```python
# Ejemplo de implementación para enviar solicitudes con cambios de estado
import requests
import json
from datetime import datetime
import logging

# Configurar logging
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

def get_requests_changed_today(date_str):
    """
    Función para obtener las solicitudes que cambiaron en una fecha específica
    Esta función debe ser implementada según la estructura de tu base de datos
    
    Args:
        date_str: Fecha en formato YYYY-MM-DD
        
    Returns:
        list: Lista de solicitudes que cambiaron en la fecha indicada
    """
    # Implementación de ejemplo - reemplazar con tu lógica de acceso a datos
    # Por ejemplo, consulta a base de datos para obtener solicitudes con cambios
    # return [
    #     {
    #         "serviceId": "svc-123",
    #         "requestId": "req-456",
    #         "openingDate": "2025-05-14T08:00:00Z",
    #         "statusId": 2,
    #         "lastModifiedDate": "2025-05-14T08:15:00Z"
    #     }
    # ]

def send_daily_status_changes():
    """
    Envía al Dashboard Monitor todas las solicitudes que han cambiado de estado hoy
    """
    try:
        # 1. Obtener la fecha actual
        today = datetime.now().strftime("%Y-%m-%d")
        logger.info(f"Procesando cambios de estado para la fecha: {today}")
        
        # 2. Obtener solicitudes que cambiaron hoy
        changed_requests = get_requests_changed_today(today)
        
        if not changed_requests:
            logger.info("No hay cambios de estado para enviar hoy")
            return
            
        # 3. Formatear solicitudes según el esquema RequestArrayDto
        formatted_requests = []
        for request in changed_requests:
            formatted_requests.append({
                "service_id": request["serviceId"],
                "request_id": request["requestId"],
                "opening_date": request["openingDate"],
                "status_id": request["statusId"],
                "last_modified_date": request["lastModifiedDate"]
            })
        
        # 4. Enviar las solicitudes
        payload = {
            "requests": formatted_requests
        }
        
        response = requests.post(
            '/services-data/requests',
            headers={'Content-Type': 'application/json'},
            json=payload,
            timeout=30  # Timeout de 30 segundos
        )
        
        if response.ok:
            logger.info(f"✅ {len(formatted_requests)} solicitudes enviadas exitosamente")
            return True
        else:
            logger.error(f"❌ Error al enviar solicitudes: {response.status_code} - {response.text}")
            return False
            
    except Exception as e:
        logger.error(f"❌ Error en el proceso de envío: {str(e)}")
        return False

# Ejecutar si se llama directamente
if __name__ == "__main__":
    send_daily_status_changes()
```

---

**[⬅️ Atrás](01-instalacion-xroad.md) | [Siguiente ➡️](03-envio-informacion-intervalos.md)**
