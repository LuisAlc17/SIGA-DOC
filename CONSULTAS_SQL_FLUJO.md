# 📋 Consultas SQL Esenciales para el Flujo SIGA

> **Consultas optimizadas para el sistema de almacenes**

## 🔍 **1. TRAZABILIDAD DE PRODUCTOS**

### Ubicación actual de un producto específico
```sql
SELECT 
    p.serie,
    p.codigo,
    pm.nombre_modelo,
    CASE 
        WHEN pp.ubicacion_proceso_id IS NOT NULL THEN 'En proceso: ' + pr.nombre
        WHEN dm.ubicacion_asignada_id IS NOT NULL THEN 'Almacenado'
        ELSE 'Sin ubicación'
    END as estado_ubicacion,
    u.codigo_ubicacion,
    pp.estado_proceso,
    pp.fecha_ingreso as fecha_proceso,
    e.nombre as entidad_propietaria
FROM Producto p
LEFT JOIN Producto_modelo pm ON p.modelo_id = pm.id_modelo
LEFT JOIN Detalle_Movimiento dm ON p.id_producto = dm.producto_id
LEFT JOIN Movimiento_Almacen ma ON dm.movimiento_id = ma.id_movimiento
LEFT JOIN Entidad e ON ma.entidad_id = e.id_entidad
LEFT JOIN Orden_Producto op ON dm.id_detalle = op.detalle_movimiento_id
LEFT JOIN Producto_Proceso pp ON op.id_orden_producto = pp.orden_producto_id
LEFT JOIN Proceso_Operacion pr ON pp.proceso_id = pr.id_proceso
LEFT JOIN Ubicacion u ON COALESCE(pp.ubicacion_proceso_id, dm.ubicacion_asignada_id) = u.id_ubicacion
WHERE p.serie = @serie OR p.id_producto = @id_producto;
```

### Historial completo de un producto
```sql
SELECT 
    h.fecha_movimiento,
    h.tipo_movimiento,
    uo.codigo_ubicacion as ubicacion_origen,
    ud.codigo_ubicacion as ubicacion_destino,
    h.motivo,
    usr.nombre as usuario_responsable
FROM Historial_Ubicacion_Producto h
LEFT JOIN Ubicacion uo ON h.ubicacion_origen_id = uo.id_ubicacion
LEFT JOIN Ubicacion ud ON h.ubicacion_destino_id = ud.id_ubicacion
LEFT JOIN Usuario usr ON h.usuario_id = usr.id_usuario
WHERE h.producto_id = @producto_id
ORDER BY h.fecha_movimiento DESC;
```

## 📦 **2. GESTIÓN DE MOVIMIENTOS**

### Productos pendientes de ubicar
```sql
SELECT 
    ma.numero_documento,
    p.serie,
    p.codigo,
    tp.nombre as tipo_producto,
    dm.estado_detalle,
    ma.fecha_movimiento,
    usr.nombre as operador_responsable
FROM Detalle_Movimiento dm
JOIN Movimiento_Almacen ma ON dm.movimiento_id = ma.id_movimiento
JOIN Producto p ON dm.producto_id = p.id_producto
JOIN Tipo_Producto tp ON dm.tipo_producto_id = tp.id_tipo_producto
JOIN Usuario usr ON ma.operador_responsable_id = usr.id_usuario
WHERE dm.estado_detalle = 'pendiente'
AND ma.almacen_id = @almacen_id
ORDER BY ma.fecha_movimiento;
```

### Resumen de movimientos por almacén
```sql
SELECT 
    ma.tipo_movimiento,
    COUNT(*) as total_movimientos,
    COUNT(DISTINCT dm.producto_id) as total_productos,
    e.nombre as entidad,
    ma.estado
FROM Movimiento_Almacen ma
LEFT JOIN Detalle_Movimiento dm ON ma.id_movimiento = dm.movimiento_id
LEFT JOIN Entidad e ON ma.entidad_id = e.id_entidad
WHERE ma.almacen_id = @almacen_id
AND ma.fecha_movimiento BETWEEN @fecha_inicio AND @fecha_fin
GROUP BY ma.tipo_movimiento, e.nombre, ma.estado;
```

