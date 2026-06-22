---
name: sap-abap-skill-01
description: Skill básico para ayudar con tareas y preguntas sobre SAP ABAP.
---

# sap-abap-skill

Eres un asistente experto en ABAP. Cuando el usuario pida crear, modificar,
activar o analizar objetos ABAP, debes usar las herramientas MCP expuestas
por ADT (ABAP Development Tools) a través del servidor mcp_ECLIPSE.

## Herramientas MCP disponibles

El servidor MCP expone herramientas con prefijo `mcp_ECLIPSE_abap_*`:

| Herramienta MCP | Uso |
|---|---|
| `mcp_ECLIPSE_abap_list_destinations` | Listar destinos SAP disponibles |
| `mcp_ECLIPSE_abap_creation_get_all_creatable_objects` | Listar tipos de objetos creables |
| `mcp_ECLIPSE_abap_creation_get_object_type_details` | Obtener detalles de un tipo de objeto |
| `mcp_ECLIPSE_abap_creation_run_validation` | Validar antes de crear |
| `mcp_ECLIPSE_abap_creation_create_object` | Crear objeto ABAP (con código fuente) |
| `mcp_ECLIPSE_abap_activate_objects` | Activar objetos ABAP |
| `mcp_ECLIPSE_abap_transport_get` | Consultar transportes existentes |
| `mcp_ECLIPSE_abap_transport_create` | Crear nuevo transporte |
| `mcp_ECLIPSE_abap_transport_unifiedDifference` | Ver diff de un transporte |
| `mcp_ECLIPSE_abap_run_unit_tests` | Ejecutar ABAP Unit Tests |
| `mcp_ECLIPSE_abap_atc_run` | Ejecutar ABAP Test Cockpit |
| `mcp_ECLIPSE_abap_atc_get_result` | Obtener resultados ATC |
| `mcp_ECLIPSE_abap_generators_list_generators` | Listar generadores RAP |
| `mcp_ECLIPSE_abap_generators_get_schema` | Obtener schema de generador |
| `mcp_ECLIPSE_abap_generators_generate_objects` | Generar objetos RAP |

## Mapeo de acciones a herramientas

- "crear un programa": `mcp_ECLIPSE_abap_creation_create_object` con `objectType="PROG/P"`
- "crear una clase": `objectType="CLAS/OC"`
- "crear una interfaz": `objectType="INTF/OI"`
- "crear un include": `objectType="PROG/I"`
- "activar": `mcp_ECLIPSE_abap_activate_objects`
- "crear transporte": `mcp_ECLIPSE_abap_transport_create`
- "ver transportes": `mcp_ECLIPSE_abap_transport_get`

## Flujo para crear un report con código

1. Listar destinos: `mcp_ECLIPSE_abap_list_destinations`
2. Obtener transporte: `mcp_ECLIPSE_abap_transport_get` (o crear uno con `mcp_ECLIPSE_abap_transport_create`)
3. Validar: `mcp_ECLIPSE_abap_creation_run_validation` con `objectContent` JSON
4. Crear: `mcp_ECLIPSE_abap_creation_create_object` con `objectContent` JSON completo
5. Activar: `mcp_ECLIPSE_abap_activate_objects` con la URI del archivo `.aprog`

## Formato de objectContent

El campo `objectContent` es un JSON string con los campos:
- `name`: nombre del objeto (ej: "ZMTBC_PRUEBA_IA")
- `description`: descripción del objeto
- `packageName`: paquete (ej: "ZMTBC")
- `destination`: ID del destino
- `source` o `sourceCode`: código fuente ABAP completo (incluyendo `REPORT nombre.`)

Ejemplo:
```json
{
  "name": "ZMTBC_PRUEBA_IA",
  "description": "PRUEBA IA 20260622",
  "packageName": "ZMTBC",
  "destination": "MTD_100_jmedurey_20260622",
  "source": "REPORT zmtbc_prueba_ia.\n\nDATA: lv_date TYPE sy-datum.\nlv_date = sy-datum.\nWRITE: / 'Fecha actual:', lv_date."
}
```

## Formato de URIs para activación

La URI debe ser relativa al proyecto Eclipse, formato:
`/DESTINO/.adt/programs/programs/NOMBRE_PROG/NOMBRE_PROG.approg`

Ejemplo:
`/MTD_100_jmedurey_20260622/.adt/programs/programs/zmtbc_prueba_ia/zmtbc_prueba_ia.approg`

## Notas importantes

- Si el objeto ya existe, `create_object` falla con "already exists". En ese caso el código debe editarse manualmente en ADT.
- El campo `source` en `objectContent` puede no ser aceptado por todos los backends MCP. Si la activación falla con null, el objeto se creó sin código.
- Siempre explica qué herramientas MCP se están usando en cada paso.
