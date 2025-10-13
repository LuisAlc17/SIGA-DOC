# 📊 DOCUMENTACIÓN DE APIS DASHBOARD - SIGA v.3

## 🎯 ARQUITECTURA DE DASHBOARDS

El sistema SIGA v.3 implementa una **arquitectura de dashboards multinivel**:

1. **Dashboard General** (`/api/dashboard`) - Información consolidada de todo el sistema
2. **Dashboards por Módulo** (`/api/[modulo]/dashboard`) - Información específica de cada módulo

---

## 📋 DASHBOARD GENERAL

### `GET /api/dashboard`
**Descripción**: Dashboard principal con información consolidada de todos los módulos

**Respuesta**:
```json
{
  "success": true,
  "data": {
    "resumen_sistema": {
      "usuarios": {
        "total_usuarios": 7,
        "usuarios_activos": 7
      },
      "productos": {
        "total_productos": 25,
        "productos_activos": 25
      },
      "ubicaciones": {
        "total_ubicaciones": 155,
        "ubicaciones_activas": 155
      },
      "almacenes": {
        "total_almacenes": 1,
        "almacenes_activos": 1
      }
    },
    "modulos": {
      "logistica": {
        "productos_logistica": 25,
        "ubicaciones_logistica": 20,
        "modelos_activos": 21,
        "estado": "activo"
      },
      "usuarios": {
        "total_usuarios": 7,
        "usuarios_activos": 7,
        "usuarios_activos_mes": 0,
        "estado": "activo"
      },
      "licencias": {
        "total_licencias": 0,
        "licencias_activas": 0,
        "licencias_vencidas": 0,
        "estado": "no_disponible"
      }
    },
    "alertas": [
      {
        "tipo": "info",
        "modulo": "logistica",
        "mensaje": "Producto PROD-119 en ubicación UBI-005-03",
        "fecha": "2025-07-27T05:30:31.215Z"
      }
    ],
    "actividad_reciente": [
      {
        "tipo": "producto_ingresado",
        "descripcion": "Producto PROD-119 (Laptop Empresarial HP ProBook 450) ingresado",
        "fecha": "2025-07-26T13:43:08.270Z",
        "modulo": "logistica"
      }
    ]
  },
  "timestamp": "2025-07-27T05:30:31.221Z",
  "version": "SIGA v.3"
}
```

### `GET /api/dashboard/info`
**Descripción**: Información básica del sistema

**Respuesta**:
```json
{
  "success": true,
  "data": {
    "nombre": "SIGA",
    "version": "v.3",
    "descripcion": "Sistema Integral de Gestión de Activos",
    "servidor": "Activo",
    "timestamp": "2025-07-27T05:30:31.221Z"
  }
}
```

### `GET /api/dashboard/performance`
**Descripción**: Estadísticas de rendimiento del sistema

**Respuesta**:
```json
{
  "success": true,
  "data": {
    "base_datos": {
      "tablas_totales": 65,
      "conexiones_activas": 1,
      "ultima_actualizacion": "2025-07-27T05:30:31.221Z"
    },
    "servidor": {
      "memoria_uso": {
        "rss": 123456789,
        "heapTotal": 98765432,
        "heapUsed": 87654321,
        "external": 12345678
      },
      "tiempo_actividad": 3600,
      "version_node": "v22.3.0"
    },
    "apis": {
      "endpoints_activos": 12,
      "respuesta_promedio": "< 500ms",
      "disponibilidad": "99.9%"
    }
  },
  "timestamp": "2025-07-27T05:30:31.221Z"
}
```

---

## 🔧 DASHBOARD LOGÍSTICA

### `GET /api/logistica/dashboard`
**Descripción**: Dashboard específico del módulo de logística con información detallada

