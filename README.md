 # Taller-SQL---An-lisis-Base-de-Datos-Tienda---Soluciones

1. Distribución de ventas por método de pago

   SELECT
   
       metodo_pago,
       COUNT(*) as numero_ventas,
       SUM(dv.cantidad * dv.precio_unitario) as monto_total_facturado
       FROM venta v
       INNER JOIN detalle_venta dv ON v.venta_id = dv.venta_id
       GROUP BY metodo_pago
       ORDER BY monto_total_facturado DESC;

Explicación: Esta consulta agrupa las ventas por método de pago, cuenta el número de transacciones y suma el monto total facturado calculando cantidad × precio unitario para cada detalle de venta.

2. Categoría que genera mayor facturación

   SELECT

       c.nombre as categoria,
       SUM(dv.cantidad * dv.precio_unitario) as facturacion_total
       FROM categoria c
       INNER JOIN producto p ON c.categoria_id = p.categoria_id
       INNER JOIN detalle_venta dv ON p.producto_id = dv.producto_id
       GROUP BY c.categoria_id, c.nombre
       ORDER BY facturacion_total DESC
       LIMIT 1;

Explicación: Une las tablas categoria, producto y detalle_venta para calcular la facturación total por categoría. El resultado se ordena descendentemente y se limita al primer registro (mayor facturación).

3. Empleado con mejor desempeño (más ventas realizadas)
   
SELECT
   
    e.nombre,
    e.apellido,
    COUNT(v.venta_id) as total_ventas
    FROM empleado e
    INNER JOIN venta v ON e.empleado_id = v.empleado_id
    GROUP BY e.empleado_id, e.nombre, e.apellido
    ORDER BY total_ventas DESC
    LIMIT 1;

Explicación: Cuenta el número de ventas realizadas por cada empleado, agrupando por empleado y ordenando por el total de ventas de mayor a menor.

4. Evolución del inventario de un producto específico

   -- Para un producto específico (ejemplo: producto_id = 1)
   
SELECT

    v.fecha,
    p.nombre as producto,
    p.stock_inicial,
    SUM(dv.cantidad) as cantidad_vendida,
    (p.stock_inicial - SUM(dv.cantidad)) as stock_restante
    FROM producto p
    INNER JOIN detalle_venta dv ON p.producto_id = dv.producto_id
    INNER JOIN venta v ON dv.venta_id = v.venta_id
    WHERE p.producto_id = 1  -- Cambiar por el ID del producto deseado
    GROUP BY v.fecha, p.producto_id, p.nombre, p.stock_inicial
    ORDER BY v.fecha ASC;

Explicación: Muestra la evolución del inventario para un producto específico, calculando el stock restante después de cada venta y ordenando por fecha.

5. Clientes sin compras desde su alta
 
  SELECT   
  
    c.cliente_id,
    c.nombre,
    c.apellido,
    c.email
    FROM cliente c
    LEFT JOIN venta v ON c.cliente_id = v.cliente_id
    WHERE v.cliente_id IS NULL;

Explicación: Utiliza un LEFT JOIN para encontrar clientes que no tienen registros asociados en la tabla de ventas (clientes que nunca han comprado).

6. Ticket promedio por venta

   SELECT
   
       AVG(total_venta) as ticket_promedio
       FROM (
       SELECT 
        v.venta_id,
        SUM(dv.cantidad * dv.precio_unitario) as total_venta
       FROM venta v
       INNER JOIN detalle_venta dv ON v.venta_id = dv.venta_id
       GROUP BY v.venta_id
       ) as ventas_totales;

Explicación: Primero calcula el total de cada venta individual en una subconsulta, luego calcula el promedio de todos esos totales para obtener el ticket promedio.

7. Meses con más actualizaciones de venta

SELECT

    YEAR(updatedAt) as año,
    MONTH(updatedAt) as mes,
    MONTHNAME(updatedAt) as nombre_mes,
    COUNT(*) as total_actualizaciones
    FROM venta
    WHERE updatedAt IS NOT NULL
    GROUP BY YEAR(updatedAt), MONTH(updatedAt), MONTHNAME(updatedAt)
    ORDER BY total_actualizaciones DESC;

Explicación: Filtra las ventas que tienen fecha de actualización (updatedAt no nulo), las agrupa por año y mes, y cuenta cuántas actualizaciones hubo en cada período.
