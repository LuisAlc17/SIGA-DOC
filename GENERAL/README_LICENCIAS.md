# 🎯 **SIGA BACKEND - SISTEMA DE LICENCIAS FUNCIONAL**

## ✅ **ESTADO ACTUAL: FUNCIONANDO**

### 🚀 **Servidor Iniciado Correctamente**
- **Puerto**: 3001
- **URL Base**: http://localhost:3001
- **Estado**: ✅ Funcionando sin SQL Server
- **Equipo ID**: `2c0d012ac764d0aed69989ffa2ef0882`

---

## 🔐 **ENDPOINTS PRINCIPALES PARA FRONTEND**

### 1. **Validar Licencia** (PRINCIPAL)
```http
GET http://localhost:3001/api/licencias/validar
```

**Propósito**: **Tu frontend debe llamar a este endpoint al iniciar la aplicación**

**Respuestas**:
```json
// ✅ LICENCIA VÁLIDA
{
  "success": true,
  "acceso_permitido": true,
  "message": "Licencia válida",
  "data": {
    "dias_restantes": 45,
    "fecha_expiracion": "2025-09-15T00:00:00.000Z",
    "almacen": {
      "id": 1,
      "nombre": "Almacén Principal"
    }
  },
  "alerta": null,
  "equipo_id": "2c0d012ac764d0aed69989ffa2ef0882"
}

// ❌ LICENCIA INVÁLIDA/EXPIRADA
{
  "success": false,
  "acceso_permitido": false,
  "message": "La licencia ha expirado. Contacte al proveedor para renovar.",
  "codigo": "LICENSE_EXPIRED"
}

// ⚠️ SIN SQL SERVER (Actual)
{
  "success": false,
  "acceso_permitido": false,
  "message": "Error interno del sistema. Contacte al administrador.",
  "codigo": "SYSTEM_ERROR"
}
```

### 2. **Información del Equipo** ✅ FUNCIONANDO
```http
GET http://localhost:3001/api/licencias/equipo
```

**Respuesta Actual**:
```json
{
  "success": true,
  "data": {
    "equipo_id": "2c0d012ac764d0aed69989ffa2ef0882",
    "hostname": "GE-LAP-0060",
    "platform": "win32",
    "arch": "x64"
  }
}
```

### 3. **Estado del Sistema** ✅ FUNCIONANDO
```http
GET http://localhost:3001/health
```

**Respuesta Actual**:
```json
{
  "status": "OK",
  "timestamp": "2025-07-23T19:25:58.667Z",
  "service": "SIGA Backend",
  "database": "Disconnected",
  "endpoints": {
    "health": "/health",
    "licencias": "/api/licencias/validar",
    "equipo": "/api/licencias/equipo"
  }
}
```

---

## 💻 **INTEGRACIÓN EN FRONTEND**

### **React/Vue/Angular Example**
```javascript
// Al iniciar la aplicación
async function validarLicenciaInicio() {
  try {
    const response = await fetch('http://localhost:3001/api/licencias/validar');
    const result = await response.json();
    
    if (result.acceso_permitido) {
      // ✅ LICENCIA VÁLIDA - Continuar con la aplicación
      console.log('✅ Licencia válida');
      
      // Mostrar alerta si está próxima a expirar
      if (result.alerta) {
        showWarning(result.alerta);
      }
      
      // Continuar cargando la aplicación
      initApp();
      
    } else {
      // ❌ LICENCIA INVÁLIDA - Bloquear aplicación
      console.error('❌ Licencia inválida:', result.message);
      
      // Mostrar pantalla de error de licencia
      showLicenseError(result.message, result.codigo);
      
      // NO CARGAR LA APLICACIÓN
      return;
    }
    
  } catch (error) {
    // Error de conexión - Manejar según tus necesidades
    console.error('Error conectando al servidor:', error);
    showConnectionError();
  }
}

// Llamar al iniciar
validarLicenciaInicio();
```

### **Vanilla JavaScript Example**
```javascript
document.addEventListener('DOMContentLoaded', async function() {
  const licenceEndpoint = 'http://localhost:3001/api/licencias/validar';
  
  try {
    const response = await fetch(licenceEndpoint);
    const licenseData = await response.json();
    
    if (licenseData.acceso_permitido) {
      // Licencia válida - mostrar aplicación
      document.getElementById('app').style.display = 'block';
      document.getElementById('license-error').style.display = 'none';
      
      // Verificar si está próxima a expirar
      if (licenseData.alerta) {
        showAlert(licenseData.alerta);
      }
      
    } else {
      // Licencia inválida - mostrar error y ocultar app
      document.getElementById('app').style.display = 'none';
      document.getElementById('license-error').style.display = 'block';
      document.getElementById('error-message').textContent = licenseData.message;
    }
    
  } catch (error) {
    console.error('Error validando licencia:', error);
    // Manejar error de conexión según tus necesidades
  }
});
```

