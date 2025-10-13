# Diagrama de Flujo - Inicio de Sesión SIGA

## 📋 Flujo Principal de Autenticación

```mermaid
flowchart TD
    A[🚀 Inicio de Aplicación] --> B{🔑 Validar Licencias}
    
    B -->|❌ Licencia Inválida| C[⚠️ Error de Licencia<br/>- Mostrar mensaje<br/>- Cerrar aplicación]
    B -->|✅ Licencia Válida| D[🔐 Mostrar Login]
    
    D --> E[📝 Ingreso de Credenciales<br/>- Email como usuario<br/>- Contraseña]
    
    E --> F{🔍 Validar Credenciales}
    
    F -->|❌ Inválidas| G[⚠️ Error de Login<br/>- Mostrar mensaje<br/>- Incrementar intentos]
    G --> H{🚫 ¿Muchos intentos?}
    H -->|Sí| I[🔒 Bloquear Usuario<br/>- Notificar admin<br/>- Registrar evento]
    H -->|No| D
    I --> D
    
    F -->|✅ Válidas| J{🔄 ¿Primer Login?}
    
    J -->|Sí| K[🔑 Cambiar Contraseña<br/>- Forzar nueva contraseña<br/>- Actualizar BD]
    K --> L[✅ Login Exitoso]
    J -->|No| L
    
    L --> M{👤 Tipo de Usuario}
    
    M -->|🔧 Administrador| N[👑 Dashboard Admin<br/>- Todos los módulos<br/>- Gestión usuarios<br/>- Configuraciones<br/>- Sin licencia requerida]
    
    M -->|⚙️ Operador Módulo| O[🎯 Dashboard Operador<br/>- Módulos asignados<br/>- Permisos de edición<br/>- Reportes básicos]
    
    M -->|📊 Auditor| P[📈 Dashboard Auditor<br/>- Solo lectura<br/>- Todos los reportes<br/>- Descarga de datos<br/>- Consultas avanzadas]
    
    M -->|🏭 Usuario Operativo| Q[🔧 Dashboard Operaciones<br/>- Solo módulo operaciones<br/>- Procesos asignados<br/>- Tareas específicas]
    
    N --> R[🎯 Sistema Principal]
    O --> R
    P --> R
    Q --> R
    
    R --> S[📱 Gestión de Sesión<br/>- Log de actividad<br/>- Control de tiempo<br/>- Alertas automáticas]
```

## 🔐 Flujo de Validación de Licencias

```mermaid
flowchart TD
    A[🔍 Validación de Licencias] --> B{📋 ¿Licencia en BD?}
    
    B -->|❌ No existe| C[⚠️ Error Fatal<br/>- Sistema bloqueado<br/>- Contactar soporte]
    
    B -->|✅ Existe| D{📅 ¿Vigente?}
    
    D -->|❌ Expirada| E[⏰ Licencia Expirada<br/>- Mostrar mensaje<br/>- Bloquear acceso]
    
    D -->|✅ Vigente| F{👥 ¿Usuarios disponibles?}
    
    F -->|❌ Cupo lleno| G[👥 Límite de Usuarios<br/>- Mostrar mensaje<br/>- Contactar admin]
    
    F -->|✅ Disponible| H[✅ Continuar a Login]
    
    E --> I[📧 Notificar Admin]
    G --> I
    I --> C
```

## 👤 Flujo de Tipos de Usuario

```mermaid
flowchart TD
    A[👤 Usuario Autenticado] --> B{🎭 Tipo de Usuario}
    
    B -->|👑| C[ADMINISTRADOR<br/>📌 Sin licencia requerida<br/>📌 Acceso desde cualquier dispositivo]
    C --> C1[✅ Todos los módulos]
    C --> C2[✅ Gestión de usuarios]
    C --> C3[✅ Configuraciones]
    C --> C4[✅ Administración licencias]
    C --> C5[✅ Reportes avanzados]
    
    B -->|⚙️| D[OPERADOR MÓDULO<br/>📌 Licencia requerida<br/>📌 Dispositivo específico]
    D --> D1[✅ Módulos asignados]
    D --> D2[✅ Edición permitida]
    D --> D3[✅ Reportes básicos]
    D --> D4[❌ Sin configuraciones]
    
    B -->|📊| E[AUDITOR<br/>📌 Licencia requerida<br/>📌 Solo lectura]
    E --> E1[✅ Consultas avanzadas]
    E --> E2[✅ Todos los reportes]
    E --> E3[✅ Descarga de datos]
    E --> E4[❌ Sin edición]
    
    B -->|🏭| F[USUARIO OPERATIVO<br/>📌 Solo módulo operaciones<br/>📌 Procesos específicos]
    F --> F1[✅ Módulo operaciones únicamente]
    F --> F2[✅ Procesos asignados]
    F --> F3[✅ Tareas específicas]
    F --> F4[❌ Sin otros módulos]
```

