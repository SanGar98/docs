---
title: 'Registro Control API'
description: 'Change control record management endpoints'
api: 'POST /api/v1/registroControl/guardarRegistroControl'
---

# Registro Control API

Manage change control records (registros de control de cambios).

## List Change Control Records

<api method="POST" url="/api/v1/registroControl/encontrarRegistroControl/lista" description="Query change control records with filters">

### Headers

```
Authorization: Bearer <token>
Content-Type: application/json
```

### Request Body

```json
{
  "proyId": 1,
  "recoEstado": "PENDIENTE",
  "recoFechaInicio": "2024-01-01",
  "recoFechaFin": "2024-12-31"
}
```

### Response (200 OK)

```json
[
  {
    "recoId": 1,
    "proyId": 1,
    "proyNombre": "Proyecto Alpha",
    "recoDescripcion": "Implementación de nueva funcionalidad",
    "recoFechaSolicitud": "2024-02-01",
    "recoFechaImplementacion": "2024-02-15",
    "recoSolicitante": "Juan Pérez",
    "recoHorasEstimadas": 40.0,
    "recoHorasReales": 35.5,
    "recoPrioridad": "ALTA",
    "recoEstado": "COMPLETADO",
    "recoObservaciones": "Implementado exitosamente"
  }
]
```

</api>

## Get Change Control Record

<api method="GET" url="/api/v1/registroControl/encontrarRegistroControl/{recoId}" description="Get a specific change control record by ID">

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `recoId` | integer | Change control record ID |

### Request Example

```bash
curl -X GET http://localhost:8080/api/v1/registroControl/encontrarRegistroControl/1 \
  -H "Authorization: Bearer <token>"
```

### Response (200 OK)

```json
{
  "recoId": 1,
  "proyId": 1,
  "proyNombre": "Proyecto Alpha",
  "emprId": 1,
  "emprNombre": "Empresa ABC",
  "recoDescripcion": "Implementación de módulo de reportes",
  "recoFechaSolicitud": "2024-02-01",
  "recoFechaImplementacion": "2024-02-15",
  "recoSolicitante": "Juan Pérez",
  "recoHorasEstimadas": 40.0,
  "recoHorasReales": 35.5,
  "recoPrioridad": "ALTA",
  "recoEstado": "COMPLETADO",
  "recoObservaciones": "Implementado con éxito",
  "archivos": [
    {
      "archId": 1,
      "archNombre": "documento.pdf",
      "archRuta": "/uploads/documento.pdf",
      "archTipo": "application/pdf",
      "archTamano": 1024000
    }
  ],
  "usuarioCreacion": "admin",
  "fechaCreacion": "2024-02-01T10:30:00Z",
  "usuarioModificacion": "admin",
  "fechaModificacion": "2024-02-15T16:45:00Z"
}
```

</api>

## Create Change Control Record

<api method="POST" url="/api/v1/registroControl/guardarRegistroControl" description="Create a new change control record">

### Headers

```
Authorization: Bearer <token>
Content-Type: application/json
```

### Request Body

```json
{
  "proyId": 1,
  "recoDescripcion": "Nueva funcionalidad de exportación",
  "recoFechaSolicitud": "2024-02-03",
  "recoSolicitante": "María García",
  "recoHorasEstimadas": 24.0,
  "recoPrioridad": "MEDIA",
  "recoEstado": "PENDIENTE",
  "recoObservaciones": "Solicitud urgente del cliente"
}
```

### Request Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `proyId` | integer | Yes | Project ID |
| `recoDescripcion` | string | Yes | Change description |
| `recoFechaSolicitud` | date | Yes | Request date (YYYY-MM-DD) |
| `recoFechaImplementacion` | date | No | Implementation date |
| `recoSolicitante` | string | Yes | Requester name |
| `recoHorasEstimadas` | decimal | No | Estimated hours |
| `recoHorasReales` | decimal | No | Actual hours |
| `recoPrioridad` | string | No | Priority: BAJA, MEDIA, ALTA |
| `recoEstado` | string | No | Status code |
| `recoObservaciones` | string | No | Observations/notes |

### Response (200 OK)

```json
{
  "recoId": 15,
  "proyId": 1,
  "proyNombre": "Proyecto Alpha",
  "recoDescripcion": "Nueva funcionalidad de exportación",
  "recoFechaSolicitud": "2024-02-03",
  "recoSolicitante": "María García",
  "recoHorasEstimadas": 24.0,
  "recoPrioridad": "MEDIA",
  "recoEstado": "PENDIENTE",
  "usuarioCreacion": "current_user",
  "fechaCreacion": "2024-02-03T15:30:00Z"
}
```

</api>

## Update Change Control Record

<api method="PUT" url="/api/v1/registroControl/actualizarRegistroControl" description="Update an existing change control record">

### Request Body

```json
{
  "recoId": 15,
  "proyId": 1,
  "recoDescripcion": "Nueva funcionalidad de exportación (actualizado)",
  "recoFechaSolicitud": "2024-02-03",
  "recoFechaImplementacion": "2024-02-10",
  "recoSolicitante": "María García",
  "recoHorasEstimadas": 24.0,
  "recoHorasReales": 22.5,
  "recoPrioridad": "ALTA",
  "recoEstado": "EN_PROGRESO",
  "recoObservaciones": "En desarrollo"
}
```

### Response (200 OK)

```json
{
  "recoId": 15,
  "proyId": 1,
  "recoDescripcion": "Nueva funcionalidad de exportación (actualizado)",
  "recoEstado": "EN_PROGRESO",
  "usuarioModificacion": "current_user",
  "fechaModificacion": "2024-02-05T11:20:00Z"
}
```

<Note>
Updating a change control record automatically creates a history entry in `cc_registro_control_historial`.
</Note>

</api>

## Download PDF Report

<api method="GET" url="/api/v1/registroControl/descargarRegistroControl/{recoId}" description="Generate and download a PDF report for a change control record">

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `recoId` | integer | Change control record ID |

### Request Example

```bash
curl -X GET http://localhost:8080/api/v1/registroControl/descargarRegistroControl/1 \
  -H "Authorization: Bearer <token>" \
  --output reporte.pdf
```

### Response

Returns a PDF file with Content-Type: `application/pdf`

**PDF Contents**:
- Change control details
- Project information
- Company information
- Request and implementation dates
- Hour estimates and actuals
- Status and priority
- Observations
- Attached files list
- Change history

</api>

## Status Values

| Status | Description |
|--------|-------------|
| `PENDIENTE` | Pending review |
| `APROBADO` | Approved |
| `EN_PROGRESO` | In progress |
| `COMPLETADO` | Completed |
| `RECHAZADO` | Rejected |
| `CANCELADO` | Cancelled |

## Priority Values

| Priority | Description |
|----------|-------------|
| `BAJA` | Low priority |
| `MEDIA` | Medium priority |
| `ALTA` | High priority |
| `CRITICA` | Critical priority |

## Error Responses

### 404 Not Found

```json
{
  "timestamp": "2024-02-03T20:30:45.123Z",
  "status": 404,
  "error": "Not Found",
  "message": "Registro de control no encontrado con ID: 999",
  "path": "/api/v1/registroControl/encontrarRegistroControl/999"
}
```

### 400 Bad Request

```json
{
  "timestamp": "2024-02-03T20:30:45.123Z",
  "status": 400,
  "error": "Bad Request",
  "message": "Validation failed",
  "errors": [
    {
      "field": "proyId",
      "message": "Project ID is required"
    },
    {
      "field": "recoDescripcion",
      "message": "Description cannot be empty"
    }
  ]
}
```
