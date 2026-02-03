---
title: 'Entity Models'
description: 'Database entity definitions and relationships'
---

# Entity Models

Entity models represent the database tables using JPA annotations.

## Core Entities

### Empresa (Company)

```java
@Entity
@Table(name = "cc_empresa")
public class Empresa {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "empr_id")
    private Integer emprId;
    
    @Column(name = "empr_nombre", length = 100, nullable = false)
    private String emprNombre;
    
    @Column(name = "empr_nit", length = 20)
    private String emprNit;
    
    @Column(name = "empr_contacto", length = 100)
    private String emprContacto;
    
    @Column(name = "empr_email", length = 100)
    private String emprEmail;
    
    @Column(name = "empr_telefono", length = 20)
    private String emprTelefono;
    
    @Column(name = "empr_activo")
    private Boolean emprActivo;
    
    @OneToMany(mappedBy = "empresa")
    private List<Proyecto> proyectos;
}
```

### Proyecto (Project)

```java
@Entity
@Table(name = "cc_proyecto")
public class Proyecto {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "proy_id")
    private Integer proyId;
    
    @ManyToOne
    @JoinColumn(name = "empr_id", nullable = false)
    private Empresa empresa;
    
    @Column(name = "proy_nombre", length = 200, nullable = false)
    private String proyNombre;
    
    @Column(name = "proy_descripcion", columnDefinition = "TEXT")
    private String proyDescripcion;
    
    @Column(name = "proy_fecha_inicio")
    private LocalDate proyFechaInicio;
    
    @Column(name = "proy_fecha_fin")
    private LocalDate proyFechaFin;
    
    @Column(name = "proy_estado", length = 50)
    private String proyEstado;
    
    @Column(name = "proy_activo")
    private Boolean proyActivo;
    
    @OneToMany(mappedBy = "proyecto")
    private List<RegistroControl> registrosControl;
    
    @OneToMany(mappedBy = "proyecto")
    private List<BolsaHoras> bolsasHoras;
    
    @OneToMany(mappedBy = "proyecto")
    private List<Desarrollador> desarrolladores;
}
```

### RegistroControl (Change Control Record)

```java
@Entity
@Table(name = "cc_registro_control")
public class RegistroControl {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "reco_id")
    private Integer recoId;
    
    @ManyToOne
    @JoinColumn(name = "proy_id", nullable = false)
    private Proyecto proyecto;
    
    @ManyToOne
    @JoinColumn(name = "esta_id")
    private Estado estado;
    
    @Column(name = "reco_descripcion", columnDefinition = "TEXT")
    private String recoDescripcion;
    
    @Column(name = "reco_fecha_solicitud")
    private LocalDate recoFechaSolicitud;
    
    @Column(name = "reco_fecha_implementacion")
    private LocalDate recoFechaImplementacion;
    
    @Column(name = "reco_solicitante", length = 100)
    private String recoSolicitante;
    
    @Column(name = "reco_horas_estimadas", precision = 10, scale = 2)
    private BigDecimal recoHorasEstimadas;
    
    @Column(name = "reco_horas_reales", precision = 10, scale = 2)
    private BigDecimal recoHorasReales;
    
    @Column(name = "reco_prioridad", length = 20)
    private String recoPrioridad;
    
    @Column(name = "reco_observaciones", columnDefinition = "TEXT")
    private String recoObservaciones;
    
    @OneToMany(mappedBy = "registroControl")
    private List<Archivo> archivos;
    
    @OneToMany(mappedBy = "registroControl")
    private List<RegistroControlHistorial> historial;
    
    // Audit fields
    @Column(name = "usuario_creacion", length = 50)
    private String usuarioCreacion;
    
    @Column(name = "fecha_creacion")
    private LocalDateTime fechaCreacion;
    
    @Column(name = "usuario_modificacion", length = 50)
    private String usuarioModificacion;
    
    @Column(name = "fecha_modificacion")
    private LocalDateTime fechaModificacion;
}
```

### BolsaHoras (Hour Pool)

```java
@Entity
@Table(name = "cc_bolsa_horas")
public class BolsaHoras {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "boho_id")
    private Integer bohoId;
    
    @ManyToOne
    @JoinColumn(name = "proy_id", nullable = false)
    private Proyecto proyecto;
    
    @Column(name = "boho_horas_totales", precision = 10, scale = 2)
    private BigDecimal bohoHorasTotales;
    
    @Column(name = "boho_horas_consumidas", precision = 10, scale = 2)
    private BigDecimal bohoHorasConsumidas;
    
    @Column(name = "boho_horas_disponibles", precision = 10, scale = 2)
    private BigDecimal bohoHorasDisponibles;
    
    @Column(name = "boho_fecha_inicio")
    private LocalDate bohoFechaInicio;
    
    @Column(name = "boho_fecha_fin")
    private LocalDate bohoFechaFin;
    
    @Column(name = "boho_observaciones", columnDefinition = "TEXT")
    private String bohoObservaciones;
}
```