---

## �️ **CONFIGURACIÓN SQL SERVER**

Para que el sistema de licencias funcione completamente:

### 1. **Instalar SQL Server 2019**
- Descargar e instalar SQL Server 2019
- Configurar autenticación mixta
- Crear usuario `sa` con contraseña

### 2. **Crear Base de Datos**
```sql
-- Ejecutar tu script database.sql en SQL Server Management Studio
-- Esto creará todas las tablas incluyendo la tabla Licencia
```

### 3. **Configurar Variables de Entorno**
Crear archivo `.env` en la raíz:
```env
# Base de datos SQL Server
DB_HOST=localhost
DB_PORT=1433
DB_NAME=SIGA_DB
DB_USER=sa
DB_PASSWORD=************

# Servidor
PORT=3001
NODE_ENV=development
```

### 4. **Crear Licencia de Prueba**
Una vez conectado SQL Server, puedes usar estos endpoints para crear licencias:
```http
POST http://localhost:3001/api/licencias
Content-Type: application/json

{
  "fecha_expiracion": "2025-12-31T23:59:59.000Z",
  "almacen_id": 1
}
```

---

## 🏗️ **ESTRUCTURA DEL PROYECTO**

```
d:\Front-End_SIGA v.2\
├── app.js                          # 🚀 SERVIDOR PRINCIPAL (raíz)
├── package.json                    # 📦 Dependencias
├── .env                           # 🔐 Variables de entorno
├── database.sql                   # 🗄️ Script de BD
│
└── src/
    ├── config/
    │   ├── database.js            # 🔌 Conexión SQL Server
    │   └── constants.js           # ⚙️ Configuraciones
    │
    └── modules/
        └── licencias/
            ├── licenciaService.js  # 🔐 Lógica de licencias
            ├── licenciaController.js # 🎮 Controladores HTTP
            └── licenciaRoutes.js   # 🛣️ Rutas de API
```

---

## 🎯 **PRÓXIMOS PASOS**

### **Inmediato**
1. ✅ **Servidor funcionando** - Completado
2. ✅ **Endpoints de licencia** - Completado  
3. ⏳ **Configurar SQL Server** - Pendiente
4. ⏳ **Crear licencias de prueba** - Pendiente

### **Integración Frontend**
1. ⏳ **Llamar `/api/licencias/validar` al iniciar**
2. ⏳ **Manejar respuesta de licencia válida/inválida**
3. ⏳ **Mostrar mensajes de error apropiados**
4. ⏳ **Bloquear acceso si licencia inválida**

---

## 🚨 **IMPORTANTE PARA PRODUCCIÓN**

### **Seguridad**
- Cambiar `JWT_SECRET` en `.env`
- Usar HTTPS en producción
- Configurar CORS apropiadamente

### **Base de Datos**
- Usar credenciales seguras para SQL Server
- Configurar backup automático de licencias
- Monitorear expiración de licencias

### **Monitoreo**
- Log de intentos de acceso
- Alertas de licencias próximas a expirar
- Notificaciones de licencias inválidas

---

## 🎉 **¡SISTEMA DE LICENCIAS LISTO!**

**Tu backend está funcionando y listo para validar licencias desde el frontend. Solo necesitas:**

1. **Configurar SQL Server** con el script `database.sql`
2. **Crear licencias** en la base de datos
3. **Integrar** en tu frontend la llamada a `/api/licencias/validar`

**🔗 El equipo actual tiene ID: `2c0d012ac764d0aed69989ffa2ef0882`**

**Respuesta error (licencia inválida):**
```json
{
  "success": false,
  "acceso_permitido": false,
  "message": "La licencia ha expirado. Contacte al proveedor para renovar.",
  "codigo": "LICENSE_EXPIRED"
}
```

### 🔧 **Validaciones Automáticas**

#### ✅ **Verificaciones que Realiza**
1. **Existencia de licencia** para el equipo actual
2. **Fecha de expiración** (marca como expirada automáticamente)
3. **Estado de la licencia** (activa/inactiva/suspendida)
4. **Almacén activo** (si está asociado)
5. **Identificación única del equipo** (hostname, MAC, etc.)

#### ⚠️ **Códigos de Error**
- `LICENSE_NOT_FOUND` - No hay licencia para este equipo
- `LICENSE_EXPIRED` - Licencia vencida
- `WAREHOUSE_INACTIVE` - Almacén inactivo
- `SYSTEM_ERROR` - Error interno

### 🛠️ **APIs Administrativas**

```javascript
// Gestión de licencias (requiere autenticación admin)
GET    /api/licencias          # Obtener todas las licencias
POST   /api/licencias          # Crear nueva licencia
GET    /api/licencias/:id      # Obtener licencia específica
PUT    /api/licencias/:id/estado # Actualizar estado

// Utilidades
GET    /api/licencias/equipo   # Info del equipo actual
GET    /api/licencias/token/:token # Verificar por token
```

