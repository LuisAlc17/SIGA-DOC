# API de Menús y Permisos de Usuario

Esta documentación describe las APIs para manejar módulos del sistema, submódulos y permisos de usuario para el control de acceso basado en roles.

## Estructura de Datos

### Módulo Sistema
```sql
- id_modulo: INT (PK)
- codigo_modulo: VARCHAR(20) UNIQUE
- nombre: VARCHAR(100)
- descripcion: VARCHAR(255)
- ruta_sistema: VARCHAR(100)
- icono: VARCHAR(50)
- orden_menu: INT
- activo: BIT
- fecha_creacion: DATETIME
```

### Submódulo Sistema
```sql
- id_submodulo: INT (PK)
- modulo_id: INT (FK)
- codigo_submodulo: VARCHAR(20) UNIQUE
- nombre: VARCHAR(100)
- descripcion: VARCHAR(255)
- ruta_sistema: VARCHAR(100)
- icono: VARCHAR(50)
- orden_menu: INT
- activo: BIT
- fecha_creacion: DATETIME
```

### Permiso Usuario
```sql
- id_permiso: INT (PK)
- usuario_id: INT (FK)
- sub_modulo_id: INT (FK)
- almacen_id: INT (FK)
- fecha_asignacion: DATETIME
```

## Endpoints

### 1. Obtener Menú del Usuario

**GET** `/api/menu/usuario/:usuarioId`

Obtiene el menú estructurado de un usuario con todos los módulos y submódulos a los que tiene acceso.

**Parámetros:**
- `usuarioId` (path): ID del usuario
- `almacenId` (query, opcional): ID del almacén para filtrar permisos

**Ejemplo de respuesta:**
```json
{
  "success": true,
  "message": "Menú obtenido exitosamente",
  "data": {
    "usuario_id": 1,
    "almacen_id": null,
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
            "codigo_submodulo": "USUARIOS",
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

### 2. Obtener Permisos de Usuario

**GET** `/api/permisos/usuario/:usuarioId`

Obtiene todos los permisos detallados de un usuario.

**Parámetros:**
- `usuarioId` (path): ID del usuario
- `almacenId` (query, opcional): ID del almacén para filtrar

**Ejemplo de respuesta:**
```json
{
  "success": true,
  "message": "Permisos obtenidos exitosamente",
  "data": {
    "usuario_id": 1,
    "total_permisos": 3,
    "permisos": [
      {
        "id_permiso": 1,
        "usuario_id": 1,
        "sub_modulo_id": 1,
        "almacen_id": 1,
        "fecha_asignacion": "2024-01-15T10:30:00.000Z",
        "codigo_submodulo": "USUARIOS",
        "nombre_submodulo": "Gestión de Usuarios",
        "ruta_sistema": "/admin/usuarios",
        "codigo_modulo": "ADMIN",
        "nombre_modulo": "Administración",
        "nombre_almacen": "Almacén Central"
      }
    ]
  }
}
```

### 3. Asignar Permiso a Usuario

**POST** `/api/permisos/asignar`

Asigna un permiso específico a un usuario para un submódulo en un almacén.

**Body:**
```json
{
  "usuario_id": 1,
  "sub_modulo_id": 1,
  "almacen_id": 1
}
```

**Ejemplo de respuesta:**
```json
{
  "success": true,
  "message": "Permiso asignado exitosamente",
  "data": {
    "id_permiso": 1,
    "usuario_id": 1,
    "sub_modulo_id": 1,
    "almacen_id": 1,
    "fecha_asignacion": "2024-01-15T10:30:00.000Z"
  }
}
```

### 4. Asignar Múltiples Permisos

**POST** `/api/permisos/asignar-multiples`

Asigna múltiples permisos a un usuario de una vez.

**Body:**
```json
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
    },
    {
      "sub_modulo_id": 3,
      "almacen_id": 2
    }
  ]
}
```

**Ejemplo de respuesta:**
```json
{
  "success": true,
  "message": "3 permisos asignados exitosamente",
  "data": {
    "permisosCreados": 3,
    "permisos": [
      {
        "id_permiso": 1,
        "usuario_id": 1,
        "sub_modulo_id": 1,
        "almacen_id": 1,
        "fecha_asignacion": "2024-01-15T10:30:00.000Z"
      }
    ]
  }
}
```

### 5. Revocar Permiso

**DELETE** `/api/permisos/:permisoId`

Revoca un permiso específico.

**Parámetros:**
- `permisoId` (path): ID del permiso a revocar

**Ejemplo de respuesta:**
```json
{
  "success": true,
  "message": "Permiso revocado exitosamente"
}
```

### 6. Verificar Permiso

**GET** `/api/permisos/verificar/:usuarioId/:submoduloId/:almacenId`

Verifica si un usuario tiene un permiso específico.

**Parámetros:**
- `usuarioId` (path): ID del usuario
- `submoduloId` (path): ID del submódulo
- `almacenId` (path): ID del almacén

**Ejemplo de respuesta:**
```json
{
  "success": true,
  "message": "Verificación completada",
  "data": {
    "usuario_id": 1,
    "sub_modulo_id": 1,
    "almacen_id": 1,
    "tiene_permiso": true
  }
}
```

### 7. Obtener Módulos

**GET** `/api/modulos`

Obtiene todos los módulos activos del sistema.

**Ejemplo de respuesta:**
```json
{
  "success": true,
  "message": "Módulos obtenidos exitosamente",
  "data": {
    "total": 3,
    "modulos": [
      {
        "id_modulo": 1,
        "codigo_modulo": "ADMIN",
        "nombre": "Administración",
        "descripcion": "Módulo de administración del sistema",
        "ruta_sistema": "/admin",
        "icono": "admin-icon",
        "orden_menu": 1,
        "activo": true,
        "fecha_creacion": "2024-01-01T00:00:00.000Z"
      }
    ]
  }
}
```

### 8. Obtener Submódulos

**GET** `/api/submodulos`

Obtiene todos los submódulos activos del sistema.

**Parámetros:**
- `moduloId` (query, opcional): ID del módulo para filtrar submódulos

**Ejemplo de respuesta:**
```json
{
  "success": true,
  "message": "Submódulos obtenidos exitosamente",
  "data": {
    "total": 5,
    "modulo_id": null,
    "submodulos": [
      {
        "id_submodulo": 1,
        "modulo_id": 1,
        "codigo_submodulo": "USUARIOS",
        "nombre": "Gestión de Usuarios",
        "descripcion": "Administrar usuarios del sistema",
        "ruta_sistema": "/admin/usuarios",
        "icono": "users-icon",
        "orden_menu": 1,
        "activo": true,
        "fecha_creacion": "2024-01-01T00:00:00.000Z",
        "nombre_modulo": "Administración",
        "codigo_modulo": "ADMIN"
      }
    ]
  }
}
```

## Uso en Frontend

### Ejemplo de implementación en React/TypeScript:

```typescript
// Servicio para obtener menú del usuario
export const getMenuUsuario = async (usuarioId: number, almacenId?: number) => {
  const params = almacenId ? `?almacenId=${almacenId}` : '';
  const response = await fetch(`/api/menu/usuario/${usuarioId}${params}`);
  return response.json();
};

