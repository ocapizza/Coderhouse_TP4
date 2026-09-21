# Coderhouse TP4

## Sincronizacion del Cerebro Agentico con Ecosistemas de Negocio

Este repositorio contiene la evolucion del workflow del TP3. Conserva el sistema multiagente, la memoria persistente en Airtable y la summarization, e incorpora una rama operativa para correo de soporte con Gmail, HubSpot y Slack.

## Entregable

- `checkpoint4_osvaldo_capizzano.json`: workflow importable en n8n.

## Controles evaluados

1. **IF anti auto-reply:** se ejecuta inmediatamente despues de `Gmail Trigger - Correo entrante` y corta asuntos como `Auto-reply`, `Out of office` y `Undeliverable`, junto con remitentes `no-reply@`.
2. **Look up antes de Create:** `HubSpot - Look up contacto` consulta el email y `IF - Contacto existe` decide entre actualizar o crear, evitando duplicados y errores 409.
3. **Create Draft con Human-in-the-loop:** `Gmail - Create Draft HITL` guarda la respuesta generada como borrador. El workflow no envia correos automaticamente.
4. **Limpieza del payload:** los nodos Set conservan solo email, nombre, asunto, texto y resumen; eliminan adjuntos/binarios y limitan longitudes antes de Slack.

## Seguridad y minimo privilegio

Las credenciales no se incluyen en el JSON. Despues de importar, deben conectarse credenciales OAuth2 propias en Gmail, HubSpot y Slack. Los permisos recomendados son:

- Gmail: leer mensajes de la casilla de soporte y crear borradores. No habilitar envio automatico.
- HubSpot: leer, crear y actualizar contactos. No habilitar objetos comerciales que no use el flujo.
- Slack: publicar mensajes solamente en el canal de Operaciones seleccionado.

## Importacion y configuracion

1. En n8n, abrir el menu de workflows y elegir **Import from File**.
2. Seleccionar `checkpoint4_osvaldo_capizzano.json`.
3. Abrir los nodos de Gmail, HubSpot y Slack y elegir las credenciales OAuth2 correspondientes.
4. En `Slack - Avisar a Operaciones`, reemplazar `CONECTAR_CANAL_OPERACIONES` por el canal autorizado.
5. Verificar que los nodos queden en verde antes de activar el workflow.

## Pruebas de regresion

Ejecutar manualmente estos casos antes de activar:

- Correo con asunto `Auto-reply`: debe terminar en `Stop - Correo automatico ignorado`.
- Correo de `no-reply@dominio.com`: debe cortarse sin pasar al agente.
- Cliente existente en HubSpot: debe ejecutar Update y no Create.
- Cliente nuevo: debe crear un unico contacto.
- Correo normal: debe crear un borrador en Gmail, nunca enviarlo.
- Aviso a Slack: debe contener solo email, asunto, resumen breve y la marca de aprobacion humana.

El workflow se entrega desactivado para impedir ejecuciones reales antes de completar credenciales, canal y pruebas.
