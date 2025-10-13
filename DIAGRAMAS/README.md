# 🔄 Diagramas de Flujo - Sistema SIGA

> **Documentación visual completa del flujo de trabajo**

## 📋 **Índice de Diagramas**

### 🎯 **Diagrama General**
- **[DIAGRAMA_FLUJO_GENERAL.md](./DIAGRAMA_FLUJO_GENERAL.md)** - Flujo completo desde ingreso hasta despacho

### 📦 **Diagramas por Módulo**
- **[DIAGRAMA_FLUJO_LOGISTICA.md](./DIAGRAMA_FLUJO_LOGISTICA.md)** - Gestión de ingresos, salidas y almacenamiento
- **[DIAGRAMA_FLUJO_OPERACIONES.md](./DIAGRAMA_FLUJO_OPERACIONES.md)** - Procesamiento y manufactura de productos  
- **[DIAGRAMA_FLUJO_FACTURACION.md](./DIAGRAMA_FLUJO_FACTURACION.md)** - Gestión de facturación y control financiero

## 🎨 **Características de los Diagramas**

### ✅ **Formato Mermaid**
- Compatible con GitHub y VS Code
- Renderizado automático como gráficos
- Código versionable y mantenible

### ✅ **Nivel Detallado**
- Incluye todas las validaciones
- Manejo de errores y excepciones
- Estados intermedios y decisiones
- Sistema completo de notificaciones

### ✅ **Cobertura Completa**
- **Trazabilidad**: Seguimiento de productos paso a paso
- **Interacciones**: Comunicación entre operadores
- **Estados**: Transiciones de productos
- **Notificaciones**: Flujo de información entre módulos

## 📊 **Resumen Ejecutivo**

### 🎯 **Flujo Principal**
```
Ingreso → Clasificación → Ubicación → Solicitud → Aprobación → 
Procesamiento → Validación → Devolución → Despacho → Facturación
```

### 👥 **Actores Principales**
- **Operador Logístico**: Ingresos, salidas, aprobaciones
- **Operador Operativo**: Solicitudes, procesamiento, manufactura
- **Operador Facturación**: Aprobación financiera, facturación

### 📍 **Estados Clave del Producto**
| Estado | Módulo | Descripción |
|--------|--------|-------------|
| `Ingresado` | Logística | Producto registrado en sistema |
| `Ubicado` | Logística | Asignado a ubicación física |
| `En Proceso` | Operaciones | Ejecutando manufactura |
| `Terminado` | Operaciones | Procesamiento completado |
| `Listo Despacho` | Logística | Preparado para salida |
| `Facturado` | Facturación | Proceso financiero completado |
| `Despachado` | Logística | Producto fuera del almacén |

## 🔔 **Sistema de Notificaciones**

### **Flujos de Comunicación**
1. **Logística → Operaciones**: Productos listos para procesamiento
2. **Operaciones → Logística**: Solicitud de productos y devolución
3. **Logística → Facturación**: Productos listos para facturar
4. **Facturación → Logística**: Aprobación/rechazo de facturación

### **Tipos de Notificación**
- `productos_listos` - Productos disponibles para procesamiento
- `orden_pendiente` - Orden esperando aprobación
- `productos_asignados` - Productos asignados a orden
- `devolucion_terminados` - Productos listos para devolución
- `facturacion_pendiente` - Solicitud de facturación
- `facturacion_aprobada` - Facturación aprobada

## 📊 **Métricas de Seguimiento**

### **Por Módulo**
| Módulo | Métricas Clave |
|--------|----------------|
| **Logística** | Tiempo de ingreso, Ocupación almacén, Órdenes procesadas |
| **Operaciones** | Tiempo por proceso, Tasa de defectos, Productos terminados |
| **Facturación** | Tiempo de aprobación, Montos facturados, Tasa de rechazo |

### **Generales**
- **Trazabilidad**: Ubicación en tiempo real de cada producto
- **Eficiencia**: Tiempo total desde ingreso hasta despacho
- **Calidad**: Porcentaje de productos defectuosos
- **Financiero**: Montos facturados por período

## 🎯 **Casos de Uso Principales**

### 1. **Producto para Almacenaje Simple**
```
Ingreso → Clasificación (Almacenaje) → Ubicación → Listo para Despacho
```

### 2. **Producto para Procesamiento**
```
Ingreso → Clasificación (Procesamiento) → Espera → Orden → Aprobación → 
Procesos → Validación → Devolución → Despacho → Facturación
```

### 3. **Producto Defectuoso**
```
Proceso → Evaluación (Defectuoso) → Observaciones → Acumulación → 
Devolución Especial → Ubicación Defectuosos
```

## 🔧 **Herramientas de Desarrollo**

### **Para Visualizar Diagramas**
- **GitHub**: Renderizado automático de Mermaid
- **VS Code**: Extensión "Mermaid Preview"
- **Mermaid Live Editor**: https://mermaid.live/

### **Para Modificar**
- Editar archivos `.md` directamente  
- Sintaxis Mermaid estándar
- Colores y estilos configurables

## 📚 **Referencias Relacionadas**

- **[database.sql](../../database.sql)** - Estructura de base de datos
- **[CONSULTAS_SQL_FLUJO.md](../CONSULTAS_SQL_FLUJO.md)** - Consultas optimizadas
- **[ESTRUCTURA_BASE_DATOS.md](../ESTRUCTURA_BASE_DATOS.md)** - Documentación técnica de BD
- **[flujo.md](./flujo.md)** - Especificaciones originales del flujo

---

## 🚀 **Próximos Pasos**

1. **Implementar APIs**: Crear endpoints basados en los flujos
2. **Frontend**: Interfaces para cada módulo siguiendo los diagramas
3. **Pruebas**: Validar cada flujo con casos de uso reales
4. **Optimización**: Métricas y mejoras basadas en uso real

**💡 Los diagramas están listos para guiar el desarrollo completo del sistema SIGA.**
