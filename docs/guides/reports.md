---
title: 'Reports Guide'
description: 'JasperReports integration and PDF generation'
---

# Reports Guide

Control de Cambios uses JasperReports for generating PDF reports.

## JasperReports Overview

JasperReports is a Java reporting library that allows creating dynamic PDF, Excel, and other format reports from templates.

**Version**: 6.20.0

## Report Templates

Report templates are stored in `src/main/resources/report/` as `.jrxml` files (Jaspersoft XML format).

### Template Structure

```xml
<?xml version="1.0" encoding="UTF-8"?>
<jasperReport xmlns="http://jasperreports.sourceforge.net/jasperreports"
              name="registro_control_report">
    
    <!-- Parameters -->
    <parameter name="recoId" class="java.lang.Integer"/>
    <parameter name="proyNombre" class="java.lang.String"/>
    
    <!-- Title -->
    <title>
        <band height="50">
            <staticText>
                <reportElement x="0" y="0" width="555" height="30"/>
                <text>Control de Cambios - Reporte</text>
            </staticText>
        </band>
    </title>
    
    <!-- Detail -->
    <detail>
        <band height="200">
            <!-- Content here -->
        </band>
    </detail>
    
</jasperReport>
```

## Generating PDFs

### Service Implementation

```java
@Service
@Slf4j
public class ReportService {
    
    @Value("${reports.template-path:classpath:report/}")
    private String templatePath;
    
    @Value("${reports.output-path:/tmp/reports}")
    private String outputPath;
    
    @Autowired
    private CcRegistroControlRepository registroRepository;
    
    public byte[] generarReporteRegistroControl(Integer recoId) throws SystemException {
        try {
            log.info("Generating PDF report for registro control ID: {}", recoId);
            
            // Load data
            RegistroControl registro = registroRepository.findById(recoId)
                .orElseThrow(() -> new EntityNotFoundException("RegistroControl", recoId));
            
            // Prepare parameters
            Map<String, Object> parameters = new HashMap<>();
            parameters.put("recoId", registro.getRecoId());
            parameters.put("proyNombre", registro.getProyecto().getProyNombre());
            parameters.put("recoDescripcion", registro.getRecoDescripcion());
            parameters.put("recoFechaSolicitud", registro.getRecoFechaSolicitud());
            parameters.put("recoSolicitante", registro.getRecoSolicitante());
            parameters.put("recoHorasEstimadas", registro.getRecoHorasEstimadas());
            parameters.put("recoHorasReales", registro.getRecoHorasReales());
            parameters.put("recoEstado", registro.getEstado().getEstaNombre());
            
            // Load template
            InputStream templateStream = getClass()
                .getResourceAsStream("/report/registro_control_report.jrxml");
            
            JasperReport jasperReport = JasperCompileManager
                .compileReport(templateStream);
            
            // Fill report
            JasperPrint jasperPrint = JasperFillManager
                .fillReport(jasperReport, parameters, new JREmptyDataSource());
            
            // Export to PDF
            byte[] pdfBytes = JasperExportManager.exportReportToPdf(jasperPrint);
            
            log.info("PDF report generated successfully, size: {} bytes", pdfBytes.length);
            return pdfBytes;
            
        } catch (JRException e) {
            log.error("Error generating PDF report", e);
            throw new SystemException("Failed to generate PDF report", e);
        }
    }
}
```

### Controller Endpoint

```java
@GetMapping("/descargarRegistroControl/{recoId}")
public ResponseEntity<?> descargarRegistroControl(@PathVariable Integer recoId) {
    try {
        byte[] pdfBytes = reportService.generarReporteRegistroControl(recoId);
        
        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(MediaType.APPLICATION_PDF);
        headers.setContentDispositionFormData("attachment", 
            "registro_control_" + recoId + ".pdf");
        headers.setContentLength(pdfBytes.length);
        
        return ResponseEntity.ok()
            .headers(headers)
            .body(pdfBytes);
            
    } catch (Exception e) {
        return ResponseEntity.internalServerError()
            .body("Error generating report: " + e.getMessage());
    }
}
```

## Report Features

### Dynamic Data

Reports can display dynamic data from the database:

```xml
<textField>
    <reportElement x="100" y="50" width="200" height="20"/>
    <textFieldExpression>
        <![CDATA[$P{proyNombre}]]>
    </textFieldExpression>
</textField>
```

### Images

Include logos or images:

```xml
<image>
    <reportElement x="0" y="0" width="100" height="50"/>
    <imageExpression>
        <![CDATA["classpath:static/images/logo.png"]]>
    </imageExpression>
</image>
```

### Tables

Display tabular data:

```xml
<detail>
    <band height="20">
        <textField>
            <reportElement x="0" y="0" width="100" height="20"/>
            <textFieldExpression><![CDATA[$F{campo1}]]></textFieldExpression>
        </textField>
        <textField>
            <reportElement x="100" y="0" width="100" height="20"/>
            <textFieldExpression><![CDATA[$F{campo2}]]></textFieldExpression>
        </textField>
    </band>
</detail>
```

### Conditional Formatting

```xml
<textField>
    <reportElement x="0" y="0" width="100" height="20">
        <printWhenExpression>
            <![CDATA[$P{recoEstado}.equals("COMPLETADO")]]>
        </printWhenExpression>
    </reportElement>
    <textFieldExpression>
        <![CDATA["Estado: Completado"]]>
    </textFieldExpression>
</textField>
```

