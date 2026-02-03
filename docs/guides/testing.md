---
title: 'Testing Guide'
description: 'Testing strategies and practices'
---

# Testing Guide

Control de Cambios includes comprehensive testing strategies using JUnit 5, Mockito, and other testing frameworks.

## Test Structure

```
src/test/java/com/vortexbird/control/cambio/
├── service/          # Service layer tests
├── controller/       # Controller tests
├── repository/       # Repository tests
├── integration/      # Integration tests
└── util/            # Test utilities
```

## Running Tests

### All Tests

```bash
mvn test
```

### Unit Tests Only

```bash
mvn test -Pdev
```

### Integration Tests

```bash
mvn verify -Ptest
```

### Specific Test Class

```bash
mvn test -Dtest=CcRegistroControlServiceTest
```

### With Coverage

```bash
mvn test jacoco:report
```

View coverage report: `target/site/jacoco/index.html`

## Unit Testing

### Service Layer Tests

Example test for `CcRegistroControlService`:

```java
@ExtendWith(MockitoExtension.class)
class CcRegistroControlServiceTest {
    
    @Mock
    private CcRegistroControlRepository repository;
    
    @Mock
    private RegistroControlMapper mapper;
    
    @InjectMocks
    private CcRegistroControlServiceImpl service;
    
    @Test
    void testGuardarRegistroControl() {
        // Arrange
        RegistroControlDTO dto = new RegistroControlDTO();
        dto.setProyId(1);
        dto.setRecoDescripcion("Test");
        
        RegistroControl entity = new RegistroControl();
        entity.setRecoId(1);
        
        when(mapper.toEntity(dto)).thenReturn(entity);
        when(repository.save(entity)).thenReturn(entity);
        when(mapper.toDTO(entity)).thenReturn(dto);
        
        // Act
        RegistroControlDTO result = service.guardar(dto, "testuser");
        
        // Assert
        assertNotNull(result);
        verify(repository).save(entity);
    }
    
    @Test
    void testEncontrarRegistroControl_NotFound() {
        // Arrange
        when(repository.findById(999)).thenReturn(Optional.empty());
        
        // Act & Assert
        assertThrows(VortexbirdException.class, () -> {
            service.encontrarRegistroControl(999);
        });
    }
}
```

### Controller Tests

```java
@WebMvcTest(CcRegistroControlRestController.class)
@AutoConfigureMockMvc(addFilters = false) // Disable security for tests
class CcRegistroControlRestControllerTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @MockBean
    private CcRegistroControlService service;
    
    @Test
    void testEncontrarRegistroControl() throws Exception {
        // Arrange
        RegistroControlDTO dto = new RegistroControlDTO();
        dto.setRecoId(1);
        dto.setRecoDescripcion("Test");
        
        when(service.encontrarRegistroControl(1)).thenReturn(dto);
        
        // Act & Assert
        mockMvc.perform(get("/api/v1/registroControl/encontrarRegistroControl/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.recoId").value(1))
            .andExpect(jsonPath("$.recoDescripcion").value("Test"));
    }
    
    @Test
    void testGuardarRegistroControl() throws Exception {
        // Arrange
        RegistroControlDTO dto = new RegistroControlDTO();
        dto.setProyId(1);
        dto.setRecoDescripcion("New record");
        
        when(service.guardar(any(), anyString())).thenReturn(dto);
        
        // Act & Assert
        mockMvc.perform(post("/api/v1/registroControl/guardarRegistroControl")
                .contentType(MediaType.APPLICATION_JSON)
                .content("""
                    {
                        "proyId": 1,
                        "recoDescripcion": "New record",
                        "recoFechaSolicitud": "2024-02-03",
                        "recoSolicitante": "Test User"
                    }
                    """))
            .andExpect(status().isOk());
    }
}
```

## Integration Testing

### Repository Tests

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = Replace.NONE)
class CcProyectoRepositoryTest {
    
    @Autowired
    private CcProyectoRepository repository;
    
    @Autowired
    private TestEntityManager entityManager;
    
    @Test
    void testFindByEmprId() {
        // Arrange
        Empresa empresa = new Empresa();
        empresa.setEmprNombre("Test Company");
        entityManager.persist(empresa);
        
        Proyecto proyecto = new Proyecto();
        proyecto.setProyNombre("Test Project");
        proyecto.setEmpresa(empresa);
        entityManager.persistAndFlush(proyecto);
        
        // Act
        List<Proyecto> proyectos = repository.findByEmpresa_EmprId(empresa.getEmprId());
        
        // Assert
        assertEquals(1, proyectos.size());
        assertEquals("Test Project", proyectos.get(0).getProyNombre());
    }
}
```

### End-to-End Tests

```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
@TestPropertySource(locations = "classpath:application-test.properties")
class RegistroControlIntegrationTest {
    