**Respuesta**:
```json
{
  "success": true,
  "data": {
    "resumen": {
      "total_productos": 25,
      "productos_activos": 25,
      "total_ubicaciones": 155,
      "ubicaciones_activas": 155,
      "total_modelos": 21,
      "total_almacenes": 1
    },
    "distribuciones": {
      "por_categoria": [
        {
          "categoria": "Computadoras",
          "cantidad": 8,
          "valor_promedio": 15000.50
        },
        {
          "categoria": "Herramientas",
          "cantidad": 5,
          "valor_promedio": 2500.00
        }
      ],
      "por_almacen": [
        {
          "almacen": "Almacén Principal",
          "productos": 25,
          "ubicaciones_ocupadas": 20
        }
      ],
      "por_estado": [
        {
          "estado": "Ingresado",
          "cantidad": 20,
          "porcentaje": 80.00
        },
        {
          "estado": "Disponible",
          "cantidad": 5,
          "porcentaje": 20.00
        }
      ]
    },
    "movimientos": {
      "recientes": [
        {
          "codigo_producto": "PROD-119",
          "numero_serie": "001-2025-119",
          "modelo": "Laptop Empresarial HP ProBook 450",
          "almacen_actual": "Almacén Principal",
          "ubicacion_codigo": "UBI-005-03",
          "fecha_ingreso_sistema": "2025-07-26T13:43:08.270Z",
          "estado": "Ingresado"
        }
      ]
    },
    "alertas": [
      {
        "tipo": "info",
        "titulo": "Productos de alto valor",
        "mensaje": "17 productos tienen valor superior a $10,000",
        "prioridad": "baja"
      }
    ],
    "kpis": {
      "total_items": 25,
      "valor_total_inventario": 504274,
      "valor_promedio_item": 20170.96,
      "ubicaciones_en_uso": 20,
      "porcentaje_ocupacion": 100.00
    }
  },
  "modulo": "logistica",
  "timestamp": "2025-07-27T05:30:41.391Z"
}
```

---

## 🎯 CASOS DE USO

### 1. **Frontend - Página Principal**
```javascript
// Obtener dashboard general para página de inicio
const dashboardData = await fetch('/api/dashboard').then(r => r.json());

// Mostrar resumen de todo el sistema
displaySystemSummary(dashboardData.data.resumen_sistema);
displayModuleStatus(dashboardData.data.modulos);
displayAlerts(dashboardData.data.alertas);
```

### 2. **Frontend - Módulo Logística**
```javascript
// Obtener dashboard específico de logística
const logisticaDashboard = await fetch('/api/logistica/dashboard').then(r => r.json());

// Mostrar KPIs específicos
displayKPIs(logisticaDashboard.data.kpis);
displayDistributions(logisticaDashboard.data.distribuciones);
displayRecentMovements(logisticaDashboard.data.movimientos);
```

### 3. **Monitoreo del Sistema**
```javascript
// Verificar estado y rendimiento
const systemInfo = await fetch('/api/dashboard/info').then(r => r.json());
const performance = await fetch('/api/dashboard/performance').then(r => r.json());

// Alertas de sistema
if (performance.data.servidor.memoria_uso.heapUsed > 100000000) {
  alert('Alto uso de memoria');
}
```

---

## 🚀 BENEFICIOS

### ✅ **Dashboards Implementados**:
1. **Información Centralizada**: Un solo endpoint para resumen general
2. **Modularidad**: Dashboards específicos por módulo
3. **Tiempo Real**: Datos actualizados con timestamp
4. **Escalabilidad**: Fácil agregar nuevos módulos
5. **Performance**: Consultas optimizadas y agrupadas

### 🔄 **Dashboards Pendientes**:
- Dashboard de Usuarios (`/api/usuarios/dashboard`)
- Dashboard de Licencias (`/api/licencias/dashboard`)
- Dashboard de Inventario (`/api/inventario/dashboard`)

---

## 📊 ESTRUCTURA DE ARCHIVOS

```
src/
├── controllers/
│   └── dashboardController.js          # Controlador general
├── routes/
│   └── dashboard.js                    # Rutas generales
└── modules/
    └── logistica/
        ├── logisticaController.js      # Método dashboard específico
        └── routes/
            └── logisticaRoutes.js      # Ruta /dashboard
```

---

## 🎉 ESTADO ACTUAL: ✅ COMPLETADO

- ✅ Dashboard General funcionando
- ✅ Dashboard Logística funcionando  
- ✅ Información del sistema funcionando
- ✅ APIs de performance funcionando
- ✅ Estructura escalable implementada
- ✅ Documentación completa

**Próximo paso**: Implementar dashboards para otros módulos siguiendo el mismo patrón.