## Report Types

### Simple Report

Basic report with static data and parameters.

### Report with Subreports

Main report that includes subreports:

```xml
<subreport>
    <reportElement x="0" y="100" width="555" height="200"/>
    <subreportParameter name="recoId">
        <subreportParameterExpression>
            <![CDATA[$P{recoId}]]>
        </subreportParameterExpression>
    </subreportParameter>
    <subreportExpression>
        <![CDATA["archivos_subreport.jasper"]]>
    </subreportExpression>
</subreport>
```

### Chart Reports

Include charts and graphs:

```xml
<pieChart>
    <chart>
        <reportElement x="0" y="0" width="400" height="300"/>
    </chart>
    <pieDataset>
        <!-- Dataset configuration -->
    </pieDataset>
    <piePlot>
        <!-- Plot configuration -->
    </piePlot>
</pieChart>
```

## Using Data Sources

### JDBC Data Source

```java
Connection connection = dataSource.getConnection();
JasperPrint jasperPrint = JasperFillManager.fillReport(
    jasperReport, 
    parameters, 
    connection
);
connection.close();
```

### Collection Data Source

```java
List<RegistroControlDTO> data = getReportData();
JRBeanCollectionDataSource dataSource = new JRBeanCollectionDataSource(data);
JasperPrint jasperPrint = JasperFillManager.fillReport(
    jasperReport, 
    parameters, 
    dataSource
);
```

### Empty Data Source

For reports with only parameters (no data rows):

```java
JasperPrint jasperPrint = JasperFillManager.fillReport(
    jasperReport, 
    parameters, 
    new JREmptyDataSource()
);
```

## Compiling Reports

### At Build Time

Add to `pom.xml`:

```xml
<plugin>
    <groupId>com.alexnederlof</groupId>
    <artifactId>jasperreports-plugin</artifactId>
    <version>2.8</version>
    <executions>
        <execution>
            <phase>process-resources</phase>
            <goals>
                <goal>jasper</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <sourceDirectory>src/main/resources/report</sourceDirectory>
        <outputDirectory>${project.build.directory}/classes/report</outputDirectory>
    </configuration>
</plugin>
```

### At Runtime

```java
InputStream jrxmlStream = getClass().getResourceAsStream("/report/template.jrxml");
JasperReport jasperReport = JasperCompileManager.compileReport(jrxmlStream);
```

<Note>
Compiling at build time improves performance as compiled `.jasper` files are used directly.
</Note>

## Export Formats

### PDF (Default)

```java
byte[] pdf = JasperExportManager.exportReportToPdf(jasperPrint);
```

### Excel

```java
JRXlsxExporter exporter = new JRXlsxExporter();
exporter.setExporterInput(new SimpleExporterInput(jasperPrint));
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
exporter.setExporterOutput(new SimpleOutputStreamExporterOutput(outputStream));
exporter.exportReport();
byte[] xlsx = outputStream.toByteArray();
```

### HTML

```java
exporter = new HtmlExporter();
exporter.setExporterInput(new SimpleExporterInput(jasperPrint));
exporter.setExporterOutput(new SimpleHtmlExporterOutput(outputStream));
exporter.exportReport();
```

## Designing Reports

### Jaspersoft Studio

Use Jaspersoft Studio (free drag-and-drop designer) to create report templates:

1. Download from: https://community.jaspersoft.com/project/jaspersoft-studio
2. Create new report
3. Add fields, text, images
4. Preview with sample data
5. Export as `.jrxml`
6. Place in `src/main/resources/report/`

### Best Practices

- **Use parameters** for dynamic values
- **Keep templates simple** for better performance
- **Test with real data** before deployment
- **Version control templates** in Git
- **Cache compiled reports** if using runtime compilation
- **Handle missing data** gracefully
- **Optimize images** (compress, appropriate resolution)

## Troubleshooting

### Template Not Found

```
Error: net.sf.jasperreports.engine.JRException: Could not load resource
```

**Solution**: Verify template path and ensure file is in `src/main/resources/report/`

### Compilation Error

```
Error: net.sf.jasperreports.engine.design.JRCompiler: Errors were encountered
```

**Solution**: Check JRXML syntax, verify all fields and parameters are defined

### Font Issues

```
Error: Font 'Arial' is not available
```

**Solution**: Include font in report or use default fonts (SansSerif, Serif, Monospaced)

### Memory Issues with Large Reports

**Solution**: 
- Use pagination
- Stream output instead of loading entire report in memory
- Increase JVM heap size: `-Xmx2g`

## Example: Complete Report Flow

```java
// 1. Load data
RegistroControl registro = findById(id);

// 2. Prepare parameters and data
Map<String, Object> params = buildParameters(registro);
List<ArchivoDTO> archivos = registro.getArchivos();
JRDataSource dataSource = new JRBeanCollectionDataSource(archivos);

// 3. Compile template (or load compiled)
JasperReport report = JasperCompileManager.compileReport(
    getClass().getResourceAsStream("/report/template.jrxml")
);

// 4. Fill report
JasperPrint print = JasperFillManager.fillReport(report, params, dataSource);

// 5. Export to PDF
byte[] pdf = JasperExportManager.exportReportToPdf(print);

// 6. Return to client
return ResponseEntity.ok()
    .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=report.pdf")
    .contentType(MediaType.APPLICATION_PDF)
    .body(pdf);
```
