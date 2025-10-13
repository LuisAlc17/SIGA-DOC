# 📚 Documentación de Endpoints - Flujo de Inicio de Sesión SIGA

## 📋 Tabla de Contenidos
1. [Resumen General](#resumen-general)
2. [Endpoints de Autenticación](#endpoints-de-autenticación)
3. [Flujo de Implementación](#flujo-de-implementación)
4. [Esquemas de Base de Datos](#esquemas-de-base-de-datos)
5. [Ejemplos de Uso](#ejemplos-de-uso)

---

## 🎯 Resumen General

Los nuevos endpoints en `/api/auth-v2/` implementan el flujo completo de inicio de sesión según las especificaciones del documento `FLUJO_INICIO_SESION.MD`:

### ✅ Características Implementadas:
- **Validación de licencias** antes del login
- **4 tipos de usuario** con permisos específicos
- **Cambio de contraseña** obligatorio en primer login
- **Recuperación de contraseña** via email
- **Login con email** como usuario principal
- **Sistema de notificaciones** configurable
- **Auditoría completa** de accesos

---

## 🔐 Endpoints de Autenticación

### 1. **Validación de Licencias**
```http
GET /api/auth-v2/validate-license?almacen_id=1&device_hash=abc123
```

**Descripción**: Primer paso del flujo - valida licencias antes de mostrar login.

**Query Parameters**:
- `almacen_id` (optional): ID del almacén (default: 1)
- `device_hash` (optional): Hash único del dispositivo

**Respuesta Exitosa**:
```json
{
  "success": true,
  "message": "Licencias válidas",
  "data": {
    "valid": true,
    "license_type": "enterprise",
    "available_users": 15,
    "expiration_date": "2025-12-31T23:59:59.000Z",
    "can_proceed_to_login": true
  }
}
```

**Respuesta Error**:
```json
{
  "success": false,
  "message": "La licencia ha expirado",
  "codigo": "LICENSE_INVALID",
  "data": {
    "valid": false,
    "can_proceed_to_login": false
  }
}
```

---

### 2. **Login Mejorado**
```http
POST /api/auth-v2/login
```

**Headers**:
- `Content-Type: application/json`
- `X-Device-Hash: hash_del_dispositivo` (opcional)

**Body**:
```json
{
  "username": "admin@siga.com",
  "password": "admin123"
}
```

**Respuesta Exitosa**:
```json
{
  "success": true,
  "message": "Login exitoso",
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "user": {
    "id": 2,
    "username": "ADM-001",
    "name": "Administrador Sistema",
    "email": "admin@siga.com",
    "role": "admin",
    "tipo_usuario": "administrador",
    "nivel_acceso": 4,
    "primer_login": false,
    "debe_cambiar_password": false,
    "almacen": {
      "codigo": "ALM-001",
      "nombre": "Almacén Principal"
    }
  },
  "permissions": [
    {
      "codigo_modulo": "DASHBOARD",
      "modulo_nombre": "Dashboard",
      "ruta_sistema": "/dashboard",
      "puede_ver": true,
      "puede_crear": true,
      "puede_editar": true,
      "puede_eliminar": true
    }
  ],
  "expires_in": "24h",
  "requires_password_change": false,
  "timestamp": "2025-07-28T12:00:00.000Z"
}
```

**Códigos de Error**:
- `MISSING_CREDENTIALS`: Faltan credenciales
- `USER_NOT_FOUND`: Usuario no encontrado
- `USER_INACTIVE`: Usuario inactivo
- `USER_BLOCKED`: Usuario bloqueado por intentos fallidos
- `INVALID_PASSWORD`: Contraseña incorrecta

---

### 3. **Cambio de Contraseña**
```http
POST /api/auth-v2/change-password
```

**Headers**:
- `Authorization: Bearer <token>`
- `Content-Type: application/json`

**Body para Primer Login**:
```json
{
  "new_password": "nuevaPassword123",
  "confirm_password": "nuevaPassword123",
  "is_first_login": true
}
```

**Body para Cambio Voluntario**:
```json
{
  "current_password": "passwordActual123",
  "new_password": "nuevaPassword123",
  "confirm_password": "nuevaPassword123",
  "is_first_login": false
}
```

**Respuesta Exitosa**:
```json
{
  "success": true,
  "message": "Contraseña cambiada exitosamente",
  "timestamp": "2025-07-28T12:00:00.000Z"
}
```

---

### 4. **Solicitar Recuperación de Contraseña**
```http
POST /api/auth-v2/request-password-recovery
```

**Body**:
```json
{
  "email": "usuario@empresa.com"
}
```

**Respuesta**:
```json
{
  "success": true,
  "message": "Si el email existe, se enviará un enlace de recuperación",
  "debug_token": "abc123def456...",
  "debug_expiration": "2025-07-28T14:00:00.000Z"
}
```

---

### 5. **Resetear Contraseña con Token**
```http
POST /api/auth-v2/reset-password
```

**Body**:
```json
{
  "token": "abc123def456...",
  "new_password": "nuevaPassword123",
  "confirm_password": "nuevaPassword123"
}
```

**Respuesta Exitosa**:
```json
{
  "success": true,
  "message": "Contraseña reseteada exitosamente",
  "timestamp": "2025-07-28T12:00:00.000Z"
}
```

---

### 6. **Obtener Perfil de Usuario**
```http
GET /api/auth-v2/profile
```

**Headers**:
- `Authorization: Bearer <token>`

**Respuesta**:
```json
{
  "success": true,
  "data": {
    "id": 2,
    "codigo_empleado": "ADM-001",
    "nombre_completo": "Administrador Sistema",
    "email": "admin@siga.com",
    "telefono": "+1234567890",
    "tipo_usuario": {
      "codigo": "ADMIN",
      "nombre": "Administrador",
      "descripcion": "Puede editar y visualizar todos los módulos..."
    },
    "rol": {
      "nombre": "Administrador",
      "es_superadmin": true
    },
    "almacen": {
      "codigo": "ALM-001",
      "nombre": "Almacén Principal"
    },
    "ultimo_acceso": "2025-07-28T11:30:00.000Z",
    "fecha_ultimo_cambio_password": "2025-07-27T10:00:00.000Z",
    "debe_cambiar_password": false,
    "configuraciones_notificacion": [
      {
        "tipo_evento": "login",
        "recibe_email": true,
        "recibe_notificacion_app": true
      }
    ]
  }
}
```

---

### 7. **Logout**
```http
POST /api/auth-v2/logout
```

**Headers**:
- `Authorization: Bearer <token>`

**Respuesta**:
```json
{
  "success": true,
  "message": "Logout exitoso",
  "timestamp": "2025-07-28T12:00:00.000Z"
}
```

---

## 🚀 Flujo de Implementación

### 1. **Aplicar Mejoras a la Base de Datos**
```bash
cd "d:\Back-End_SIGA v.3"
node aplicar-mejoras-db.js
```

### 2. **Verificar Servidor**
El servidor debe estar ejecutándose con los nuevos endpoints:
```bash
npm run dev
```

### 3. **Probar Endpoints**
Los endpoints están disponibles en: `http://localhost:3001/api/auth-v2/`

---

## 🗄️ Esquemas de Base de Datos

### **Nuevas Tablas Creadas**:

#### `Tipo_Usuario`
Tipos de usuario según especificaciones:
- **ADMIN**: Administrador (sin licencia requerida)
- **OPERADOR**: Operador de módulo
- **AUDITOR**: Solo lectura
- **OPERATIVO**: Solo módulo operaciones

#### `Email_Notificacion`
Emails adicionales para notificaciones del sistema.

#### `Usuario_Notificacion_Config`
Configuración de notificaciones por usuario y tipo de evento.

#### `Historial_Password`
Historial de cambios de contraseña con motivos.

#### `Token_Recuperacion`
Tokens para recuperación de contraseña con expiración.

#### `Sesion_Usuario`
Control de sesiones activas por usuario y dispositivo.

### **Vistas Creadas**:

#### `vw_usuario_login`
Vista consolidada con toda la información necesaria para login.

### **Stored Procedures**:

#### `sp_validar_licencias`
Valida licencias y disponibilidad de usuarios.

#### `sp_login_usuario`
Proceso completo de login con todas las validaciones.

---

## 🧪 Ejemplos de Uso

### **Flujo Completo de Login**:

1. **Validar Licencias**:
```bash
curl -X GET "http://localhost:3001/api/auth-v2/validate-license?almacen_id=1"
```

2. **Login**:
```bash
curl -X POST "http://localhost:3001/api/auth-v2/login" \
  -H "Content-Type: application/json" \
  -d '{
    "username": "ADM-001",
    "password": "admin123"
  }'
```

3. **Obtener Perfil** (con token del login):
```bash
curl -X GET "http://localhost:3001/api/auth-v2/profile" \
  -H "Authorization: Bearer YOUR_TOKEN_HERE"
```

### **Flujo de Recuperación de Contraseña**:

1. **Solicitar Recuperación**:
```bash
curl -X POST "http://localhost:3001/api/auth-v2/request-password-recovery" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@siga.com"
  }'
```

2. **Resetear con Token**:
```bash
curl -X POST "http://localhost:3001/api/auth-v2/reset-password" \
  -H "Content-Type: application/json" \
  -d '{
    "token": "TOKEN_FROM_EMAIL",
    "new_password": "newPassword123",
    "confirm_password": "newPassword123"
  }'
```

---

## 🎭 Tipos de Usuario y Permisos

### **👑 Administrador (ADMIN)**
- ✅ Todos los módulos
- ✅ Gestión de usuarios y licencias
- ✅ Configuraciones del sistema
- ✅ **No requiere licencia**
- ✅ Acceso desde cualquier dispositivo

### **⚙️ Operador de Módulo (OPERADOR)**
- ✅ Módulos asignados (puede tener 2 o más)
- ✅ Edición permitida en sus módulos
- ✅ Reportes básicos
- ❌ Requiere licencia
- ❌ Dispositivo específico

### **📊 Auditor (AUDITOR)**
- ✅ Consultas en todos los módulos (solo lectura)
- ✅ Descarga de datos y reportes
- ❌ Sin permisos de edición
- ❌ Requiere licencia

### **🏭 Usuario Operativo (OPERATIVO)**
- ✅ Solo módulo de operaciones
- ✅ Procesos específicos asignados
- ❌ Sin acceso a otros módulos
- ❌ Requiere licencia

---

## 🔔 Sistema de Notificaciones

Los usuarios reciben notificaciones según su configuración para eventos como:
- 🔐 Login exitoso/fallido
- 🔑 Cambio de contraseña
- 👤 Usuario bloqueado
- 📦 Movimientos de inventario
- ⚠️ Alertas críticas

La distribución se hace automáticamente según:
- **Tipo de usuario**
- **Módulos asignados** 
- **Configuración individual**
- **Emails adicionales** configurados por administradores

---

## 📝 Notas de Implementación

1. **Seguridad**: Todos los passwords se hashean con bcrypt (12 rounds)
2. **Tokens**: JWT con expiración de 24 horas
3. **Sesiones**: Control granular por dispositivo
4. **Auditoría**: Log completo de todos los accesos
5. **Recuperación**: Tokens de recuperación válidos por 2 horas
6. **Bloqueo**: 3 intentos fallidos = bloqueo temporal 30 minutos

¡Los endpoints están listos para usar! 🚀
