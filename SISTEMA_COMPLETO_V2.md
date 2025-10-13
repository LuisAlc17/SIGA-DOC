# SISTEMA SIGA - VERSIÓN COMPLETA

## Descripción del Sistema

El **Sistema SIGA Completo** es una solución empresarial avanzada para la gestión integral de almacenes, operaciones, logística y facturación. Esta versión incluye funcionalidades especializadas para diferentes tipos de productos, gestión de reparaciones, manejo de SCRAP, y un sistema completo de trazabilidad.

## Características Principales

### 🏢 **Gestión Multi-Almacén**
- Jerarquía de ubicaciones de 5 niveles (País → Estado/Región → Ciudad → Almacén → Ubicación)
- Capacidades por peso, volumen y unidades
- Ubicaciones especializadas para diferentes propósitos

### 👥 **Sistema de Usuarios Avanzado**
- 7 niveles de roles predefinidos
- Perfiles operativos especializados por proceso
- Sistema de permisos granular por módulo
- Especialización técnica para reparaciones

### 📦 **Gestión de Productos Especializada**
- **4 tipos de productos** con tablas de detalle específicas:
  - **Almacenaje/Procesamiento**: Para productos que se procesan
  - **Activos**: Bienes de la empresa con información patrimonial
  - **Suministros**: Insumos y materiales de consumo
  - **Otros**: Productos de categorías especiales

### 🔧 **Módulo de Reparaciones Completo**
- Categorías y procesos de reparación detallados
- Técnicos especialistas con certificaciones
- Órdenes de servicio con diagnóstico completo
- Control de repuestos y garantías
- Seguimiento de tiempo y costos

### ♻️ **Gestión Avanzada de SCRAP**
- Tipos de SCRAP con normativas aplicables
- Ubicaciones especializadas para diferentes tipos
- Evaluación de valor recuperable
- Trazabilidad completa del proceso

### 📋 **Sistema de Órdenes de Trabajo**
- Múltiples niveles de aprobación
- Asignación detallada de productos y operadores
- Seguimiento granular de procesos
- Control de tiempos y costos por proceso

### 💰 **Facturación Integral**
- Tipos de facturación configurables
- Flujo de aprobación contable
- Control de impuestos y descuentos
- Registro de pagos y comprobantes

### 📊 **Sistema de Reportes y Análisis**
- Categorías de reportes organizadas
- Definición de reportes con SQL dinámico
- Parámetros configurables
- Exportación de resultados

### 🔔 **Notificaciones Avanzadas**
- Sistema de plantillas personalizables
- Múltiples canales (sistema, email, SMS, push)
- Notificaciones con vencimiento
- Seguimiento de acciones requeridas

### 📈 **Trazabilidad Completa**
- Historial detallado de todos los movimientos
- Registro de condiciones ambientales
- Documentación fotográfica
- Seguimiento de valores y pesos

## Arquitectura de la Base de Datos

### Tablas Principales (50+ tablas)

#### **Módulos del Sistema:**
1. **Gestión de Usuarios** (7 tablas)
2. **Gestión de Ubicaciones** (6 tablas)
3. **Gestión de Productos** (8 tablas)
4. **Movimientos de Almacén** (4 tablas)
5. **Órdenes de Trabajo** (8 tablas)
6. **Módulo de Reparaciones** (6 tablas)
7. **Gestión de SCRAP** (4 tablas)
8. **Sistema de Observaciones** (3 tablas)
9. **Facturación** (5 tablas)
10. **Trazabilidad** (3 tablas)
11. **Notificaciones** (4 tablas)
12. **Reportes** (4 tablas)

### Características Técnicas

- **Base de datos**: SQL Server 2019+
- **Índices optimizados** para consultas frecuentes
- **Claves foráneas** para integridad referencial
- **Campos auditables** (fechas de creación/modificación)
- **Estados configurables** para flujos de trabajo
- **Soporte para archivos adjuntos** (fotos, documentos)

## Flujos de Trabajo Principales

### 1. **Flujo de Ingreso de Productos**
```
Ingreso → Ubicación → Inspección → Aprobación → Disponible
```

### 2. **Flujo de Procesamiento**
```
Asignación → Orden de Trabajo → Procesos → Control Calidad → Terminado
```

