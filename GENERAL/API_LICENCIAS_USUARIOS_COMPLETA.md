# API de Licencias con Gestión de Usuarios

## Descripción General

Esta API proporciona un sistema completo de gestión de licencias con control granular de usuarios. Permite crear, activar, gestionar y monitorear licencias con capacidades específicas de asignación de usuarios por licencia.

## Características Principales

- ✅ **Gestión completa de licencias**: Crear, activar, consultar y actualizar
- ✅ **Control granular de usuarios**: Asignar/revocar usuarios específicos por licencia
- ✅ **Validación de acceso multinivel**: Licencia + Usuario + Estado activo
- ✅ **Dashboard administrativo**: Estadísticas y reportes de uso
- ✅ **Auditoría completa**: Registro de asignaciones, revocaciones y accesos
- ✅ **Límites por licencia**: Control de cantidad máxima de usuarios por licencia
- ✅ **Estados dinámicos**: Manejo automático de licencias expiradas
- ✅ **Identificación de equipos**: Hash único por equipo cliente

## Base URL
```
http://localhost:3000/api/licencias
```

---

## 📋 ENDPOINTS DE VALIDACIÓN Y VERIFICACIÓN

### POST /verificar
**Verificar una licencia específica**

```javascript
// Request Body
{
  "token": "ABC123...", // opcional
  "equipoId": "hash_equipo", // opcional  
  "usuarioId": 123 // opcional
}

// Response Success
{
  "success": true,
  "data": {
    "valida": true,
    "licencia": {
      "id": 1,
      "token": "ABC123...",
      "equipo_id": "hash_equipo",
      "fecha_activacion": "2025-01-01T00:00:00.000Z",
      "fecha_expiracion": "2026-01-01T00:00:00.000Z",
      "dias_restantes": 365,
      "almacen": {
        "id": 1,
        "nombre": "Almacén Principal"
      }
    },
    "alerta": "Licencia expira en 30 días" // si aplica
  },
  "timestamp": "2025-07-26T00:00:00.000Z"
}

// Response Error
{
  "success": false,
  "data": {
    "valida": false,
    "motivo": "Usuario no autorizado para usar esta licencia",
    "codigo": "USER_NOT_AUTHORIZED",
    "detalles": "Usuario no está asignado a esta licencia"
  }
}
```

### POST /validar-acceso
**Validar acceso completo al sistema**

```javascript
// Request Body
{
  "usuarioId": 123 // opcional
}

// Response Success
{
  "success": true,
  "data": {
    "acceso_permitido": true,
    "licencia_info": {
      "dias_restantes": 365,
      "fecha_expiracion": "2026-01-01T00:00:00.000Z",
      "almacen": {
        "id": 1,
        "nombre": "Almacén Principal"
      }
    },
    "alerta": null,
    "equipo_id": "hash_equipo_actual"
  }
}
```

### GET /info-equipo
**Obtener información del equipo actual**

```javascript
// Response
{
  "success": true,
  "data": {
    "equipo_id": "a1b2c3d4e5f6...",
    "hostname": "PC-OFICINA-01",
    "platform": "win32",
    "arch": "x64",
    "fecha_consulta": "2025-07-26T00:00:00.000Z"
  }
}
```

---

## 🔧 ENDPOINTS DE GESTIÓN DE LICENCIAS

### POST /
**Crear nueva licencia**

```javascript
// Request Body
{
  "usuarios_permitidos": 10, // opcional, default: 5
  "tipo_licencia": "premium", // opcional, default: "estandar"
  "almacen_id": 1, // opcional
  "meses_validez": 12 // opcional, default: 12
}

// Response
{
  "success": true,
  "message": "Licencia creada exitosamente",
  "data": {
    "id_licencia": 123,
    "token": "ABC123-DEF456-GHI789-JKL012",
    "estado": "pendiente",
    "usuarios_permitidos": 10,
    "tipo_licencia": "premium",
    "fecha_creacion": "2025-07-26T00:00:00.000Z",
    "mensaje": "Licencia creada exitosamente. Lista para activación en cliente."
  }
}
```

### POST /activar
**Activar licencia en cliente**

```javascript
// Request Body
{
  "token": "ABC123-DEF456-GHI789-JKL012",
  "equipoId": "hash_equipo_cliente",
  "meses_validez": 12, // opcional
  "almacen_id": 1 // opcional
}

// Response
{
  "success": true,
  "message": "Licencia activada exitosamente",
  "data": {
    "token": "ABC123-DEF456-GHI789-JKL012",
    "equipo_id": "hash_equipo_cliente",
    "fecha_activacion": "2025-07-26T00:00:00.000Z",
    "fecha_expiracion": "2026-07-26T00:00:00.000Z",
    "meses_validez": 12,
    "almacen_id": 1,
    "mensaje": "Licencia activada exitosamente"
  }
}
```

### GET /
**Obtener todas las licencias**

