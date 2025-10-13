# Test de APIs de Menú y Permisos

Este archivo contiene ejemplos de cómo probar las nuevas APIs de menú y permisos integradas.

## URLs Base
```
Backend: http://localhost:3000
Health Check: http://localhost:3000/health
```

## Headers Requeridos
```json
{
  "Content-Type": "application/json",
  "X-App-Code": "YOUR_APP_CODE_HERE",
  "Authorization": "Bearer YOUR_JWT_TOKEN"
}
```

## Endpoints Disponibles

### 1. Health Check (Sin autenticación)
```bash
GET http://localhost:3000/health
```

### 2. Obtener Menú del Usuario
```bash
GET http://localhost:3000/api/menu/usuario/1
GET http://localhost:3000/api/menu/usuario/1?almacenId=1
```

### 3. Obtener Permisos Detallados
```bash
GET http://localhost:3000/api/menu/permisos/usuario/1
GET http://localhost:3000/api/menu/permisos/usuario/1?almacenId=1
```

### 4. Asignar Permiso Individual
```bash
POST http://localhost:3000/api/menu/permisos/asignar
Content-Type: application/json

{
  "usuario_id": 1,
  "sub_modulo_id": 1,
  "almacen_id": 1
}
```

### 5. Asignar Múltiples Permisos
```bash
POST http://localhost:3000/api/menu/permisos/asignar-multiples
Content-Type: application/json

{
  "usuario_id": 1,
  "permisos": [
    {
      "sub_modulo_id": 1,
      "almacen_id": 1
    },
    {
      "sub_modulo_id": 2,
      "almacen_id": 1
    }
  ]
}
```

### 6. Verificar Permiso Específico
```bash
GET http://localhost:3000/api/menu/permisos/verificar/1/1/1
```

### 7. Revocar Permiso
```bash
DELETE http://localhost:3000/api/menu/permisos/1
```

### 8. Obtener Módulos del Sistema
```bash
GET http://localhost:3000/api/menu/modulos
```

### 9. Obtener Submódulos
```bash
GET http://localhost:3000/api/menu/submodulos
GET http://localhost:3000/api/menu/submodulos?moduloId=1
```

## Ejemplos con cURL

### Obtener menú del usuario
```bash
curl -X GET "http://localhost:3000/api/menu/usuario/1" \
  -H "Content-Type: application/json" \
  -H "X-App-Code: YOUR_APP_CODE" \
  -H "Authorization: Bearer YOUR_TOKEN"
```

### Asignar permiso
```bash
curl -X POST "http://localhost:3000/api/menu/permisos/asignar" \
  -H "Content-Type: application/json" \
  -H "X-App-Code: YOUR_APP_CODE" \
  -H "Authorization: Bearer YOUR_TOKEN" \
  -d '{
    "usuario_id": 1,
    "sub_modulo_id": 1,
    "almacen_id": 1
  }'
```

## Respuestas Esperadas

### Menú del Usuario (Exitoso)
```json
{
  "success": true,
  "message": "Menú obtenido exitosamente",
  "data": {
    "usuario_id": 1,
    "almacen_id": 1,
    "total_modulos": 2,
    "menu": [
      {
        "id_modulo": 1,
        "codigo_modulo": "ADMIN",
        "nombre_modulo": "Administración",
        "descripcion_modulo": "Módulo de administración del sistema",
        "icono_modulo": "admin-icon",
        "orden_modulo": 1,
        "submodulos": [
          {
            "id_submodulo": 1,
            "codigo_submodulo": "ADMIN_USUARIOS",
            "nombre_submodulo": "Gestión de Usuarios",
            "descripcion_submodulo": "Administrar usuarios del sistema",
            "ruta_sistema": "/admin/usuarios",
            "icono_submodulo": "users-icon",
            "orden_submodulo": 1,
            "almacen_id": 1,
            "nombre_almacen": "Almacén Central"
          }
        ]
      }
    ]
  }
}
```

### Error de Validación
```json
{
  "success": false,
  "message": "ID de usuario inválido"
}
```

### Error de Autenticación
```json
{
  "success": false,
  "message": "Código de aplicación requerido",
  "required_header": "X-App-Code"
}
```

## Notas Importantes

1. **Código de Aplicación**: Todas las rutas `/api/*` requieren el header `X-App-Code`
2. **Autenticación**: Las rutas de menú necesitan autenticación JWT válida
3. **Base de Datos**: Asegúrate de que las tablas estén creadas y pobladas con datos
4. **CORS**: El frontend debe estar en `http://localhost:5173` o `http://localhost:3001`

## Próximos Pasos

1. Iniciar el servidor backend: `npm run dev`
2. Poblar la base de datos con los módulos y permisos
3. Crear un usuario de prueba
4. Probar las APIs con Postman o Thunder Client
5. Integrar en el frontend React
