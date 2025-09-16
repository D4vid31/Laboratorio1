# Tableau – TechStore (Lab 1)

![Dashboard](./Dashboard%20tableau.png)

## Descripción
Análisis de ventas de **TechStore** desarrollado en Tableau. El objetivo es medir **Ventas**, **Cumplimiento de la meta** y **Margen**, y complementar con visualizaciones operativas: evolución mensual, top de productos, distribución por categoría y desempeño de vendedores.

---

## Contenido de esta carpeta
- **[LAB 1 sisinfo.twbx](./LAB%201%20sisinfo.twbx)** – Libro de Tableau empaquetado con todo lo necesario para abrir y revisar el proyecto.
- **Dashboard tableau.png** – Captura del dashboard.

> Los archivos de datos (.csv) del proyecto se encuentran en la carpeta raíz del repo: `../data`

---

## Cómo abrir
1. Descargar **LAB 1 sisinfo.twbx**.
2. Abrir con **Tableau Desktop** (`File → Open` o doble clic).
3. Si Tableau pregunta por rutas de archivos, apuntar a la carpeta `../data`.

---

## Datasets de origen (`../data`)
- `ventas_techstore.csv`  
  Hechos de venta: `fecha`, `producto_id`, `cliente_id`, `cantidad`, `precio_unitario`, `descuento`, `canal_venta`, `vendedor`.
- `productos.csv`  
  Dimensión productos: `producto_id`, `nombre_producto`, `categoría`, `marca`, `precio_costo`, `precio_venta`, `stock_actual`.
- `clientes.csv`  
  Dimensión clientes: `cliente_id`, `nombre_cliente`, `email`, `ciudad`, `fecha_registro`, `segmento`.
- `metas_mensuales.csv`  
  Metas: `año`, `mes`, `meta_ventas`, `meta_unidades`, `meta_nuevos_clientes`.

### Relaciones del modelo
- **ventas_techstore ↔ productos** por `Producto Id`.
- **ventas_techstore ↔ clientes** por `Cliente Id`.
- **ventas_techstore ↔ metas_mensuales** por `Año (ventas) ↔ Año` y `Mes (ventas) ↔ Mes`.

---

## Parámetro
**Año seleccionado** (Integer)
- `0` = Todos los años
- Cualquier otro valor filtra al año indicado (se aplica a todas las hojas).

---

## Campos calculados (principales)

**Ventas Netas**
[Cantidad] * [Precio Unitario] * (1 - [Descuento])

```tableau
[Cantidad] * [Precio Unitario] * (1 - [Descuento])
