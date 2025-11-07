# BCRA - Consultas e Informes

Este módulo permite consultar la Central de Deudores del BCRA y generar informes en PDF.

## Archivos

- `client.py`: Cliente para la API del BCRA
- `generar_pdf.py`: Generador de PDFs con la estética de la app (para uso interno)
- `generar_informe_pdf.py`: Script standalone para generar PDFs desde línea de comandos
- `test_bcra.py`: Script de prueba para consultar la API
- `test_api_raw.py`: Script de prueba raw de la API

## Funcionamiento Automático

### ✨ Consulta automática al crear solicitud

**Cuando se crea una nueva Solicitud de Crédito**, el sistema automáticamente:

1. ✅ **Verifica** si el cliente tiene DNI/CUIT registrado
2. ✅ **Crea** un registro de consulta BCRA asociado a la solicitud
3. ✅ **Ejecuta** la consulta en segundo plano (asíncrono, no bloquea)
4. ✅ **Guarda** los resultados automáticamente

**Ventajas**:
- No bloquea la creación de la solicitud
- Si la API del BCRA está lenta o caída, no afecta al usuario
- Los datos se guardan automáticamente cuando la consulta termine
- El usuario puede ver el resultado después refrescando la página

### ⚠️ Requisitos para la consulta automática

- El cliente debe tener el campo **DNI/CUIT** completado
- El sistema limpia automáticamente guiones y espacios del DNI/CUIT

## Uso desde el Admin

### Consultar BCRA para una solicitud

1. Ve a **Solicitudes de Crédito** en el admin
2. Selecciona las solicitudes que deseas consultar
3. En "Acciones", selecciona **"Execute BCRA Consultation for selected applications"**
4. Click en **"Go"**

### Descargar informe PDF

1. Abre una **Solicitud de Crédito** que tenga una consulta BCRA exitosa
2. En la sección **"Consultas BCRA"** verás un botón **"📄 Descargar Informe PDF"**
3. Click en el botón para descargar el PDF

## Uso desde línea de comandos

### Probar la API

```powershell
# Consultar y mostrar información en terminal
python bcra\test_bcra.py --dni 20377928270

# También acepta formato con guiones
python bcra\test_bcra.py --dni 20-37792827-0
```

### Generar PDF standalone

```powershell
# Generar PDF con nombre automático
python bcra\generar_informe_pdf.py --dni 20377928270

# Generar PDF con nombre personalizado
python bcra\generar_informe_pdf.py --dni 20377928270 --output informe_cliente.pdf
```

## Contenido del Informe PDF

El informe incluye:

1. **Datos del Consultado**
   - CUIT/CUIL
   - Nombre completo
   - Fecha de consulta

2. **Resumen de Situación Actual**
   - Período consultado
   - Total de entidades
   - Entidades con deuda
   - Deuda total

3. **Detalle de Deudas por Entidad**
   - Nombre de la entidad financiera
   - Monto adeudado
   - Situación (Normal, Riesgo Bajo/Medio/Alto, etc.)
   - Días de atraso en pagos
   - Si está refinanciada

4. **Evolución Histórica (24 meses)**
   - Tabla con períodos mensuales
   - Cantidad de entidades con deuda
   - Monto total por período

## Configuración

La URL de la API se configura en el archivo `.env`:

```env
BCRA_CENTRAL_DEUDORES_URL=https://api.bcra.gob.ar/centraldedeudores/v1.0
```

**Nota**: La API del BCRA es pública y no requiere autenticación.

## Códigos de Situación

- **0**: Normal
- **1**: Riesgo Bajo
- **2**: Riesgo Medio
- **3**: Riesgo Alto
- **4**: Irrecuperable
- **5**: Irrecuperable Técnico
- **6**: Irrecuperable por Disposición Técnica

## Troubleshooting

### Error 404 - No records found

Esto significa que el CUIT no tiene registros en el BCRA. Puede ser porque:
- No tiene deudas en el sistema financiero ✓
- El CUIT no está dado de alta en AFIP
- El formato del CUIT es incorrecto

### Error de conexión

Verifica:
1. Conexión a internet
2. URL correcta en `.env`
3. Firewall no bloqueando `api.bcra.gob.ar`