    @Autowired
    private TestRestTemplate restTemplate;
    
    @Test
    void testCreateAndRetrieveRegistroControl() {
        // Create
        RegistroControlDTO createDto = new RegistroControlDTO();
        createDto.setProyId(1);
        createDto.setRecoDescripcion("Integration test");
        
        ResponseEntity<RegistroControlDTO> createResponse = 
            restTemplate.postForEntity("/api/v1/registroControl/guardarRegistroControl", 
                createDto, RegistroControlDTO.class);
        
        assertEquals(HttpStatus.OK, createResponse.getStatusCode());
        Integer id = createResponse.getBody().getRecoId();
        
        // Retrieve
        ResponseEntity<RegistroControlDTO> getResponse = 
            restTemplate.getForEntity("/api/v1/registroControl/encontrarRegistroControl/" + id, 
                RegistroControlDTO.class);
        
        assertEquals(HttpStatus.OK, getResponse.getStatusCode());
        assertEquals("Integration test", getResponse.getBody().getRecoDescripcion());
    }
}
```

## Test Configuration

### Test Properties

`src/test/resources/application-test.properties`:

```properties
# H2 In-Memory Database for tests
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true

# Disable security for tests
spring.autoconfigure.exclude=org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration
```

## Mocking

### Common Mocking Patterns

```java
// Mock with return value
when(service.findById(1)).thenReturn(dto);

// Mock with exception
when(service.findById(999)).thenThrow(new EntityNotFoundException());

// Argument matchers
when(service.save(any(DTO.class))).thenReturn(savedDto);
when(service.updateStatus(eq(1), anyString())).thenReturn(true);

// Verify method calls
verify(repository).save(entity);
verify(repository, times(2)).findAll();
verify(repository, never()).delete(any());
```

## Code Coverage

### JaCoCo Configuration

Coverage reports are generated in: `target/site/jacoco/index.html`

### Coverage Goals

- **Minimum**: 70% line coverage
- **Target**: 80%+ line coverage
- **Services**: 90%+ coverage
- **Controllers**: 80%+ coverage

exclude configuration in `pom.xml` excludes DTOs, entities, and mappers.

## Mutation Testing

### PITest

Run mutation tests:

```bash
mvn test pitest:mutationCoverage
```

Report location: `target/pit-reports/index.html`

### Mutation Score

Target mutation score: 75%+

## Best Practices

### Test Naming

```java
@Test
void testMethodName_Scenario_ExpectedBehavior() {
    // Test implementation
}
```

Examples:
- `testGuardar_ValidDTO_ReturnsDTO()`
- `testEncontrar_NonExistentId_ThrowsException()`
- `testActualizar_NullDTO_ThrowsValidationException()`

### AAA Pattern

Always use Arrange-Act-Assert:

```java
@Test
void testExample() {
    // Arrange
    DTO input = createTestDTO();
    when(mock.method()).thenReturn(expected);
    
    // Act
    DTO result = service.method(input);
    
    // Assert
    assertNotNull(result);
    assertEquals(expected, result);
}
```

### Test Data Builders

```java
public class RegistroControlDTOBuilder {
    private Integer recoId = 1;
    private Integer proyId = 1;
    private String recoDescripcion = "Test description";
    
    public RegistroControlDTOBuilder withId(Integer id) {
        this.recoId = id;
        return this;
    }
    
    public RegistroControlDTOBuilder withDescripcion(String desc) {
        this.recoDescripcion = desc;
        return this;
    }
    
    public RegistroControlDTO build() {
        RegistroControlDTO dto = new RegistroControlDTO();
        dto.setRecoId(recoId);
        dto.setProyId(proyId);
        dto.setRecoDescripcion(recoDescripcion);
        return dto;
    }
}
```

Usage:

```java
RegistroControlDTO dto = new RegistroControlDTOBuilder()
    .withId(5)
    .withDescripcion("Custom description")
    .build();
```

## Continuous Integration

### GitHub Actions Example

``yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v2
      
      - name: Set up JDK 17
        uses: actions/setup-java@v2
        with:
          java-version: '17'
          distribution: 'adopt'
      
      - name: Run tests
        run: mvn test
      
      - name: Generate coverage report
        run: mvn jacoco:report
      
      - name: Upload coverage
        uses: codecov/codecov-action@v2
```

## Test Documentation

Document complex test scenarios:

```java
/**
 * Test scenario: When updating a registro control's status from PENDIENTE to COMPLETADO,
 * the system should:
 * 1. Update the status
 * 2. Create a history entry
 * 3. Update modification timestamp
 * 4. Send notification (if configured)
 */
@Test
void testStatusUpdate_PendienteToCompletado_CreatesHistory() {
    // Test implementation
}
```
