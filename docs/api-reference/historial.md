---
title: 'Historial API'
description: 'Change control history endpoints'
api: 'GET /api/v1/CcregistroControlHistorial/encontrarHistorial/{recoId}'
---

# Historial API

Track and query the history of changes made to change control records.

## Get History for Record

<api method="GET" url="/api/v1/CcregistroControlHistorial/encontrarHistorial/{recoId}" description="Get all history entries for a change control record">

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `recoId` | integer | Change control record ID |

### Response (200 OK)

```json
[
  {
    "rehiId": 1,
    "recoId": 1,
    "rehiDescripcion": "Registro creado",
    "rehiFecha": "2024-02-01T10:30:00Z",
    "rehiUsuario": "admin",
    "rehiAccion": "CREATE"
  },
  {
    "rehiId": 2,
    "recoId": 1,
    "rehiDescripcion": "Estado cambiado de PENDIENTE a EN_PROGRESO",
    "rehiFecha": "2024-02-02T14:15:00Z",
    "rehiUsuario": "manager",
    "rehiAccion": "UPDATE"
  },
  {
    "rehiId": 3,
    "recoId": 1,
    "rehiDescripcion": "Horas reales actualizadas: 35.5",
    "rehiFecha": "2024-02-15T16:45:00Z",
    "rehiUsuario": "developer",
    "rehiAccion": "UPDATE"
  }
]
```

</api>

## List All History Entries

<api method="GET" url="/api/v1/CcregistroControlHistorial/encontrarTodosHistoriales" description="Get all history entries across all records">

### Query Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `fechaInicio` | date | Filter by start date (optional) |
| `fechaFin` | date | Filter by end date (optional) |
| `usuario` | string | Filter by user (optional) |

### Response (200 OK)

```json
[
  {
    "rehiId": 1,
    "recoId": 1,
    "proyNombre": "Proyecto Alpha",
    "rehiDescripcion": "Registro creado",
    "rehiFecha": "2024-02-01T10:30:00Z",
    "rehiUsuario": "admin",
    "rehiAccion": "CREATE"
  }
]
```

</api>

## History Actions

| Action | Description |
|--------|-------------|
| `CREATE` | Record was created |
| `UPDATE` | Record was updated |
| `DELETE` | Record was deleted |
| `STATUS_CHANGE` | Status was changed |
| `ASSIGN` | Record was assigned |
| `COMMENT` | Comment was added |
| `ATTACH` | File was attached |

## Audit Trail

The history feature provides a complete audit trail:

- **Who**: User who made the change
- **What**: Description of the change
- **When**: Exact timestamp
- **Why**: Action type

This ensures full traceability and compliance with audit requirements.

## Automatic History Creation

History entries are automatically created when:

1. A change control record is created
2. A change control record is updated
3. The status changes
4. Files are attached or removed
5. Hours are updated
6. Priority is changed

<Note>
History entries are immutable once created to maintain audit integrity.
</Note>