### Desarrollador (Developer)

```java
@Entity
@Table(name = "cc_desarrollador")
public class Desarrollador {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "desa_id")
    private Integer desaId;
    
    @ManyToOne
    @JoinColumn(name = "proy_id")
    private Proyecto proyecto;
    
    @Column(name = "desa_nombre", length = 50)
    private String desaNombre;
    
    @Column(name = "desa_apellido", length = 50)
    private String desaApellido;
    
    @Column(name = "desa_email", length = 100)
    private String desaEmail;
    
    @Column(name = "desa_especialidad", length = 100)
    private String desaEspecialidad;
    
    @Column(name = "desa_activo")
    private Boolean desaActivo;
}
```

### Archivo (File)

```java
@Entity
@Table(name = "cc_archivo")
public class Archivo {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "arch_id")
    private Integer archId;
    
    @ManyToOne
    @JoinColumn(name = "reco_id", nullable = false)
    private RegistroControl registroControl;
    
    @Column(name = "arch_nombre", length = 200)
    private String archNombre;
    
    @Column(name = "arch_ruta", length = 500)
    private String archRuta;
    
    @Column(name = "arch_tipo", length = 50)
    private String archTipo;
    
    @Column(name = "arch_tamano")
    private Long archTamano;
    
    @Column(name = "arch_fecha_subida")
    private LocalDateTime archFechaSubida;
}
```

### Estado (Status)

```java
@Entity
@Table(name = "cc_estado")
public class Estado {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "esta_id")
    private Integer estaId;
    
    @Column(name = "esta_nombre", length = 50)
    private String estaNombre;
    
    @Column(name = "esta_descripcion", length = 200)
    private String estaDescripcion;
    
    @Column(name = "esta_color", length = 7)
    private String estaColor;
    
    @Column(name = "esta_activo")
    private Boolean estaActivo;
}
```

### RegistroControlHistorial (History)

```java
@Entity
@Table(name = "cc_registro_control_historial")
public class RegistroControlHistorial {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "rehi_id")
    private Integer rehiId;
    
    @ManyToOne
    @JoinColumn(name = "reco_id", nullable = false)
    private RegistroControl registroControl;
    
    @Column(name = "rehi_descripcion", columnDefinition = "TEXT")
    private String rehiDescripcion;
    
    @Column(name = "rehi_fecha")
    private LocalDateTime rehiFecha;
    
    @Column(name = "rehi_usuario", length = 50)
    private String rehiUsuario;
    
    @Column(name = "rehi_accion", length = 50)
    private String rehiAccion;
}
```

### UserApplication (User)

```java
@Entity
@Table(name = "cc_user_application")
public class UserApplication {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "usap_id")
    private Integer usapId;
    
    @Column(name = "usap_username", length = 50, unique = true)
    private String usapUsername;
    
    @Column(name = "usap_password", length = 255)
    private String usapPassword;
    
    @Column(name = "usap_email", length = 100)
    private String usapEmail;
    
    @Column(name = "usap_nombre", length = 100)
    private String usapNombre;
    
    @Column(name = "usap_activo")
    private Boolean usapActivo;
    
    @Column(name = "usap_roles", length = 200)
    private String usapRoles;
}
```

### Parametros (Parameters)

```java
@Entity
@Table(name = "cc_parametros")
public class Parametros {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Column(name = "para_id")
    private Integer paraId;
    
    @Column(name = "para_codigo", length = 50, unique = true)
    private String paraCodigo;
    
    @Column(name = "para_nombre", length = 100)
    private String paraNombre;
    
    @Column(name = "para_valor", columnDefinition = "TEXT")
    private String paraValor;
    
    @Column(name = "para_descripcion", columnDefinition = "TEXT")
    private String paraDescripcion;
    
    @Column(name = "para_activo")
    private Boolean paraActivo;
}
```

## JPA Annotations Used

| Annotation | Purpose |
|------------|---------|
| `@Entity` | Marks class as JPA entity |
| `@Table` | Specifies table name |
| `@Id` | Primary key |
| `@GeneratedValue` | Auto-generated ID |
| `@Column` | Column mapping |
| `@ManyToOne` | Many-to-one relationship |
| `@OneToMany` | One-to-many relationship |
| `@JoinColumn` | Foreign key column |

## Lombok Annotations

All entities use Lombok to reduce boilerplate:

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
```

For more details on relationships, see [Database Schema](/architecture/database-schema).
