---
title: 'Bolsa Horas API'
description: 'Hour pool management endpoints'
api: 'POST /api/v1/bolsa-horas/consultaBolsaHoras'
---

# Bolsa Horas API

Manage hour pools for projects.

## Query Hour Pool

<api method="POST" url="/api/v1/bolsa-horas/consultaBolsaHoras" description="Query hour pool information for a project">

### Request Body

```json
{
  "proyId": 1
}
```

### Response (200 OK)

```json
{
  "bohoId": 1,
  "proyId": 1,
  "proyNombre": "Proyecto Alpha",
  "bohoHorasTotales": 500.0,
  "bohoHorasConsumidas": 235.5,
  "bohoHorasDisponibles": 264.5,
  "bohoFechaInicio": "2024-01-01",
  "bohoFechaFin": "2024-12-31",
  "bohoObservaciones": "Pool de horas asignado para todo el año",
  "porcentajeConsumo": 47.1
}
```

</api>

## List Hour Pools

<api method="GET" url="/api/v1/bolsa-horas/encontrarBolsasHoras" description="Get all hour pools">

### Response (200 OK)

```json
[
  {
    "bohoId": 1,
    "proyId": 1,
    "proyNombre": "Proyecto Alpha",
    "bohoHorasTotales": 500.0,
    "bohoHorasConsumidas": 235.5,
    "bohoHorasDisponibles": 264.5,
    "bohoFechaInicio": "2024-01-01",
    "bohoFechaFin": "2024-12-31"
  }
]
```

</api>

## Get Hour Pool by ID

<api method="GET" url="/api/v1/bolsa-horas/encontrarBolsaHoras/{bohoId}" description="Get a specific hour pool">

</api>

## Create Hour Pool

<api method="POST" url="/api/v1/bolsa-horas/guardarBolsaHoras" description="Create a new hour pool">

### Request Body

```json
{
  "proyId": 1,
  "bohoHorasTotales": 1000.0,
  "bohoFechaInicio": "2024-02-01",
  "bohoFechaFin": "2024-12-31",
  "bohoObservaciones": "Nuevo pool de horas"
}
```

<Note>
The system automatically calculates `bohoHorasDisponibles` as `bohoHorasTotales - bohoHorasConsumidas`.
</Note>

</api>

## Update Hour Pool

<api method="PUT" url="/api/v1/bolsa-horas/actualizarBolsaHoras" description="Update an hour pool">

### Request Body

```json
{
  "bohoId": 1,
  "proyId": 1,
  "bohoHorasTotales": 600.0,
  "bohoHorasConsumidas": 250.0,
  "bohoFechaInicio": "2024-01-01",
  "bohoFechaFin": "2024-12-31",
  "bohoObservaciones": "Pool ampliado"
}
```

</api>

## Hour Pool Metrics

Calculate key metrics from hour pool data:

- **Percentage Consumed**: `(bohoHorasConsumidas / bohoHorasTotales) * 100`
- **Percentage Available**: `(bohoHorasDisponibles / bohoHorasTotales) * 100`
- **Daily Burn Rate**: `bohoHorasConsumidas / days_elapsed`
- **Estimated Depletion Date**: Based on current burn rate
