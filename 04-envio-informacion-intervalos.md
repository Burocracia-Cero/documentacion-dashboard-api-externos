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

---

**[⬅️ Atrás](03-envio-solicitudes.md) | [Siguiente ➡️](05-consideraciones-adicionales.md)**
