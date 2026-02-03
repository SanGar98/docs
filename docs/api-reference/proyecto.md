---
title: 'Proyecto API'
description: 'Project management endpoints'
api: 'GET /api/v1/proyecto/encontrarTodosProyectos'
---

# Proyecto API

Manage projects in the system.

## List All Projects

<api method="GET" url="/api/v1/proyecto/encontrarTodosProyectos" description="Get all projects">

### Response (200 OK)

```json
[
  {
    "proyId": 1,
    "emprId": 1,
    "emprNombre": "Empresa ABC",
    "proyNombre": "Proyecto Alpha",
    "proyDescripcion": "Sistema de gestión empresarial",
    "proyFechaInicio": "2024-01-01",
    "proyFechaFin": "2024-12-31",
    "proyEstado": "ACTIVO",
    "proyActivo": true
  }
]
```

</api>

## Get Project by ID

<api method="GET" url="/api/v1/proyecto/encontrarProyecto/{proyId}" description="Get a specific project">

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `proyId` | integer | Project ID |

### Response (200 OK)

```json
{
  "proyId": 1,
  "emprId": 1,
  "emprNombre": "Empresa ABC",
  "proyNombre": "Proyecto Alpha",
  "proyDescripcion": "Sistema de gestión empresarial",
  "proyFechaInicio": "2024-01-01",
  "proyFechaFin": "2024-12-31",
  "proyEstado": "ACTIVO",
  "proyActivo": true,
  "desarrolladores": [],
  "bolsasHoras": []
}
```

</api>

## Create Project

<api method="POST" url="/api/v1/proyecto/guardarProyecto" description="Create a new project">

### Request Body

```json
{
  "emprId": 1,
  "proyNombre": "Nuevo Proyecto",
  "proyDescripcion": "Descripción del proyecto",
  "proyFechaInicio": "2024-02-01",
  "proyFechaFin": "2024-12-31",
  "proyEstado": "PLANIFICACION"
}
```

### Response (200 OK)

```json
{
  "proyId": 5,
  "emprId": 1,
  "proyNombre": "Nuevo Proyecto",
  "proyEstado": "PLANIFICACION",
  "proyActivo": true
}
```

</api>

## Update Project

<api method="PUT" url="/api/v1/proyecto/actualizarProyecto" description="Update an existing project">

### Request Body

```json
{
  "proyId": 5,
  "emprId": 1,
  "proyNombre": "Proyecto Actualizado",
  "proyDescripcion": "Descripción actualizada",
  "proyEstado": "ACTIVO"
}
```

</api>

## Delete Project

<api method="DELETE" url="/api/v1/proyecto/eliminarProyecto/{proyId}" description="Delete a project">

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `proyId` | integer | Project ID |

### Response (200 OK)

```json
{
  "message": "Proyecto eliminado exitosamente"
}
```

</api>
