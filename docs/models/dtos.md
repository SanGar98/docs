---
title: 'DTOs (Data Transfer Objects)'
description: 'API request and response DTOs'
---

# Data Transfer Objects (DTOs)

DTOs are used to transfer data between the client and server, decoupling the API from internal domain models.

## Request/Response DTOs

### UsuarioDTO

```java
@Data
public class UsuarioDTO {
    private Integer usapId;
    
    @NotBlank(message = "Username is required")
    private String username;
    
    @NotBlank(message = "Password is required")
    private String password;
    
    @Email(message = "Invalid email format")
    private String email;
    
    private String nombre;
    private Boolean activo;
    private List<String> roles;
}
```

### EmpresaDTO

```java
@Data
public class EmpresaDTO {
    private Integer emprId;
    
    @NotBlank
    private String emprNombre;
    
    private String emprNit;
    private String emprContacto;
    
    @Email
    private String emprEmail;
    
    private String emprTelefono;
    private Boolean emprActivo;
}
```

### ProyectoDTO

```java
@Data
public class ProyectoDTO {
    private Integer proyId;
    
    @NotNull
    private Integer emprId;
    
    private String emprNombre;
    
    @NotBlank
    private String proyNombre;
    
    private String proyDescripcion;
    
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate proyFechaInicio;
    
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate proyFechaFin;
    
    private String proyEstado;
    private Boolean proyActivo;
}
```

### RegistroControlDTO

```java
@Data
public class RegistroControlDTO {
    private Integer recoId;
    
    @NotNull(message = "Project ID is required")
    private Integer proyId;
    
    private String proyNombre;
    private Integer emprId;
    private String emprNombre;
    
    @NotBlank(message = "Description is required")
    private String recoDescripcion;
    
    @NotNull
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate recoFechaSolicitud;
    
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate recoFechaImplementacion;
    
    @NotBlank
    private String recoSolicitante;
    
    @DecimalMin(value = "0.0")
    private BigDecimal recoHorasEstimadas;
    
    @DecimalMin(value = "0.0")
    private BigDecimal recoHorasReales;
    
    private String recoPrioridad;
    private String recoEstado;
    private String recoObservaciones;
    
    private List<ArchivoDTO> archivos;
    
    private String usuarioCreacion;
    
    @JsonFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    private LocalDateTime fechaCreacion;
    
    private String usuarioModificacion;
    
    @JsonFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    private LocalDateTime fechaModificacion;
}
```

### BolsaHorasDTO

```java
@Data
public class BolsaHorasDTO {
    private Integer bohoId;
    
    @NotNull
    private Integer proyId;
    
    private String proyNombre;
    
    @NotNull
    @DecimalMin(value = "0.0")
    private BigDecimal bohoHorasTotales;
    
    @DecimalMin(value = "0.0")
    private BigDecimal bohoHorasConsumidas;
    
    @DecimalMin(value = "0.0")
    private BigDecimal bohoHorasDisponibles;
    
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate bohoFechaInicio;
    
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate bohoFechaFin;
    
    private String bohoObservaciones;
}
```

### BolsaHorasConsultaDTO

```java
@Data
public class BolsaHorasConsultaDTO extends BolsaHorasDTO {
    private BigDecimal porcentajeConsumo;
    private BigDecimal porcentajeDisponible;
    private Integer diasTranscurridos;
    private Integer diasRestantes;
    private BigDecimal tasaConsumoDiario;
}
```

### DesarrolladorDTO

```java
@Data
public class DesarrolladorDTO {
    private Integer desaId;
    
    @NotNull
    private Integer proyId;
    
    private String proyNombre;
    
    @NotBlank
    private String desaNombre;
    
    @NotBlank
    private String desaApellido;
    
    @Email
    @NotBlank
    private String desaEmail;
    
    private String desaEspecialidad;
    private Boolean desaActivo;
}
```

### ArchivoDTO

```java
@Data
public class ArchivoDTO {
    private Integer archId;
    
    @NotNull
    private Integer recoId;
    
    private String archNombre;
    private String archRuta;
    private String archTipo;
    private Long archTamano;
    
    @JsonFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    private LocalDateTime archFechaSubida;
}
```

### EstadoDTO

```java
@Data
public class EstadoDTO {
    private Integer estaId;
    
    @NotBlank
    private String estaNombre;
    
    private String estaDescripcion;
    
    @Pattern(regexp = "^#([A-Fa-f0-9]{6}|[A-Fa-f0-9]{3})$", 
             message = "Invalid color format")
    private String estaColor;
    
    private Boolean estaActivo;
}
```

### RegistroControlHistorialDTO

```java
@Data
public class RegistroControlHistorialDTO {
    private Integer rehiId;
    
    @NotNull
    private Integer recoId;
    
    private String proyNombre;
    private String recoDescripcion;
    private String rehiDescripcion;
    
    @JsonFormat(pattern = "yyyy-MM-dd'T'HH:mm:ss")
    private LocalDateTime rehiFecha;
    
    private String rehiUsuario;
    private String rehiAccion;
}
```

### ParametrosDTO

```java
@Data
public class ParametrosDTO {
    private Integer paraId;
    
    @NotBlank
    private String paraCodigo;
    
    @NotBlank
    private String paraNombre;
    
    private String paraValor;
    private String paraDescripcion;
    private Boolean paraActivo;
}
```

## SSO DTOs

### SsoLoginRequestDTO

```java
@Data
public class SsoLoginRequestDTO {
    @NotBlank
    private String token;
}
```

### ValidateTokenAndOptionsResponseDTO

```java
@Data
public class ValidateTokenAndOptionsResponseDTO {
    private Boolean tokenValid;
    private UsuarioDTO user;
    private List<OptionResponseDTO> options;
    private List<RoleResponseDTO> roles;
}
```

### OptionResponseDTO

```java
@Data
public class OptionResponseDTO {
    private Integer id;
    private String name;
    private String url;
    private String icon;
}
```

### RoleResponseDTO

```java
@Data
public class RoleResponseDTO {
    private String name;
    private String description;
}
```

## Validation Annotations

| Annotation | Purpose |
|------------|---------|
| `@NotNull` | Field cannot be null |
| `@NotBlank` | String cannot be null or empty |
| `@Email` | Must be valid email format |
| `@Pattern` | Matches regex pattern |
| `@DecimalMin` | Minimum decimal value |
| `@JsonFormat` | JSON date/time format |

## Date Formatting

All dates use ISO 8601 format:

- **Date**: `yyyy-MM-dd` (e.g., `2024-02-03`)
- **DateTime**: `yyyy-MM-dd'T'HH:mm:ss` (e.g., `2024-02-03T15:30:00`)

## Mapping DTOs to Entities

DTOs are mapped to entities using MapStruct. See the mapper interfaces in `com.vortexbird.control.cambio.mapper`.

Example:

```java
@Mapper(componentModel = "spring")
public interface ProyectoMapper {
    ProyectoDTO toDTO(Proyecto entity);
    Proyecto toEntity(ProyectoDTO dto);
    List<ProyectoDTO> toDTOList(List<Proyecto> entities);
}
```