### 3. **Flujo de Reparación**
```
Defecto Detectado → Orden Servicio → Diagnóstico → Reparación → Pruebas → Recuperado
```

### 4. **Flujo de SCRAP**
```
Generación → Evaluación → Autorización → Ubicación → Disposición
```

### 5. **Flujo de Despacho**
```
Solicitud → Preparación → Facturación → Despacho → Entrega
```

## Instalación

### Prerrequisitos
- SQL Server 2019 o superior
- Node.js 16+ con soporte para mssql
- Espacio en disco: mínimo 5GB para datos

### Pasos de Instalación

1. **Crear la base de datos**:
```sql
CREATE DATABASE SIGA_DB;
USE SIGA_DB;
```

2. **Ejecutar scripts de estructura**:
```sql
-- Ejecutar database_completo_v2.sql
-- Seguido de database_completo_v2_parte2.sql
```

3. **Configurar conexión en Node.js**:
```javascript
const config = {
    user: 'tu_usuario',
    password: 'tu_password',
    server: 'localhost',
    database: 'SIGA_DB',
    port: 1433,
    options: {
        encrypt: false,
        trustServerCertificate: true
    }
};
```

## Configuración Inicial

### Datos Maestros Incluidos

- **7 roles** predefinidos (desde Operador hasta Superadministrador)
- **7 módulos** del sistema con rutas y permisos
- **11 estados** de productos para diferentes flujos
- **4 tipos** de productos con sus especializaciones
- **6 tipos** de ubicaciones para diferentes propósitos

### Primer Usuario Administrador

```sql
-- Crear después de ejecutar los scripts
INSERT INTO Usuario (codigo_empleado, nombre, apellido, email, password_hash, rol_id, activo)
VALUES ('ADMIN001', 'Administrador', 'Sistema', 'admin@empresa.com', 'hash_password', 1, 1);
```

## API Endpoints Sugeridos

### Autenticación
- `POST /api/auth/login`
- `POST /api/auth/logout`
- `GET /api/auth/profile`

### Productos
- `GET/POST /api/productos`
- `GET/PUT /api/productos/:id`
- `GET /api/productos/serie/:serie`

### Movimientos
- `GET/POST /api/movimientos`
- `PUT /api/movimientos/:id/estado`

### Órdenes de Trabajo
- `GET/POST /api/ordenes`
- `PUT /api/ordenes/:id/aprobar`

### Reparaciones
- `GET/POST /api/reparaciones`
- `PUT /api/reparaciones/:id/diagnostico`

### Reportes
- `GET /api/reportes/categorias`
- `POST /api/reportes/ejecutar/:id`

## Rendimiento y Escalabilidad

### Índices Implementados
- Índices en campos de búsqueda frecuente
- Índices compuestos para consultas complejas
- Índices en claves foráneas para joins

### Optimizaciones
- Paginación en consultas grandes
- Cache de reportes frecuentes
- Compresión de archivos adjuntos

## Seguridad

### Características de Seguridad
- Hashing de contraseñas
- Control de acceso por roles
- Auditoría de cambios críticos
- Logs de acceso y operaciones

### Respaldos Recomendados
- Respaldo completo diario
- Respaldo incremental cada 4 horas
- Respaldo de transacciones cada 15 minutos

## Mantenimiento

### Tareas Periódicas
- Limpieza de logs antiguos (mensual)
- Optimización de índices (semanal)
- Análisis de rendimiento (mensual)
- Actualización de estadísticas (diario)

## Soporte y Documentación

### Archivos de Documentación Incluidos
- `ESTRUCTURA_COMPLETA.md`: Diagrama de relaciones
- `GUIA_SQL_SERVER.md`: Configuración de base de datos
- `SISTEMA_LICENCIAS_FINAL.md`: Gestión de licencias
- `DIAGRAMA_FLUJO_*.md`: Flujos de trabajo detallados

### Logs del Sistema
- Errores de aplicación: `/logs/error.log`
- Accesos de usuario: `/logs/access.log`
- Operaciones críticas: `/logs/audit.log`

---

**Versión**: 2.0 Completa  
**Última actualización**: $(Get-Date -Format "yyyy-MM-dd")  
**Desarrollado para**: Sistema empresarial de gestión integral  
**Tecnologías**: SQL Server, Node.js, Express, React/Electron