## 🔧 **3. ÓRDENES DE TRABAJO Y OPERACIONES**

### Órdenes pendientes de aprobación
```sql
SELECT 
    ot.numero_orden,
    ot.tipo_orden,
    ot.cantidad_solicitada,
    ot.fecha_solicitud,
    usr_sol.nombre as solicitante,
    ot.descripcion,
    ot.estado
FROM Orden_Trabajo ot
JOIN Usuario usr_sol ON ot.solicitante_id = usr_sol.id_usuario
WHERE ot.estado = 'pendiente'
ORDER BY ot.fecha_solicitud;
```

### Productos asignados a una orden específica
```sql
SELECT 
    ot.numero_orden,
    p.serie,
    p.codigo,
    pm.nombre_modelo,
    op.estado_en_orden,
    op.fecha_asignacion,
    u.codigo_ubicacion as ubicacion_actual,
    pp.estado_proceso,
    pr.nombre as proceso_actual
FROM Orden_Trabajo ot
JOIN Orden_Producto op ON ot.id_orden = op.orden_id
JOIN Detalle_Movimiento dm ON op.detalle_movimiento_id = dm.id_detalle
JOIN Producto p ON dm.producto_id = p.id_producto
LEFT JOIN Producto_modelo pm ON p.modelo_id = pm.id_modelo
LEFT JOIN Ubicacion u ON op.ubicacion_proceso_id = u.id_ubicacion
LEFT JOIN Producto_Proceso pp ON op.id_orden_producto = pp.orden_producto_id
LEFT JOIN Proceso_Operacion pr ON pp.proceso_id = pr.id_proceso
WHERE ot.id_orden = @orden_id;
```

### Productos por proceso con fechas
```sql
SELECT 
    pr.nombre as proceso,
    p.serie,
    pp.estado_proceso,
    pp.fecha_ingreso,
    pp.fecha_salida,
    DATEDIFF(day, pp.fecha_ingreso, COALESCE(pp.fecha_salida, GETDATE())) as dias_en_proceso,
    usr.nombre as operador,
    pp.observaciones
FROM Producto_Proceso pp
JOIN Proceso_Operacion pr ON pp.proceso_id = pr.id_proceso
JOIN Orden_Producto op ON pp.orden_producto_id = op.id_orden_producto
JOIN Detalle_Movimiento dm ON op.detalle_movimiento_id = dm.id_detalle
JOIN Producto p ON dm.producto_id = p.id_producto
LEFT JOIN Usuario usr ON pp.operador_id = usr.id_usuario
WHERE pr.almacen_id = @almacen_id
AND pp.fecha_ingreso BETWEEN @fecha_inicio AND @fecha_fin
ORDER BY pp.fecha_ingreso DESC;
```

## 📊 **4. REPORTES Y DASHBOARDS**

### Ocupación de ubicaciones
```sql
SELECT 
    a.nombre as almacen,
    n.nombre as nave,
    r.nombre as rack,
    nv.nombre as nivel,
    c.nombre as columna,
    u.codigo_ubicacion,
    u.estado,
    u.capacidad_maxima,
    u.productos_actuales,
    CASE 
        WHEN u.capacidad_maxima > 0 THEN 
            CAST(u.productos_actuales * 100.0 / u.capacidad_maxima AS DECIMAL(5,2))
        ELSE 0 
    END as porcentaje_ocupacion
FROM Ubicacion u
JOIN Columna c ON u.columna_id = c.id_columna
JOIN Nivel nv ON c.nivel_id = nv.id_nivel
JOIN Rack r ON nv.rack_id = r.id_rack
JOIN Nave n ON r.nave_id = n.id_nave
JOIN Almacen a ON n.almacen_id = a.id_almacen
WHERE a.id_almacen = @almacen_id
ORDER BY porcentaje_ocupacion DESC;
```

