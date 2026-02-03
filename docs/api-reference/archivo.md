---
title: 'Archivo API'
description: 'File management endpoints'
api: 'POST /api/v1/archivo/subirArchivo'
---

# Archivo API

Manage file attachments for change control records.

## Upload File

<api method="POST" url="/api/v1/archivo/subirArchivo" description="Upload a file attachment">

### Request

Multipart form data with the following fields:

| Field | Type | Description |
|-------|------|-------------|
| `recoId` | integer | Change control record ID |
| `file` | file | File to upload |

### Request Example

```bash
curl -X POST http://localhost:8080/api/v1/archivo/subirArchivo \
  -H "Authorization: Bearer <token>" \
  -F "recoId=1" \
  -F "file=@/path/to/document.pdf"
```

### Response (200 OK)

```json
{
  "archId": 10,
  "recoId": 1,
  "archNombre": "document.pdf",
  "archRuta": "/uploads/2024/02/03/document_1234567890.pdf",
  "archTipo": "application/pdf",
  "archTamano": 1024000,
  "archFechaSubida": "2024-02-03T15:30:00Z"
}
```

</api>

## List Files for Record

<api method="GET" url="/api/v1/archivo/listarArchivos/{recoId}" description="List all files for a change control record">

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `recoId` | integer | Change control record ID |

### Response (200 OK)

```json
[
  {
    "archId": 10,
    "recoId": 1,
    "archNombre": "document.pdf",
    "archRuta": "/uploads/2024/02/03/document_1234567890.pdf",
    "archTipo": "application/pdf",
    "archTamano": 1024000,
    "archFechaSubida": "2024-02-03T15:30:00Z"
  }
]
```

</api>

## Download File

<api method="GET" url="/api/v1/archivo/descargarArchivo/{archId}" description="Download a file">

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `archId` | integer | File ID |

### Response

Returns the file with appropriate Content-Type header.

### Request Example

```bash
curl -X GET http://localhost:8080/api/v1/archivo/descargarArchivo/10 \
  -H "Authorization: Bearer <token>" \
  --output downloaded_file.pdf
```

</api>

## Delete File

<api method="DELETE" url="/api/v1/archivo/eliminarArchivo/{archId}" description="Delete a file">

### Path Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `archId` | integer | File ID |

### Response (200 OK)

```json
{
  "message": "Archivo eliminado exitosamente"
}
```

</api>

## Supported File Types

The system supports the following file types:

| Extension | MIME Type | Description |
|-----------|-----------|-------------|
| `.pdf` | application/pdf | PDF documents |
| `.doc`, `.docx` | application/msword | Word documents |
| `.xls`, `.xlsx` | application/vnd.ms-excel | Excel spreadsheets |
| `.png` | image/png | PNG images |
| `.jpg`, `.jpeg` | image/jpeg | JPEG images |
| `.txt` | text/plain | Text files |
| `.zip` | application/zip | ZIP archives |

<Warning>
Maximum file size: 10 MB. Files larger than this will be rejected.
</Warning>
