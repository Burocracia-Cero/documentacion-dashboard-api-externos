# 4. Envío de Información por Intervalos

> ⚡ **Procesos de Envío de Información:**
>
> 1. **Carga histórica:** Este proceso se realiza una sola vez para enviar todas las solicitudes históricas desde el 01/01/2020. Puede hacerse en múltiples envíos (por lotes o chunks, Ej. enviar la información por mes) hasta completar toda la carga inicial.
> 2. **Envío diario:** A partir de la carga histórica, cada día se debe enviar únicamente la información de las solicitudes que hayan cambiado de estado en el día actual. Este proceso es recurrente y se ejecuta una vez al día, todos los días.
>
> Ambos procesos son obligatorios para mantener la información sincronizada y actualizada en el tablero de monitoreo.

> 💡 **Recomendación:** Para evitar lentitud o saturación, programe el envío de datos en horarios de baja demanda (por ejemplo, durante la noche o fuera del horario laboral). Así asegura una transferencia eficiente y sin afectar el rendimiento de sus sistemas.


El sistema debe enviar información cada **24 horas** para mantener sincronizados los datos entre sistemas.

## Ejemplo - Configuración de Intervalos


```python
# Configurar envío cada hora usando schedule
import schedule
import time
from datetime import datetime, timedelta
import requests
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def send_hourly_data(max_retries=3):
    current_hour = datetime.now()
    previous_hour = current_hour - timedelta(hours=1)
    
    # Obtener datos del intervalo de la última hora
    interval_data = get_data_in_interval(previous_hour, current_hour)
    
    # Enviar datos con reintentos y backoff exponencial
    retry_count = 0
    while retry_count < max_retries:
        try:
            send_data_to_endpoint(interval_data)
            logger.info("✅ Datos enviados correctamente")
            break
        except Exception as e:
            retry_count += 1
            wait_time = 2 ** retry_count  # 2, 4, 8, ... segundos
            logger.error(f"❌ Intento {retry_count}/{max_retries} fallido: {str(e)}")
            if retry_count < max_retries:
                logger.info(f"Esperando {wait_time} segundos antes del siguiente intento...")
                time.sleep(wait_time)
            else:
                logger.error(f"❌ Se abandonó después de {max_retries} intentos fallidos")

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

---

**[⬅️ Atrás](03-envio-solicitudes.md) | [Siguiente ➡️](05-consideraciones-adicionales.md)**
