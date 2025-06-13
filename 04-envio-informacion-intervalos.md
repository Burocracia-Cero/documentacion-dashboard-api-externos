# 4. Envío de Información por Intervalos

> **Recomendación:** Puede programar el envío de datos en el horario que mejor se adapte a las necesidades y operaciones de su institución. Sin embargo, para evitar posibles afectaciones al rendimiento de los sistemas y la red, sugerimos realizar estos envíos durante periodos de baja demanda, como en la noche o fuera del horario laboral. Esto ayuda a garantizar una transferencia de datos más eficiente y reduce el riesgo de saturación o lentitud en los servicios.

El sistema debe enviar información cada **24 horas** para mantener sincronizados los datos entre sistemas.

## Ejemplo - Configuración de Intervalos

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

# Programar la tarea para ejecutar cada 24 horas
schedule.every(24).hour.do(send_hourly_data)

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

### Técnicas Recomendadas para Datos Periódicos

La implementación de un sistema eficiente para el envío periódico de datos requiere considerar varios enfoques:

- **Polling programado**: Consultas periódicas a la base de datos para detectar cambios
- **Almacenamiento de marcadores temporales**: Guardar la última fecha de sincronización
- **Procesamiento por lotes**: Agrupar múltiples cambios para optimizar el rendimiento

### Ventajas del Envío Programado

- ✅ **Predictibilidad**: Intervalos regulares y predecibles
- ✅ **Control de recursos**: Minimiza picos de uso del sistema
- ✅ **Simplicidad**: Fácil de implementar y mantener
- ✅ **Flexibilidad**: Se adapta a diferentes motores de base de datos

### Recursos Adicionales

Para una comprensión más profunda de las técnicas de sincronización de datos:

- **[🔗 Sincronización de Datos](https://www.postgresql.org/docs/current/logical-replication.html)** - Información técnica sobre replicación en PostgreSQL
- **[🔗 Técnicas de Captura en MySQL](https://dev.mysql.com/doc/refman/8.0/en/binary-log.html)** - Guía para trabajar con Binary Log de MySQL
- **[🔗 Replicación en SQL Server](https://docs.microsoft.com/en-us/sql/relational-databases/track-changes/about-change-data-capture-sql-server)** - Técnicas de replicación en SQL Server

> **Nota:** Las técnicas de captura de cambios varían según el motor de base de datos, pero el objetivo es el mismo: detectar y transmitir cambios de manera eficiente sin afectar el rendimiento de la base de datos principal.

## 3.2 Consideraciones de Implementación

- **Frecuencia de envío:** La frecuencia de 1 hora es recomendada para balancear la actualización de información con el uso de recursos
- **Buffer de datos:** Implementar un sistema de buffer para almacenar datos en caso de fallos de conectividad
- **Confirmaciones (Acknowledgments):** Utilizar confirmaciones para verificar el envío exitoso de datos
- **Reintentos automáticos:** Configurar política de reintentos para manejar fallos temporales de red

## 3.3 Ejemplo - Implementación Recomendada

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

**[⬅️ Atrás](03-envio-solicitudes.md) | [Siguiente ➡️](05-consideraciones-adicionales.md)**