## 🔄 Flujo de Cambio de Contraseña

```mermaid
flowchart TD
    A[🔑 Cambio de Contraseña] --> B{📍 Trigger}
    
    B -->|🆕 Primer login| C[🔒 Cambio Obligatorio]
    B -->|🔗 Link recuperación| D[📧 Cambio por Email]
    B -->|👤 Usuario voluntario| E[🔄 Cambio Voluntario]
    
    C --> F[📝 Formulario Cambio<br/>- Contraseña actual<br/>- Nueva contraseña<br/>- Confirmar contraseña]
    D --> G[📝 Formulario Recuperación<br/>- Token válido<br/>- Nueva contraseña<br/>- Confirmar contraseña]
    E --> F
    
    F --> H{✅ ¿Válida nueva contraseña?}
    G --> H
    
    H -->|❌ No válida| I[⚠️ Error Validación<br/>- Mostrar mensaje<br/>- Volver a formulario]
    I --> F
    I --> G
    
    H -->|✅ Válida| J[💾 Actualizar BD<br/>- Hash nueva contraseña<br/>- Actualizar flag cambio<br/>- Log de evento]
    
    J --> K[✅ Éxito<br/>- Confirmar cambio<br/>- Continuar al sistema]
```

## 📧 Flujo de Notificaciones

```mermaid
flowchart TD
    A[📧 Sistema de Notificaciones] --> B[📋 Eventos del Sistema]
    
    B --> C[🔐 Login exitoso]
    B --> D[❌ Login fallido]
    B --> E[🔑 Cambio contraseña]
    B --> F[👤 Usuario bloqueado]
    B --> G[📦 Movimiento inventario]
    B --> H[⚠️ Alerta crítica]
    
    C --> I{👥 ¿Quién debe ser notificado?}
    D --> I
    E --> I
    F --> I
    G --> I
    H --> I
    
    I --> J[👑 Administradores<br/>- Todos los eventos<br/>- Alertas críticas]
    I --> K[⚙️ Operadores<br/>- Eventos de sus módulos<br/>- Alertas específicas]
    I --> L[📊 Auditores<br/>- Eventos de consulta<br/>- Reportes programados]
    I --> M[🏭 Operativos<br/>- Eventos de procesos<br/>- Tareas asignadas]
    
    J --> N[📨 Envío de Notificación<br/>- Email usuario<br/>- Emails adicionales<br/>- Notificación in-app]
    K --> N
    L --> N
    M --> N
    
    N --> O[📝 Log de Notificación<br/>- Registrar envío<br/>- Estado de entrega<br/>- Timestamp]
```

## 🔍 Estados y Validaciones

### 📊 Estados de Usuario
- ✅ **Activo**: Usuario operativo normal
- ⏸️ **Inactivo**: Usuario deshabilitado temporalmente
- 🔒 **Bloqueado**: Usuario bloqueado por intentos fallidos
- 🔑 **Primer Login**: Debe cambiar contraseña
- 📅 **Expirado**: Usuario con acceso vencido

### 🛡️ Validaciones de Seguridad
- 🔐 **Contraseña**: Mínimo 8 caracteres, mayúsculas, números
- 🚫 **Intentos**: Máximo 3 intentos fallidos
- ⏰ **Sesión**: Timeout configurable por tipo de usuario
- 📱 **Dispositivo**: Control por licencia (excepto admin)

### 📋 Tipos de Licencia
- 🏢 **Enterprise**: Usuarios ilimitados, todos los módulos
- 💼 **Professional**: Hasta 50 usuarios, módulos específicos
- 🏪 **Basic**: Hasta 10 usuarios, funciones básicas