```javascript
// Response
{
  "success": true,
  "data": {
    "licencias": [
      {
        "id_licencia": 1,
        "token_licencia": "ABC123...",
        "hash_hardware": "equipo_hash",
        "fecha_activacion": "2025-01-01T00:00:00.000Z",
        "fecha_expiracion": "2026-01-01T00:00:00.000Z",
        "estado": "activa",
        "usuarios_permitidos": 10,
        "usuarios_asignados": 3,
        "tipo_licencia": "premium",
        "almacen_nombre": "Almacén Principal",
        "estado_calculado": "Activa",
        "dias_restantes": 365
      }
    ],
    "total": 1
  }
}
```

### GET /:id
**Obtener licencia por ID**

```javascript
// Response
{
  "success": true,
  "data": {
    "id_licencia": 1,
    "token_licencia": "ABC123...",
    "hash_hardware": "equipo_hash",
    "fecha_activacion": "2025-01-01T00:00:00.000Z",
    "fecha_expiracion": "2026-01-01T00:00:00.000Z",
    "estado": "activa",
    "almacen_id": 1,
    "usuarios_permitidos": 10,
    "tipo_licencia": "premium",
    "almacen_nombre": "Almacén Principal"
  }
}
```

### PUT /:id/estado
**Actualizar estado de licencia**

```javascript
// Request Body
{
  "estado": "activa" // activa|inactiva|expirada|suspendida|pendiente
}

// Response
{
  "success": true,
  "message": "Estado de licencia actualizado exitosamente",
  "data": {
    "id_licencia": "1",
    "nuevo_estado": "activa"
  }
}
```

---

## 👥 ENDPOINTS DE GESTIÓN DE USUARIOS

### POST /:licencia_id/usuarios
**Asignar usuario a licencia**

```javascript
// Request Body
{
  "usuario_id": 123,
  "asignado_por": 1 // opcional, se toma del middleware de auth
}

// Response Success
{
  "success": true,
  "message": "Usuario asignado correctamente a la licencia",
  "data": {
    "licencia_id": "1",
    "usuario_id": 123,
    "fecha_asignacion": "2025-07-26T00:00:00.000Z"
  }
}

// Response Error - Límite alcanzado
{
  "success": false,
  "message": "Límite de usuarios alcanzado (10)",
  "codigo": "ASSIGNMENT_ERROR"
}
```

### DELETE /:licencia_id/usuarios/:usuario_id
**Revocar usuario de licencia**

```javascript
// Request Body
{
  "motivo": "Usuario ya no requiere acceso", // opcional
  "revocado_por": 1 // opcional
}

// Response
{
  "success": true,
  "message": "Acceso del usuario revocado correctamente",
  "data": {
    "licencia_id": "1",
    "usuario_id": "123",
    "fecha_revocacion": "2025-07-26T00:00:00.000Z",
    "motivo": "Usuario ya no requiere acceso"
  }
}
```

### GET /:licencia_id/usuarios
**Listar usuarios de una licencia**

```javascript
// Response
{
  "success": true,
  "data": {
    "licencia_id": "1",
    "usuarios": [
      {
        "id_asignacion": 1,
        "usuario": {
          "id": 123,
          "codigo": "EMP001",
          "nombre": "Juan Pérez",
          "email": "juan.perez@empresa.com",
          "activo": true
        },
        "fecha_asignacion": "2025-07-26T00:00:00.000Z",
        "asignado_por": "Admin Sistema"
      }
    ],
    "total_usuarios": 1
  }
}
```

### GET /:licencia_id/usuarios/:usuario_id/acceso
**Verificar acceso específico de usuario**

```javascript
// Response - Con acceso
{
  "success": true,
  "data": {
    "licencia_id": "1",
    "usuario_id": "123",
    "acceso": {
      "tieneAcceso": true,
      "asignacion": {
        "id": 1,
        "fecha_asignacion": "2025-07-26T00:00:00.000Z",
        "usuario": {
          "nombre": "Juan Pérez",
          "codigo": "EMP001"
        }
      }
    }
  }
}

// Response - Sin acceso
{
  "success": true,
  "data": {
    "licencia_id": "1",
    "usuario_id": "123",
    "acceso": {
      "tieneAcceso": false,
      "motivo": "Usuario no está asignado a esta licencia"
    }
  }
}
```

---

## 📊 ENDPOINTS ADMINISTRATIVOS

### GET /admin/dashboard
**Dashboard de licencias con estadísticas**

```javascript
// Response
{
  "success": true,
  "data": {
    "estadisticas": {
      "total_licencias": 25,
      "activas": 20,
      "expiradas": 3,
      "por_expirar": 2,
      "pendientes": 5,
      "total_usuarios_asignados": 150,
      "licencias_por_tipo": {
        "estandar": 15,
        "premium": 8,
        "empresarial": 2
      }
    },
    "licencias_recientes": [
      // Últimas 10 licencias creadas
    ]
  }
}
```

### GET /admin/reporte-uso
**Reporte detallado de uso de licencias**