// Hook para cargar menú
export const useMenuUsuario = (usuarioId: number, almacenId?: number) => {
  const [menu, setMenu] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    if (usuarioId) {
      getMenuUsuario(usuarioId, almacenId)
        .then(response => {
          if (response.success) {
            setMenu(response.data.menu);
          }
        })
        .finally(() => setLoading(false));
    }
  }, [usuarioId, almacenId]);

  return { menu, loading };
};

// Componente de menú dinámico
const DynamicMenu = ({ usuarioId, almacenId }) => {
  const { menu, loading } = useMenuUsuario(usuarioId, almacenId);

  if (loading) return <div>Cargando menú...</div>;

  return (
    <nav>
      {menu.map(modulo => (
        <div key={modulo.id_modulo} className="menu-module">
          <h3>{modulo.nombre_modulo}</h3>
          <ul>
            {modulo.submodulos.map(submodulo => (
              <li key={submodulo.id_submodulo}>
                <Link to={submodulo.ruta_sistema}>
                  <i className={submodulo.icono_submodulo}></i>
                  {submodulo.nombre_submodulo}
                </Link>
              </li>
            ))}
          </ul>
        </div>
      ))}
    </nav>
  );
};
```

## Códigos de Error

- **400**: Datos de entrada inválidos
- **404**: Recurso no encontrado
- **409**: Conflicto (permiso ya existe)
- **500**: Error interno del servidor

## Notas Importantes

1. **Seguridad**: Siempre verificar permisos en el backend antes de realizar operaciones
2. **Performance**: Usar caché para menús que no cambian frecuentemente
3. **Validación**: Validar IDs de usuario, módulo y almacén antes de asignar permisos
4. **Auditoría**: Todas las operaciones de permisos se registran con fecha de asignación
