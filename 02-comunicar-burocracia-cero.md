# 2. Solicitar acceso en X-Road

Antes de poder enviar solicitudes al tablero de monitoreo, es necesario que la institución solicite el identificador de servicio (**service_id**) y la autorización de acceso a los subsistemas requeridos a través de X-ROAD.

## Pasos para solicitar acceso


1. **Enviar un correo a:** monitoreo@burocraciacero.gob.do
2. **Indicar en el correo:**
   - Qué subsistema(s) de X-ROAD necesitan acceso al endpoint.
3. **Esperar confirmación:** El equipo de Burocracia Cero le proporcionará el `service_id` y confirmará el acceso a los subsistemas solicitados.

> **Importante:** Sin el `service_id` y la autorización de subsistemas, no podrá reportar solicitudes mediante la API.

---

## Cómo buscar el servicio en X-ROAD:

Para consumir el servicio de integración desde X-ROAD, sigue estos pasos dentro del catálogo de servicios:

1. **Buscar la institución:**  
   Localiza la institución **CNC** en el listado de miembros de X-ROAD.

2. **Seleccionar el subsistema:**  
   Dentro de la institución CNC, busca y selecciona el subsistema **MONITOREO**.

3. **Utilizar el servicio:**  
   Dentro del subsistema MONITOREO, selecciona el servicio **INTEGRACION** para realizar la integración correspondiente.

> **Consulta la guía oficial para consumir servicios en X-ROAD:** [08_consumir_servicio.md](https://github.com/ogticrd/xroad-members/blob/master/08_consumir_servicio.md)
> 
> **Referencia del servicio:**  
> `CNC:MONITOREO:INTEGRACION-DEV`

---

**[⬅️ Atrás](01-instalacion-xroad.md) | [Siguiente ➡️](03-envio-solicitudes.md)**