```javascript
// Query Parameters
// ?fecha_inicio=2025-01-01&fecha_fin=2025-12-31

// Response
{
  "success": true,
  "data": {
    "periodo": {
      "fecha_inicio": "2025-01-01",
      "fecha_fin": "2025-12-31"
    },
    "reporte": [
      {
        "id_licencia": 1,
        "token": "ABC123...",
        "estado": "activa",
        "fecha_activacion": "2025-01-01T00:00:00.000Z",
        "fecha_expiracion": "2026-01-01T00:00:00.000Z",
        "dias_restantes": 365,
        "usuarios_permitidos": 10,
        "usuarios_asignados": 7,
        "porcentaje_uso": "70.00",
        "almacen": "Almacén Principal",
        "tipo_licencia": "premium"
      }
    ],
    "resumen": {
      "total_licencias": 25,
      "promedio_uso": "65.40"
    }
  }
}
```

### POST /admin/sincronizar-estados
**Sincronizar estados de licencias automáticamente**

```javascript
// Response
{
  "success": true,
  "message": "Sincronización completada",
  "data": {
    "licencias_actualizadas": 3,
    "total_procesadas": 25
  }
}
```

### POST /admin/generar-lote
**Generar múltiples licencias**

```javascript
// Request Body
{
  "cantidad": 5,
  "opciones": {
    "usuarios_permitidos": 10,
    "tipo_licencia": "estandar",
    "almacen_id": 1
  }
}

// Response
{
  "success": true,
  "message": "Proceso completado: 5 licencias creadas",
  "data": {
    "licencias_creadas": [
      {
        "id_licencia": 101,
        "token": "ABC123-DEF456-GHI789-JKL012",
        "estado": "pendiente",
        "usuarios_permitidos": 10,
        "tipo_licencia": "estandar"
      }
      // ... más licencias
    ],
    "total_exitosas": 5,
    "total_errores": 0,
    "errores": []
  }
}
```

---

## 🔒 CÓDIGOS DE ERROR

| Código | Descripción |
|--------|-------------|
| `LICENSE_NOT_FOUND` | Licencia no encontrada o inactiva |
| `LICENSE_EXPIRED` | Licencia ha expirado |
| `USER_NOT_AUTHORIZED` | Usuario no autorizado para usar la licencia |
| `WAREHOUSE_INACTIVE` | Almacén asociado está inactivo |
| `MISSING_PARAMETERS` | Parámetros requeridos faltantes |
| `INVALID_STATUS` | Estado de licencia no válido |
| `ASSIGNMENT_ERROR` | Error en asignación de usuario |
| `REVOCATION_ERROR` | Error en revocación de usuario |
| `CREATE_LICENSE_ERROR` | Error creando licencia |
| `ACTIVATION_ERROR` | Error activando licencia |
| `TOKEN_NOT_FOUND` | Token de licencia no encontrado |
| `ALREADY_ACTIVE` | Licencia ya está activada |
| `INVALID_QUANTITY` | Cantidad no válida para lote |

---

## 🚀 FLUJO DE IMPLEMENTACIÓN RECOMENDADO

### 1. **Creación de Licencia (Servidor/Admin)**
```javascript
POST /api/licencias
{
  "usuarios_permitidos": 10,
  "tipo_licencia": "premium",
  "meses_validez": 12
}
// Resultado: Token para distribuir al cliente
```

### 2. **Activación en Cliente**
```javascript
POST /api/licencias/activar
{
  "token": "ABC123-DEF456-GHI789-JKL012",
  "equipoId": "auto_generado_por_cliente"
}
// Licencia queda activa y vinculada al equipo
```

### 3. **Asignación de Usuarios**
```javascript
POST /api/licencias/1/usuarios
{
  "usuario_id": 123
}
// Usuario autorizado para usar la licencia
```

### 4. **Validación en Runtime**
```javascript
POST /api/licencias/validar-acceso
{
  "usuarioId": 123
}
// Verificar acceso antes de permitir funcionalidades
```

### 5. **Monitoreo Administrativo**
```javascript
GET /api/licencias/admin/dashboard
// Estadísticas completas del sistema
```

---

## 📝 NOTAS IMPORTANTES

1. **Seguridad**: Todos los endpoints deberían implementar autenticación apropiada en producción
2. **Validación**: El sistema valida automáticamente licencias expiradas
3. **Escalabilidad**: Diseñado para manejar múltiples licencias y usuarios concurrentes
4. **Auditoría**: Todas las operaciones quedan registradas con timestamp y usuario responsable
5. **Flexibilidad**: Permite diferentes tipos de licencia y configuraciones por almacén

---

## 🔧 CONFIGURACIÓN DE BASE DE DATOS

El sistema requiere las siguientes tablas:
- `Licencia_Sistema`: Almacena información de licencias
- `Licencia_Usuario`: Relación many-to-many entre licencias y usuarios
- `Usuario`: Información de usuarios del sistema
- `Almacen`: Información de almacenes (opcional)

Ver `database_sistema_completo_final.sql` para el esquema completo.
