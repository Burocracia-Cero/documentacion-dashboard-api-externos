# 4. Implementación CDC por Base de Datos

En esta sección se detalla cómo implementar Change Data Capture (CDC) en distintos motores de bases de datos para optimizar el envío de información al Dashboard Monitor.

## 4.1 PostgreSQL

**Usando Logical Replication:**

```sql
-- 1. Habilitar logical replication
ALTER SYSTEM SET wal_level = logical;
ALTER SYSTEM SET max_replication_slots = 4;
ALTER SYSTEM SET max_wal_senders = 4;

-- Reiniciar PostgreSQL para aplicar cambios

-- 2. Crear slot de replicación
SELECT pg_create_logical_replication_slot('requests_slot', 'pgoutput');

-- 3. Crear publicación para la tabla de solicitudes
CREATE PUBLICATION requests_changes FOR TABLE requests;
```

**Script Python para capturar cambios:**

```python
import psycopg2
import json
import time
from datetime import datetime, timedelta

def capture_changes_postgresql():
    conn = psycopg2.connect(
        host="localhost",
        database="dashboard_db",
        user="postgres",
        password="password"
    )
    
    cursor = conn.cursor()
    
    # Consultar cambios en la última hora
    one_hour_ago = datetime.now() - timedelta(hours=1)
    
    query = """
    SELECT * FROM requests 
    WHERE updated_at >= %s 
    AND status_changed_at >= %s
    """
    
    cursor.execute(query, (one_hour_ago, one_hour_ago))
    changes = cursor.fetchall()
    
    # Procesar y enviar cambios
    if changes:
        send_changes_to_api(changes)
    
    cursor.close()
    conn.close()

def send_changes_to_api(changes):
    """
    Envía los cambios detectados al endpoint del Dashboard Monitor
    
    Args:
        changes: Lista de cambios detectados
    """
    import requests
    
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
            }
        )
        
        if response.ok:
            print(f"✅ {len(changes)} cambios enviados correctamente")
        else:
            print(f"❌ Error al enviar cambios: {response.status_code}")
            
    except Exception as e:
        print(f"❌ Error: {str(e)}")
```

## 4.2 MySQL

**Usando Binary Log (binlog):**

```sql
-- 1. Habilitar binary logging
SET GLOBAL log_bin = ON;
SET GLOBAL binlog_format = 'ROW';
SET GLOBAL binlog_row_image = 'FULL';

-- 2. Verificar configuración
SHOW VARIABLES LIKE 'log_bin%';
```

**Script Python para MySQL CDC:**

```python
from pymysqlreplication import BinLogStreamReader
from pymysqlreplication.row_event import (
    DeleteRowsEvent,
    UpdateRowsEvent,
    WriteRowsEvent,
)
import requests
from datetime import datetime, timedelta

def capture_changes_mysql():
    mysql_settings = {
        "host": "127.0.0.1",
        "port": 3306,
        "user": "root",
        "passwd": "password"
    }
    
    stream = BinLogStreamReader(
        connection_settings=mysql_settings,
        server_id=100,
        only_events=[DeleteRowsEvent, WriteRowsEvent, UpdateRowsEvent],
        only_tables=["dashboard_db.requests"]
    )
    
    changes_buffer = []
    last_send = datetime.now()
    
    for binlogevent in stream:
        for row in binlogevent.rows:
            change_data = {
                "timestamp": datetime.now().isoformat(),
                "table": binlogevent.table,
                "operation": type(binlogevent).__name__,
                "data": row["values"] if hasattr(row, "values") else row["after_values"]
            }
            changes_buffer.append(change_data)
        
        # Enviar cada hora
        if datetime.now() - last_send >= timedelta(hours=1):
            if changes_buffer:
                send_hourly_changes(changes_buffer)
                changes_buffer = []
                last_send = datetime.now()
    
    stream.close()

def send_hourly_changes(changes_buffer):
    """
    Envía los cambios acumulados cada hora al Dashboard Monitor
    
    Args:
        changes_buffer: Lista de cambios acumulados en la última hora
    """
    import requests
    
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
            }
        )
        
        if response.ok:
            print(f"✅ {len(changes_buffer)} cambios enviados correctamente")
        else:
            print(f"❌ Error al enviar cambios: {response.status_code}")
            
    except Exception as e:
        print(f"❌ Error: {str(e)}")
```

## 4.3 SQL Server

**Usando Change Data Capture:**

```sql
-- 1. Habilitar CDC en la base de datos
USE dashboard_db;
EXEC sys.sp_cdc_enable_db;

-- 2. Habilitar CDC en la tabla requests
EXEC sys.sp_cdc_enable_table
    @source_schema = N'dbo',
    @source_name = N'requests',
    @role_name = NULL;

-- 3. Verificar que CDC está habilitado
SELECT name, is_cdc_enabled 
FROM sys.databases 
WHERE name = 'dashboard_db';
```

**Script Python para SQL Server CDC:**

```python
import pyodbc
import json
import time
from datetime import datetime, timedelta

def capture_changes_sqlserver():
    connection_string = (
        "DRIVER={ODBC Driver 17 for SQL Server};"
        "SERVER=localhost;"
        "DATABASE=dashboard_db;"
        "Trusted_Connection=yes;"
    )
    
    conn = pyodbc.connect(connection_string)
    cursor = conn.cursor()
    
    # Obtener cambios de la última hora
    one_hour_ago = datetime.now() - timedelta(hours=1)
    
    query = """
    SELECT 
        ct.*,
        CASE 
            WHEN ct.__$operation = 1 THEN 'DELETE'
            WHEN ct.__$operation = 2 THEN 'INSERT'
            WHEN ct.__$operation = 3 THEN 'UPDATE_BEFORE'
            WHEN ct.__$operation = 4 THEN 'UPDATE_AFTER'
        END as operation_type
    FROM cdc.dbo_requests_CT ct
    WHERE ct.__$start_lsn >= sys.fn_cdc_get_min_lsn('dbo_requests')
    AND ct.__$start_lsn <= sys.fn_cdc_get_max_lsn()
    """
    
    cursor.execute(query)
    changes = cursor.fetchall()
    
    # Procesar cambios
    processed_changes = []
    for change in changes:
        change_data = {
            "operation": change.operation_type,
            "timestamp": datetime.now().isoformat(),
            "data": dict(zip([column[0] for column in cursor.description], change))
        }
        processed_changes.append(change_data)
    
    if processed_changes:
        send_changes_to_dashboard_api(processed_changes)
    
    cursor.close()
    conn.close()

def send_changes_to_dashboard_api(processed_changes):
    """
    Envía los cambios procesados al Dashboard Monitor
    
    Args:
        processed_changes: Lista de cambios procesados de SQL Server CDC
    """
    import requests
    
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
            }
        )
        
        if response.ok:
            print(f"✅ {len(processed_changes)} cambios enviados correctamente")
        else:
            print(f"❌ Error al enviar cambios: {response.status_code}")
            
    except Exception as e:
        print(f"❌ Error: {str(e)}")
```

## 4.4 Configuración de Monitoreo Automático

**Script de Scheduler (usando cron en Linux):**

```bash
# Agregar al crontab para ejecutar cada hora
# crontab -e

# Ejecutar script CDC cada hora
0 * * * * /usr/bin/python3 /path/to/cdc_script.py >> /var/log/dashboard_cdc.log 2>&1
```

---

**[⬅️ Atrás](03-envio-informacion-intervalos.md) | [Siguiente ➡️](05-consideraciones-adicionales.md)**