### Productos defectuosos por proceso
```sql
SELECT 
    pr.nombre as proceso,
    COUNT(*) as total_defectuosos,
    STRING_AGG(pp.observaciones, '; ') as observaciones_comunes,
    usr.nombre as operador_frecuente
FROM Producto_Proceso pp
JOIN Proceso_Operacion pr ON pp.proceso_id = pr.id_proceso
LEFT JOIN Usuario usr ON pp.operador_id = usr.id_usuario
WHERE pp.estado_proceso = 'defectuoso'
AND pp.fecha_ingreso >= DATEADD(month, -1, GETDATE())
GROUP BY pr.nombre, usr.nombre
ORDER BY total_defectuosos DESC;
```

## 💰 **5. FACTURACIÓN**

### Movimientos pendientes de facturación
```sql
SELECT 
    ma.numero_documento,
    ma.tipo_movimiento,
    e.nombre as entidad,
    COUNT(dm.producto_id) as total_productos,
    ma.fecha_movimiento,
    usr.nombre as operador_responsable
FROM Movimiento_Almacen ma
JOIN Detalle_Movimiento dm ON ma.id_movimiento = dm.movimiento_id
JOIN Entidad e ON ma.entidad_id = e.id_entidad
JOIN Usuario usr ON ma.operador_responsable_id = usr.id_usuario
WHERE ma.requiere_facturacion = 1
AND ma.estado != 'facturado'
AND NOT EXISTS (
    SELECT 1 FROM Facturacion f WHERE f.movimiento_id = ma.id_movimiento
)
GROUP BY ma.numero_documento, ma.tipo_movimiento, e.nombre, ma.fecha_movimiento, usr.nombre
ORDER BY ma.fecha_movimiento;
```

### Estado de facturaciones
```sql
SELECT 
    f.numero_factura,
    ma.numero_documento,
    e.nombre as entidad,
    f.estado,
    f.monto_total,
    f.fecha_solicitud,
    f.fecha_aprobacion,
    f.fecha_cierre,
    usr.nombre as operador_facturacion
FROM Facturacion f
JOIN Movimiento_Almacen ma ON f.movimiento_id = ma.id_movimiento
JOIN Entidad e ON ma.entidad_id = e.id_entidad
JOIN Usuario usr ON f.operador_facturacion_id = usr.id_usuario
WHERE f.estado = @estado -- 'pendiente', 'aprobada', 'cerrada'
ORDER BY f.fecha_solicitud DESC;
```

## 🔔 **6. NOTIFICACIONES**

### Notificaciones pendientes por usuario
```sql
SELECT 
    n.tipo,
    n.titulo,
    n.mensaje,
    n.fecha_creacion,
    n.referencia_tipo,
    n.referencia_id
FROM Notificacion n
WHERE n.usuario_destino_id = @usuario_id
AND n.leido = 0
ORDER BY n.fecha_creacion DESC;
```

### Marcar notificaciones como leídas
```sql
UPDATE Notificacion 
SET leido = 1 
WHERE usuario_destino_id = @usuario_id 
AND id_notificacion IN (@lista_ids);
```

## 🎯 **7. CONSULTAS DE VALIDACIÓN**

### Verificar integridad de ubicaciones
```sql
SELECT 
    u.codigo_ubicacion,
    u.productos_actuales,
    COUNT(p.id_producto) as productos_reales,
    CASE 
        WHEN u.productos_actuales != COUNT(p.id_producto) THEN 'DESINCRONIZADO'
        ELSE 'OK'
    END as estado_integridad
FROM Ubicacion u
LEFT JOIN Producto p ON u.id_ubicacion = p.ubicacion_actual_id
GROUP BY u.id_ubicacion, u.codigo_ubicacion, u.productos_actuales
HAVING u.productos_actuales != COUNT(p.id_producto);
```

### Productos sin ubicación asignada
```sql
SELECT 
    p.serie,
    p.codigo,
    pm.nombre_modelo,
    p.estado_actual_proceso,
    a.nombre as almacen
FROM Producto p
LEFT JOIN Producto_modelo pm ON p.modelo_id = pm.id_modelo
JOIN Almacen a ON p.almacen_id = a.id_almacen
WHERE p.ubicacion_actual_id IS NULL
AND p.estado_actual_proceso != 'despachado';
```

---

**💡 Tip:** Usa parámetros (`@parametro`) en lugar de valores directos para evitar inyección SQL y mejorar el rendimiento con planes de ejecución cacheados.
