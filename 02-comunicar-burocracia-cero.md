# 2. Solicitar acceso en X-Road

> ⚠️ **Importante:** Antes de consumir el servicio, asegúrese de que:
> - Ha seguido todos los pasos de la documentación oficial de [X-ROAD (OGTIC)](https://github.com/ogticrd/xroad-members).
> - Su subsistema y servicio ya fue creado, activado y verificado por el servidor central ([X-ROAD (OGTIC)](https://github.com/ogticrd/xroad-members)).

## 2.1 Pasos para solicitar acceso


1. **Enviar un correo a:** monitoreo@burocraciacero.gob.do
2. **Indicar en el correo:**
   - Qué subsistema(s) y servico(s) de X-ROAD necesitan acceso al endpoint.
3. **Esperar confirmación:** El equipo de Burocracia Cero le proporcionará el `service_id` y confirmará el acceso a los subsistemas solicitados.

> **⚠️ Importante:** Sin el `service_id` y la autorización de subsistemas, no podrá reportar solicitudes mediante la API.

---


## 2.2 Cómo buscar y consumir el servicio en X-ROAD

Para consumir el servicio de integración en X-ROAD, siga la guía oficial proporcionada por la OGTIC:

> **Consulta la guía oficial para consumir servicios en X-ROAD:** [08_consumir_servicio.md](https://github.com/ogticrd/xroad-members/blob/master/08_consumir_servicio.md)

Utilice los siguientes identificadores de servicio según el entorno:

- **Desarrollo:** `CNC:MONITOREO:INTEGRACION-DEV`
- **Producción:** `CNC:MONITOREO:INTEGRACION`

---

**[⬅️ Atrás](01-instalacion-xroad.md) | [Siguiente ➡️](03-envio-solicitudes.md)**
