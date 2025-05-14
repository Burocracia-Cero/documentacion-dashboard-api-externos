# Monitoreo API

API para el monitoreo de solicitudes de parte de las instituciones.

---

## 🛠️ Requisitos y acceso

Para garantizar un entorno de monitoreo seguro y eficiente, siga los pasos descritos a continuación:

1. **Instalación y configuración de X-Road**  
   Asegúrese de contar con X-Road debidamente instalado y configurado según las directrices oficiales. Para más detalles, consulte la [guía de instalación de X-Road](https://github.com/ogticrd/xroad-members/blob/master/README.md).

2. **Visualización de la documentación Swagger**  
   Ingrese a la URL `/swagger-doc` en su entorno habilitado. Allí encontrará la interfaz de Swagger UI para explorar los endpoints, revisar esquemas y generar sus propios scripts de conexión.  

---

## 📌 Endpoints

| Método | Ruta                                                      | Descripción                                                           |
| ------ | --------------------------------------------------------- | --------------------------------------------------------------------- |
| `GET`  | `/`                                                       | Endpoint para comprobar actividad en el servidor (“Hello world”)      |
| `GET`  | `/services`                                               | Obtener todos los servicios de una institución                        |
| `POST` | `/services-data/requests`                                 | Enviar y procesar un lote de solicitudes                              |

---

## 💾 Esquemas principales

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
    "requests": [ /* array de RequestDTO */ ]
  }
  ```

---

## ⚙️ Ejemplos de uso

- **Obtener servicios**  
  ```bash
  curl -X GET "/services"        
  ```

- **Procesar solicitudes**  
  ```bash
  curl -X POST "/services-data/requests"                
  -H "Content-Type: application/json"        
  -d '{
         "requests": [
           {
             "service_id": "svc-123",
             "request_id": "req-456",
             "opening_date": "2025-05-14T08:00:00Z",
             "status_id": 2,
             "last_modified_date": "2025-05-14T08:15:00Z"
           }
         ]
       }'
  ```