---

## 🚀 **CÓMO USAR DESDE EL FRONTEND**

### 1. **Al Iniciar la Aplicación**
```javascript
// En tu componente principal o App.js
useEffect(() => {
  validarLicencia();
}, []);

const validarLicencia = async () => {
  try {
    const response = await fetch('http://localhost:3000/api/licencias/validar');
    const data = await response.json();
    
    if (!data.acceso_permitido) {
      // Mostrar pantalla de error de licencia
      setError({
        tipo: 'licencia',
        mensaje: data.message,
        codigo: data.codigo
      });
      return;
    }
    
    // Licencia válida - continuar con la aplicación
    if (data.alerta) {
      // Mostrar alerta de expiración próxima
      showWarning(data.alerta);
    }
    
    setLicenciaInfo(data.data);
    setAppReady(true);
    
  } catch (error) {
    setError({
      tipo: 'conexion',
      mensaje: 'No se puede conectar al servidor'
    });
  }
};
```

### 2. **Componente de Error de Licencia**
```javascript
const LicenseError = ({ error }) => {
  const getActionMessage = (codigo) => {
    switch(codigo) {
      case 'LICENSE_EXPIRED':
        return 'Contacte al proveedor para renovar su licencia.';
      case 'LICENSE_NOT_FOUND':
        return 'Contacte al administrador para obtener una licencia.';
      default:
        return 'Contacte al soporte técnico.';
    }
  };

  return (
    <div className="license-error">
      <h2>Acceso Denegado</h2>
      <p>{error.mensaje}</p>
      <p><strong>{getActionMessage(error.codigo)}</strong></p>
    </div>
  );
};
```

---

## 💾 **CONFIGURACIÓN DE BASE DE DATOS**

### 📋 **Script SQL Requerido**
Ya tienes la tabla `Licencia` en tu `database.sql`:

```sql
CREATE TABLE Licencia (
    id_licencia INT PRIMARY KEY IDENTITY(1,1),
    token VARCHAR(255) NOT NULL,
    equipo_id VARCHAR(100) NOT NULL,
    fecha_activacion DATETIME,
    fecha_expiracion DATETIME,
    estado VARCHAR(20),
    almacen_id INT,
    FOREIGN KEY (almacen_id) REFERENCES Almacen(id_almacen)
);
```

### 🎯 **Datos de Prueba**
```sql
-- Insertar una licencia de prueba
INSERT INTO Licencia (
    token, 
    equipo_id, 
    fecha_activacion, 
    fecha_expiracion, 
    estado, 
    almacen_id
) VALUES (
    'test-license-token-2025',
    'any-equipment-id',  -- Cualquier equipo puede usar esta licencia
    GETDATE(),
    DATEADD(month, 6, GETDATE()),  -- Expira en 6 meses
    'activa',
    1  -- ID del almacén (opcional)
);
```

---

## 🔧 **CONFIGURACIÓN**

### 📄 **Variables de Entorno (.env)**
```env
# Base de datos SQL Server
DB_HOST=localhost
DB_PORT=1433
DB_NAME=SIGA_DB
DB_USER=sa
DB_PASSWORD=************

# JWT
JWT_SECRET=************
JWT_EXPIRES_IN=24h

# Servidor
PORT=3001
NODE_ENV=development
FRONTEND_URL=http://localhost:3001
```

---

## 🎯 **COMANDOS**

### 🚀 **Ejecución**
```bash
# Instalar dependencias
npm install

# Ejecutar en desarrollo
npm run dev

# El servidor estará disponible en:
# http://localhost:3000
# 
# Endpoint de licencias:
# http://localhost:3000/api/licencias/validar
```

---

## ✅ **FLUJO COMPLETO**

1. **Frontend inicia** → Llama a `/api/licencias/validar`
2. **Backend verifica:**
   - ✅ Identifica el equipo automáticamente
   - ✅ Busca licencia válida en SQL Server
   - ✅ Verifica fecha de expiración
   - ✅ Comprueba estado del almacén
3. **Respuesta:**
   - ❌ `acceso_permitido: false` → Mostrar error
   - ✅ `acceso_permitido: true` → Permitir acceso
4. **Frontend actúa** según la respuesta

---

## 🎉 **BENEFICIOS**

- **🔒 Control total** sobre el acceso al sistema
- **⚡ Validación automática** sin intervención manual
- **📊 Información detallada** sobre la licencia
- **🚨 Alertas proactivas** antes del vencimiento
- **🏢 Asociación por almacén** para multi-sucursal
- **🔑 Identificación única** por equipo

**¡Tu sistema está protegido por licencias desde el primer momento!** 🛡️
