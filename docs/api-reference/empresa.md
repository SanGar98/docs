---
title: 'Empresa API'
description: 'Company management endpoints'
api: 'GET /api/v1/empresa/encontrarTodasEmpresas'
---

# Empresa API

Manage companies in the system.

## List All Companies

<api method="GET" url="/api/v1/empresa/encontrarTodasEmpresas" description="Get all companies">

### Response (200 OK)

```json
[
  {
    "emprId": 1,
    "emprNombre": "Empresa ABC S.A.S",
    "emprNit": "900123456-1",
    "emprContacto": "Juan Pérez",
    "emprEmail": "contacto@empresaabc.com",
    "emprTelefono": "+57 300 1234567",
    "emprActivo": true
  }
]
```

</api>

## Get Company by ID

<api method="GET" url="/api/v1/empresa/encontrarEmpresa/{emprId}" description="Get a specific company">

### Response (200 OK)

```json
{
  "emprId": 1,
  "emprNombre": "Empresa ABC S.A.S",
  "emprNit": "900123456-1",
  "emprContacto": "Juan Pérez",
  "emprEmail": "contacto@empresaabc.com",
  "emprTelefono": "+57 300 1234567",
  "emprActivo": true,
  "proyectos": []
}
```

</api>

## Create Company

<api method="POST" url="/api/v1/empresa/guardarEmpresa" description="Create a new company">

### Request Body

```json
{
  "emprNombre": "Nueva Empresa S.A.S",
  "emprNit": "900999888-1",
  "emprContacto": "María García",
  "emprEmail": "contacto@nuevaempresa.com",
  "emprTelefono": "+57 310 9876543"
}
```

</api>

## Update Company

<api method="PUT" url="/api/v1/empresa/actualizarEmpresa" description="Update a company">

### Request Body

```json
{
  "emprId": 1,
  "emprNombre": "Empresa ABC S.A.S (Actualizada)",
  "emprNit": "900123456-1",
  "emprContacto": "Juan Pérez",
  "emprEmail": "nuevo@empresaabc.com",
  "emprTelefono": "+57 300 1234567",
  "emprActivo": true
}
```

</api>

## Delete Company

<api method="DELETE" url="/api/v1/empresa/eliminarEmpresa/{emprId}" description="Delete a company">

</api>
