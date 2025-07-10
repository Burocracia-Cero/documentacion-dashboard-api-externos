# 4. Procesos de Envío de Información

> 💡 **Recomendación:** Para evitar lentitud o saturación, programe el envío de datos en horarios de baja demanda (por ejemplo, durante la noche o fuera del horario laboral). Así asegura una transferencia eficiente y sin afectar el rendimiento de sus sistemas.

## 4.1 Carga histórica

La carga histórica es el primer paso para integrar su información al tablero. Consiste en enviar todas las solicitudes históricas desde el **01/01/2020** (o desde la fecha más antigua que tenga disponible). Este proceso se realiza una sola vez y puede hacerse en varios envíos por lotes (por ejemplo, mes a mes) hasta completar toda la carga inicial.

> 📅 **Importante:** Solo envíe solicitudes con fecha igual o posterior al **01/01/2020**. Si no tiene datos desde esa fecha, envíe las solicitudes más antiguas que tenga en su sistema.

### 4.1.1 Ejemplo - Configuración de Envío de Carga Histórica

```python
import schedule
import time
import logging
from datetime import datetime, timedelta
import requests

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

# Función para enviar un lote de solicitudes históricas con reintentos y backoff exponencial
def send_historical_batch(batch, max_retries=3):
    retry_count = 0
    while retry_count < max_retries:
        try:
            # Aquí se debe implementar la lógica para enviar el lote al endpoint real
            send_data_to_endpoint(batch)  # Reemplazar con la función real de envío
            logger.info("✅ Lote enviado correctamente")
            break
        except Exception as e:
            retry_count += 1
            wait_time = 2 ** retry_count  # Backoff exponencial: 2, 4, 8, ... segundos
            logger.error(f"❌ Intento {retry_count}/{max_retries} fallido: {str(e)}")
            if retry_count < max_retries:
                logger.info(f"Esperando {wait_time} segundos antes del siguiente intento...")
                time.sleep(wait_time)
    else:
        logger.error(f"❌ Se abandonó el lote después de {max_retries} intentos fallidos")

# Función para obtener el primer día del siguiente mes
def next_month(date):
    if date.month == 12:
        return date.replace(year=date.year + 1, month=1, day=1)
    else:
        return date.replace(month=date.month + 1, day=1)

# Función principal para obtener y enviar todos los lotes históricos mes por mes
# Versión simplificada

def send_all_historical_data():
    start_date = datetime(2020, 1, 1)
    end_date = datetime.now()
    current = start_date
    while current < end_date:
        next_period = min(next_month(current), end_date)
        batch = get_historical_batch(current, next_period)  # Debe implementar esta función
        send_historical_batch(batch)
        current = next_period

schedule.every().day.at("02:00").do(send_all_historical_data)

def run_scheduler():
    while True:
        schedule.run_pending()
        time.sleep(60)  # Verificar cada minuto si hay tareas pendientes

if __name__ == "__main__":
    run_scheduler()
```

## 4.2 Envío diario

El envío diario consiste en reportar, una vez al día, únicamente la información de las solicitudes que hayan cambiado de estado en el día actual. Este proceso es recurrente y obligatorio para mantener la información sincronizada y actualizada en el tablero de monitoreo.

El sistema debe enviar información cada **24 horas** para mantener sincronizados los datos entre sistemas.

### 4.2.1 Ejemplo - Configuración de Envío diario

```python
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
