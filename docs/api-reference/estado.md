---
title: 'Estado API'
description: 'Status management endpoints'
api: 'GET /api/v1/estado/encontrarTodosEstados'
---

# Estado API

Manage status definitions for change control records.

## List All Statuses

<api method="GET" url="/api/v1/estado/encontrarTodosEstados" description="Get all available statuses">

### Response (200 OK)

```json
[
  {
    "estaId": 1,
    "estaNombre": "PENDIENTE",
    "estaDescripcion": "Pendiente de revisión",
    "estaColor": "#FFA500",
    "estaActivo": true
  },
  {
    "estaId": 2,
    "estaNombre": "EN_PROGRESO",
    "estaDescripcion": "En progreso",
    "estaColor": "#0000FF",
    "estaActivo": true
  },
  {
    "estaId": 3,
    "estaNombre": "COMPLETADO",
    "estaDescripcion": "Completado",
    "estaColor": "#008000",
    "estaActivo": true
  }
]
```

</api>

## Get Status by ID

<api method="GET" url="/api/v1/estado/encontrarEstado/{estaId}" description="Get a specific status">

</api>

## Create Status

<api method="POST" url="/api/v1/estado/guardarEstado" description="Create a new status">

### Request Body

```json
{
  "estaNombre": "BLOQUEADO",
  "estaDescripcion": "Bloqueado por dependencias",
  "estaColor": "#FF0000",
  "estaActivo": true
}
```

</api>

## Update Status

<api method="PUT" url="/api/v1/estado/actualizarEstado" description="Update a status">

### Request Body

```json
{
  "estaId": 1,
  "estaNombre": "PENDIENTE",
  "estaDescripcion": "Pendiente de aprobación",
  "estaColor": "#FFA500",
  "estaActivo": true
}
```

</api>

## Delete Status

<api method="DELETE" url="/api/v1/estado/eliminarEstado/{estaId}" description="Delete a status">

<Warning>
Deleting a status that is currently in use by change control records may fail. Consider deactivating instead.
</Warning>

</api>

## Default Statuses

| Status | Color | Description |
|--------|-------|-------------|
| PENDIENTE | Orange (#FFA500) | Pending review |
| APROBADO | Green (#008000) | Approved |
| EN_PROGRESO | Blue (#0000FF) | In progress |
| COMPLETADO | Dark Green (#006400) | Completed |
| RECHAZADO | Red (#FF0000) | Rejected |
| CANCELADO | Gray (#808080) | Cancelled |
