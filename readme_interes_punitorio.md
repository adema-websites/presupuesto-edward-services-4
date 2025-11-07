# Implementación del Sistema de Interés Punitorio (Mora) en Gestión de Créditos

## 📋 Descripción General

Este documento detalla la implementación completa del sistema de cálculo de interés por mora (interés punitorio) en el proyecto **gestion_creditos**.

## 🎯 Funcionalidades Implementadas

### 1. **Configuración de Interés por Mora**
- **Campo**: `porcentaje_interes_mora` en el modelo `DatosEmpresa`
- **Tipo**: FloatField con valor por defecto 0.0
- **Descripción**: Porcentaje diario aplicado por mora en cuotas vencidas
- **Ejemplo**: 0.10 para 0.10% diario, 1.0 para 1% diario

### 2. **Controles de Visibilidad de Botones**
- **Campo**: `mostrar_boton_cobrar_con_interes`
- **Campo**: `mostrar_boton_cobrar_sin_interes`
- **Tipo**: BooleanField con valor por defecto True
- **Función**: Controlan la visibilidad de los botones de cobro con/sin interés

### 3. **Campos de Seguimiento en Cuotas**
- `interes_mora_cobrado`: Monto de interés por mora cobrado al momento del pago
- `dias_mora_al_pago`: Cantidad de días de atraso al momento del pago
- `cobrada_con_interes`: Indica si se cobró interés por mora al realizar el pago

### 4. **Métodos de Cálculo**
- `dias_atraso()`: Calcula los días de atraso de la cuota
- `calcular_interes_mora()`: Calcula el interés basado en días de atraso y porcentaje configurado
- `monto_total_con_interes()`: Retorna el monto total incluyendo intereses por mora

### 5. **Vistas de Cobro**
- `cobrar_cuota_con_interes`: Cobra cuota vencida CON interés por mora
- `cobrar_cuota_sin_interes`: Cobra cuota vencida SIN interés (perdona el interés)
- `compartir_comprobante_whatsapp`: Comparte comprobante de pago por WhatsApp

### 6. **Interfaz de Administración Mejorada**
- **CuotaAdmin**: Muestra columnas de interés y total con interés
- **DeudorAdmin**: Vista avanzada con lista de cuotas vencidas y botones de acción
- **Botones de Acción**: Diferentes opciones según estado de la cuota (vencida/no vencida)

### 7. **Reportes PDF Actualizados**
- **reporte_horizontal.py**: Incluye columnas "Interés Mora" y "Total con Interés"
- **reporte_vertical.py**: Incluye columnas "Interés Mora" y "Total con Interés"
- Eliminación de lógica antigua basada en `recargo_diario`

## 📁 Archivos Modificados

### Modelos
- `panel_control/models.py`: Agregados campos de configuración en `DatosEmpresa`
- `creditos/models.py`: Agregados campos y métodos en modelo `Cuota`

### Vistas
- `creditos/views.py`: Agregadas vistas de cobro con/sin interés y WhatsApp

### URLs
- `gestion_creditos/urls.py`: Agregados patrones de URL para nuevas vistas

### Administración
- `creditos/admin.py`: Mejorado `CuotaAdmin` y `DeudorAdmin`

### Templates
- `templates/admin/creditos/deudor_change_form.html`: Nueva plantilla para vista de deudores

### Reportes
- `creditos/reporte_horizontal.py`: Actualizado calendario de pagos
- `creditos/reporte_vertical.py`: Actualizado calendario de pagos

## 🔧 Configuración

### 1. Configurar Porcentaje de Interés
1. Ir a **Panel de Control > Datos de la Empresa**
2. Establecer `Porcentaje de interés diario por mora`
3. Configurar visibilidad de botones según necesidad

### 2. Usar el Sistema
1. **Cuotas Vencidas**: Aparecen botones rojos para cobrar con interés
2. **Perdonar Interés**: Usar botones amarillos para cobrar sin interés
3. **Vista de Deudores**: Acceder desde el admin para ver cuotas vencidas por cliente
4. **WhatsApp**: Compartir comprobantes automáticamente formateados

## 📊 Lógica de Cálculo

### Fórmula de Interés
```
Interés = Monto_Cuota × (Porcentaje_Diario ÷ 100) × Días_Atraso
```

