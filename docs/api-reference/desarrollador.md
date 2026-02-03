---
title: 'Desarrollador API'
description: 'Developer management endpoints'
api: 'GET /api/v1/desarrollador/encontrarDesarrolladores'
---

# Desarrollador API

Manage developers in the system.

## List All Developers

<api method="GET" url="/api/v1/desarrollador/encontrarDesarrolladores" description="Get all developers">

### Response (200 OK)

```json
[
  {
    "desaId": 1,
    "proyId": 1,
    "proyNombre": "Proyecto Alpha",
    "desaNombre": "Juan",
    "desaApellido": "Pérez",
    "desaEmail": "juan.perez@example.com",
    "desaEspecialidad": "Backend Developer",
    "desaActivo": true
  }
]
```

</api>

## Get Developer by ID

<api method="GET" url="/api/v1/desarrollador/encontrarDesarrollador/{desaId}" description="Get a specific developer">

### Response (200 OK)

```json
{
  "desaId": 1,
  "proyId": 1,
  "desaNombre": "Juan",
  "desaApellido": "Pérez",
  "desaEmail": "juan.perez@example.com",
  "desaEspecialidad": "Backend Developer",
  "desaActivo": true
}
```

</api>

## Create Developer

<api method="POST" url="/api/v1/desarrollador/guardarDesarrollador" description="Create a new developer">

### Request Body

```json
{
  "proyId": 1,
  "desaNombre": "María",
  "desaApellido": "García",
  "desaEmail": "maria.garcia@example.com",
  "desaEspecialidad": "Frontend Developer"
}
```

</api>

## Update Developer

<api method="PUT" url="/api/v1/desarrollador/actualizarDesarrollador" description="Update a developer">

### Request Body

```json
{
  "desaId": 1,
  "proyId": 1,
  "desaNombre": "Juan",
  "desaApellido": "Pérez",
  "desaEmail": "juan.perez@newdomain.com",
  "desaEspecialidad": "Full Stack Developer",
  "desaActivo": true
}
```

</api>

## Delete Developer

<api method="DELETE" url="/api/v1/desarrollador/eliminarDesarrollador/{desaId}" description="Delete a developer">

</api>
