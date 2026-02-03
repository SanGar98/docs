# Control de Cambios - Documentación

Documentación completa del proyecto Control de Cambios Backend, compatible con [Mintlify](https://mintlify.com).

## 📚 Contenido

Esta documentación incluye:

- **Introducción**: Descripción general del proyecto y características principales
- **Getting Started**: Guías de instalación, configuración e inicio rápido
- **Architecture**: Arquitectura del sistema, esquema de base de datos y seguridad
- **API Reference**: Documentación completa de todos los endpoints REST
- **Models**: Entidades JPA y DTOs
- **Guides**: Guías de despliegue, pruebas, manejo de errores y generación de reportes

## 🚀 Vista Previa Local

Para ver la documentación localmente con Mintlify:

### 1. Instalar Mintlify CLI

```bash
npm install -g mintlify
```

### 2. Iniciar el servidor de desarrollo

```bash
cd docs/
mintlify dev
```

### 3. Abrir en el navegador

La documentación estará disponible en `http://localhost:3000`

## 📖 Estructura

```
docs/
├── mint.json                     # Configuración de Mintlify
├── introduction.md               # Página de inicio
├── getting-started/              # Guías de inicio
│   ├── installation.md
│   ├── configuration.md
│   └── quick-start.md
├── architecture/                 # Documentación de arquitectura
│   ├── overview.md
│   ├── database-schema.md
│   └── security.md
├── api-reference/                # Referencia de API
│   ├── authentication.md
│   ├── registro-control.md
│   ├── proyecto.md
│   ├── desarrollador.md
│   ├── empresa.md
│   ├── bolsa-horas.md
│   ├── archivo.md
│   ├── estado.md
│   └── historial.md
├── models/                       # Modelos de datos
│   ├── entities.md
│   └── dtos.md
└── guides/                       # Guías avanzadas
    ├── deployment.md
    ├── testing.md
    ├── error-handling.md
    └── reports.md
```

## 🎨 Personalización

### Branding

Edita `mint.json` para personalizar:

- `name`: Nombre del proyecto
- `logo`: Rutas a los logos (claro/oscuro)
- `colors`: Colores primarios y de acento
- `favicon`: Icono del sitio

### Navegación

La estructura de navegación se define en `mint.json` bajo la clave `navigation`.

### Enlaces

Los enlaces externos se configuran en:
- `topbarLinks`: Enlaces en la barra superior
- `topbarCtaButton`: Botón CTA principal
- `anchors`: Enlaces de acceso rápido
- `footerSocials`: Redes sociales en el pie de página

## 📝 Formato de Archivos

Todos los archivos markdown incluyen frontmatter YAML:

```yaml
---
title: 'Título de la Página'
description: 'Descripción breve'
---
```

### Características Soportadas

- ✅ Bloques de código con resaltado de sintaxis
- ✅ Diagramas Mermaid
- ✅ Callouts (Note, Warning, Check, etc.)
- ✅ Card groups
- ✅ Tablas
- ✅ Imágenes
- ✅ Ejemplos de API con cURL

## 🚢 Despliegue

### Opción 1: Mintlify Hosting

1. Push el código a tu repositorio Git
2. Conecta el repositorio en [Mintlify Dashboard](https://dashboard.mintlify.com)
3. Configura la ruta de docs como `/docs`
4. Mintlify se encargará del build y deployment automático

### Opción 2: Auto-hosting

Genera el sitio estático:

```bash
mintlify build
```

Los archivos estáticos estarán en `_site/`

## 📚 Recursos

- [Mintlify Documentation](https://mintlify.com/docs)
- [Mintlify Components](https://mintlify.com/docs/components)
- [Markdown Guide](https://www.markdownguide.org/)

## 🔧 Mantenimiento

### Actualizar Documentación

1. Edita los archivos markdown correspondientes
2. Si es nuevo endpoint, agrégalo a `mint.json` en `navigation`
3. Verifica localmente con `mintlify dev`
4. Commit y push los cambios

### Agregar Nuevas Páginas

1. Crea el archivo `.md` en la carpeta apropiada
2. Incluye frontmatter YAML
3. Agrega la ruta a `navigation` en `mint.json`
4. Verifica que los enlaces funcionen

## 📞 Soporte

Para preguntas o problemas con la documentación, contacta al equipo de desarrollo en support@vortexbird.com