### Ejemplo
- **Monto de cuota**: $1,000
- **Porcentaje diario**: 0.10% (0.10)
- **Días de atraso**: 30
- **Cálculo**: $1,000 × (0.10 ÷ 100) × 30 = $30

## 🎨 Interfaz de Usuario

### Botones de Acción en CuotaAdmin
- **Cuotas al día**: Botones normales (Efectivo/Transferencia)
- **Cuotas vencidas**: Botones adicionales según configuración:
  - 🔴 **Efectivo (Con Interés)** / **Transf. (Con Interés)**
  - 🟡 **Efectivo (Sin Interés)** / **Transf. (Sin Interés)**

### Vista de Deudores
- Lista completa de cuotas vencidas por cliente
- Información detallada: importe, interés calculado, total con interés
- Botones directos de cobro con/sin interés
- Redirección automática después del cobro

### WhatsApp Sharing
- Mensaje personalizado con detalles del pago
- Información del próximo vencimiento
- Enlace directo a WhatsApp del cliente
- Interfaz visual atractiva con auto-apertura

## 📋 Estados de Cuotas

### Estados en Listado
- ✅ **Pagada**: Verde, con opción de descargar comprobante y compartir por WhatsApp
- ⚠️ **Vencida**: Rojo, con opciones de cobro con/sin interés
- 🟠 **Pendiente**: Naranja, botones normales de cobro

### Estados en PDF
- **Pagada**: Muestra fecha de pago
- **En Mora**: Indica estado de mora
- **Pendiente**: Sin fecha de pago

## 🔄 Flujo de Trabajo

### Cobro con Interés
1. Usuario hace clic en "Efectivo (Con Interés)" o "Transf. (Con Interés)"
2. Sistema calcula interés basado en días de atraso
3. Se registra el pago con interés cobrado
4. Se muestra mensaje de confirmación con total cobrado
5. Cuota queda marcada como pagada

### Cobro sin Interés
1. Usuario hace clic en "Efectivo (Sin Interés)" o "Transf. (Sin Interés)"
2. Sistema registra pago sin cobrar interés
3. Se registra que se perdonó el interés
4. Se muestra mensaje de confirmación
5. Cuota queda marcada como pagada

### Compartir por WhatsApp
1. Usuario hace clic en botón WhatsApp (💬)
2. Sistema genera mensaje personalizado
3. Se abre interfaz de confirmación
4. WhatsApp se abre automáticamente con mensaje pre-llenado
5. Usuario puede enviar directamente al cliente

## 🛡️ Validaciones y Seguridad

### Validaciones Implementadas
- Solo se puede cobrar cuotas existentes
- No se puede cobrar cuotas ya pagadas
- Validación de configuración de empresa activa
- Verificación de permisos de usuario

### Manejo de Errores
- Mensajes informativos para acciones inválidas
- Redirección segura después de operaciones
- Logging de operaciones importantes

## 📈 Beneficios del Sistema

### Para la Empresa
- **Control preciso** del interés por mora
- **Flexibilidad** en políticas de cobro
- **Automatización** de cálculos
- **Mejor seguimiento** de deudores

### Para los Clientes
- **Transparencia** en cálculos de interés
- **Opciones de perdón** de interés cuando corresponde
- **Comunicación eficiente** vía WhatsApp
- **Información clara** de próximos vencimientos

### Para los Administradores
- **Interfaz intuitiva** con botones contextuales
- **Vista consolidada** de deudores
- **Reportes actualizados** con información completa
- **Automatización** de comunicaciones

## 🔧 Mantenimiento

### Configuración Inicial
1. Establecer porcentaje de interés diario
2. Configurar visibilidad de botones
3. Verificar permisos de usuarios

### Monitoreo
- Revisar logs de operaciones
- Verificar cálculos de interés
- Monitorear uso de funciones de WhatsApp

### Actualizaciones
- El sistema es extensible para futuras mejoras
- Código modular facilita mantenimiento
- Documentación completa para desarrolladores

## 📞 Soporte

Para soporte técnico o consultas sobre esta implementación, referirse a la documentación de **electro_creditos** o contactar al equipo de desarrollo.

---

**Implementado por**: Kilo Code
**Fecha**: Noviembre 2025
**Versión**: 1.0
**Proyecto**: gestion_creditos