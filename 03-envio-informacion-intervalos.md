# 3. Envío de Información por Intervalos

El sistema debe enviar información cada **1 hora (1hr)** para mantener sincronizados los datos entre sistemas.

## Configuración de Intervalos

```python
# Configurar envío cada hora usando schedule
import schedule
import time
from datetime import datetime, timedelta
import requests

def send_hourly_data():
    current_hour = datetime.now()
    previous_hour = current_hour - timedelta(hours=1)
    
    # Obtener datos del intervalo de la última hora
    interval_data = get_data_in_interval(previous_hour, current_hour)
    
    # Enviar datos
    send_data_to_endpoint(interval_data)

# Programar la tarea para ejecutar cada hora
schedule.every(1).hour.do(send_hourly_data)

# Bucle principal para mantener el programa en ejecución
def run_scheduler():
    while True:
        schedule.run_pending()
        time.sleep(60)  # Verificar cada minuto si hay tareas pendientes

# Ejecutar en un hilo separado o como proceso principal
if __name__ == "__main__":
    run_scheduler()
```

## 3.1 Recomendaciones para Envío de Data

### Opción Más Recomendada: Change Data Capture (CDC)

**Change Data Capture (CDC)** es la técnica más recomendada para capturar y enviar cambios de datos en tiempo real o en intervalos específicos. CDC permite identificar, capturar y entregar los cambios realizados en los datos de una base de datos.

### Ventajas del CDC

- ✅ **Eficiencia:** Solo captura los datos que han cambiado
- ✅ **Tiempo real:** Permite procesamiento casi inmediato
- ✅ **Escalabilidad:** Maneja grandes volúmenes de datos
- ✅ **Integridad:** Garantiza que no se pierdan cambios

### Recursos Adicionales sobre CDC

Para una comprensión más profunda de CDC y cómo implementarlo efectivamente:

- **[🔗 Más Detalles sobre CDC](04-implementacion-cdc.md)** - Nuestra guía detallada de implementación para PostgreSQL, MySQL y SQL Server
- **[🔗 Documentación oficial de PostgreSQL sobre Logical Replication](https://www.postgresql.org/docs/current/logical-replication.html)** - Información técnica sobre CDC en PostgreSQL
- **[🔗 CDC en MySQL](https://dev.mysql.com/doc/refman/8.0/en/binary-log.html)** - Guía para trabajar con Binary Log de MySQL
- **[🔗 Microsoft SQL Server CDC](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/about-change-data-capture-sql-server)** - Explicación detallada de CDC en SQL Server

> **Nota:** El CDC funciona monitoreando los logs de transacciones de la base de datos para identificar cambios (INSERT, UPDATE, DELETE) y luego aplicando esos cambios a sistemas de destino de forma asíncrona. Cada motor de base de datos implementa CDC de manera diferente, pero el objetivo es el mismo: capturar cambios de manera eficiente sin afectar el rendimiento de la base de datos principal.

## 3.2 Consideraciones de Implementación

- **Frecuencia de envío:** La frecuencia de 1 hora es recomendada para balancear la actualización de información con el uso de recursos
- **Buffer de datos:** Implementar un sistema de buffer para almacenar datos en caso de fallos de conectividad
- **Confirmaciones (Acknowledgments):** Utilizar confirmaciones para verificar el envío exitoso de datos
- **Reintentos automáticos:** Configurar política de reintentos para manejar fallos temporales de red

## 3.3 Implementación Recomendada

```python
# Implementación con reintentos y confirmación
import requests
import time
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def send_interval_data(data, max_retries=3):
    """
    Envía datos al endpoint con reintentos automáticos y backoff exponencial
    
    Args:
        data: Lista de registros a enviar
        max_retries: Número máximo de reintentos antes de darse por vencido
        
    Returns:
        bool: True si el envío fue exitoso, False en caso contrario
    """
    retry_count = 0
    
    while retry_count < max_retries:
        try:
            response = requests.post(
                '/services-data/requests',
                headers={'Content-Type': 'application/json'},
                json={
                    "requests": [
                        {
                            "service_id": "string",
                            "request_id": "string",
                            "opening_date": "2025-05-14T12:34:56Z",
                            "status_id": 1,
                            "last_modified_date": "2025-05-14T12:34:56Z"
                        }
                    ]
                },
                timeout=30  # Timeout de 30 segundos
            )
            
            if response.ok:
                logger.info(f"✅ Datos enviados correctamente: {len(data)} registros")
                return True
            else:
                raise Exception(f"Error de servidor: {response.status_code}")
        except Exception as e:
            retry_count += 1
            logger.error(f"❌ Intento {retry_count}/{max_retries} fallido: {str(e)}")
            
            if retry_count < max_retries:
                # Esperar con backoff exponencial
                wait_time = 2 ** retry_count  # 2, 4, 8, 16, etc. segundos
                logger.info(f"Esperando {wait_time} segundos antes del siguiente intento...")
                time.sleep(wait_time)
    
    # Si llegamos aquí, todos los reintentos fallaron
    logger.error(f"❌ Se abandonó después de {max_retries} intentos fallidos")
    return False
```

---

**[⬅️ Atrás](02-envio-solicitudes.md) | [Siguiente ➡️](04-implementacion-cdc.md)**
