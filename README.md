# Monitoreo API

API para el monitoreo de solicitudes de parte de las instituciones.

---

## 📖 Especificación OpenAPI

Todos los endpoints y esquemas están definidos en el archivo `swagger.json` (OpenAPI 3.0). Puedes visualizar la documentación en dos formas:

- **Con Redoc**  
  1. Instala Redoc CLI si aún no lo tienes:  
     ```bash
     npm install -g redoc-cli
     ```  
  2. Sirve la spec y ábrela en tu navegador:  
     ```bash
     redoc-cli serve swagger.json
     ```  
  3. Visita en tu navegador:  
     ```
     http://localhost:8080
     ```

- **Con Swagger UI**  
  1. Instala un servidor estático (por ejemplo `http-server`):  
     ```bash
     npm install -g http-server
     ```  
  2. Inicia el servidor en la raíz del proyecto:  
     ```bash
     http-server . -c-1
     ```  
  3. Accede a `http://localhost:8080/swagger.json` dentro de [https://petstore.swagger.io/](https://petstore.swagger.io/) (clic en “Explore” y pega la URL).

---

## 🚀 Cómo ejecutar el servidor

> Asegúrate de tener Node.js (>=16) y npm o yarn instalados.

1. **Clona este repositorio**  
   ```bash
   git clone https://github.com/tu-usuario/api-documentation.git
   cd api-documentation
   ```

2. **Instala dependencias**  
   ```bash
   npm install
   # o
   yarn install
   ```

3. **Arranca el modo desarrollo**  
   ```bash
   npm run start:dev
   # o
   yarn start:dev
   ```

4. **Producción**  
   ```bash
   npm run build
   npm run start:prod
   ```

---

## 📌 Endpoints

| Método | Ruta                                                      | Descripción                                                           | Autenticación         |
| ------ | --------------------------------------------------------- | --------------------------------------------------------------------- | --------------------- |
| `GET`  | `/`                                                       | Endpoint de prueba (“Hello world”)                                    | —                     |
| `GET`  | `/services`                                               | Obtener todos los servicios de una institución                        | Header `api-key`      |
| `GET`  | `/institution-access/getInstitutionKey/{id}`              | Obtener el access-key asignado a la institución                       | Bearer JWT (`access-token`) |
| `PATCH`| `/institution-access/{institution_id}`                    | Crear o regenerar el token de una institución                         | Bearer JWT (`access-token`) |
| `POST` | `/services-data/requests`                                 | Enviar y procesar un lote de solicitudes                              | Header `api-key`      |

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
  curl -X GET "http://localhost:3000/services"        -H "api-key: TU_API_KEY"
  ```

- **Procesar solicitudes**  
  ```bash
  curl -X POST "http://localhost:3000/services-data/requests"        -H "api-key: TU_API_KEY"        -H "Content-Type: application/json"        -d '{
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