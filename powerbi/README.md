# Power BI — LAB 1 (TechStore)

Carpeta: `/powerbi`  
Reporte principal: `Lab 1 Power.pbix`

![Dashboard Power BI](./Dashboar%20Power.png)

---

## Contenido

| Archivo | Descripción |
|---|---|
| `Lab 1 Power.pbix` | Reporte de Power BI con modelo, medidas DAX, KPIs y visualizaciones. |
| `Dashboar Power.png` | Captura del dashboard principal. |


---

## Objetivo

Construir un dashboard ejecutivo para TechStore que permita:

1. Monitorear tres KPIs principales.
2. Analizar la tendencia de ventas contra metas mensuales.
3. Identificar el Top 10 de productos por ventas.
4. Revisar la participación por categoría de producto.
5. Filtrar por año y por rango de fecha con segmentadores.

---

## Modelo de datos

Tablas utilizadas:

- `ventas_techstore` (hechos): fecha, producto_id, cliente_id, cantidad, precio_unitario, descuento, canal_venta, vendedor.
- `productos` (dimensión): producto_id, nombre_producto, categoría, marca, precio_costo, precio_venta, stock_actual.
- `clientes` (dimensión): cliente_id, nombre_cliente, email, ciudad, fecha_registro.
- `metas_mensuales` (hechos de metas): Fecha Meta, año, mes, meta_ventas, meta_unidades, meta_nuevos_clientes.
- `Calendario` (tabla calendario): Date con derivadas Año, Mes, MesN, etc.

Relaciones (todas 1:* desde dimensiones a hechos):

- `productos[producto_id]` → `ventas_techstore[producto_id]`
- `clientes[cliente_id]` → `ventas_techstore[cliente_id]`
- `Calendario[Date]` → `ventas_techstore[fecha]`
- `Calendario[Date]` → `metas_mensuales[Fecha Meta]`

Estas relaciones permiten que los segmentadores (Año y Rango de fecha) afecten tanto ventas como metas.

---

## Medidas DAX

```DAX
Ventas Netas =
SUMX(
    'ventas_techstore',
    'ventas_techstore'[cantidad] *
    'ventas_techstore'[precio_unitario] *
    (1 - 'ventas_techstore'[descuento])
)

Costo Total =
SUMX(
    'ventas_techstore',
    'ventas_techstore'[cantidad] * RELATED('productos'[precio_costo])
)

Margen $ = [Ventas Netas] - [Costo Total]

Margen % = DIVIDE([Margen $], [Ventas Netas])

Meta Ventas Año = SUM('metas_mensuales'[meta_ventas])

Meta Ventas Mes = SUM('metas_mensuales'[meta_ventas])

Cumplimiento % = DIVIDE([Ventas Netas], [Meta Ventas Año])

Estado Cumplimiento =
VAR c = [Cumplimiento %]
RETURN
    SWITCH(
        TRUE(),
        c >= 1, "OK",
        c >= 0.90, "Riesgo",
        "Bajo"
    )

Color Cumplimiento =
SWITCH(
    [Estado Cumplimiento],
    "OK", "#2E7D32",
    "Riesgo", "#FFA000",
    "Bajo", "#D32F2F"
)

Clientes Activos = DISTINCTCOUNT('ventas_techstore'[cliente_id])
