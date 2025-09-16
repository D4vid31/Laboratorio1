# Laboratorio1

# Comparativo: Tableau vs Power BI (TechStore – Lab 1)

Este análisis resume cómo resolví el mismo caso de negocio en **Tableau** y en **Power BI**, qué construí en cada herramienta y qué aprendí al compararlas. La carpeta del repo contiene ambos trabajos:

* `tableau/` → tablero y README del Lab 1 en Tableau
* `powerbi/` → reporte `.pbix`, captura y README del Lab 1 en Power BI

---

## 1) Qué construí en cada herramienta

### Métricas (en ambos)

* **Ventas Netas**
* **Costo Total**
* **Margen \$** y **Margen %**
* **Meta Ventas Año** y **Meta Ventas Mes**
* **Cumplimiento %** (Ventas Netas / Meta Ventas Año)
* **Bandas/Estado**: color/estado de Cumplimiento y de Margen

### Visualizaciones (equivalentes)

* **KPI de Ventas**
* **KPI de Cumplimiento %**
* **Top 10 productos** (barras horizontales por ventas)
* **Ventas por categoría** (pie)
* **Vendedores** (tabla/matriz con Cantidad, Margen \$, Margen %, Ventas Netas)

### Diferencias específicas

* **Power BI**: agregué un **KPI adicional** de **Clientes Activos** y un **gráfico dual** “Ventas Netas vs Meta Ventas Mes” (dos ejes). Apliqué un **tema JSON** con la paleta de Manuelita.
* **Tableau**: utilicé **Measure Names/Measure Values** para la tabla de Vendedores y parámetros/filtros para el **año**.

---

## 2) Resultados y consistencia numérica

* **Tableau** (KPI Ventas): \~ **\$46.467.000**
* **Power BI** (KPI Ventas): \~ **\$43,34 mill.**

**Causa del gap**: en Power BI utilicé un **slicer de rango de fechas** (15/01/2024–18/02/2024), mientras que en Tableau el tablero está filtrado por **Año 2024** completo (con el parámetro “Filtro Año: True”).
**Cómo alinearlo**:

* En Power BI: mover el slicer a **01/01/2024 – 31/12/2024**.
* En Tableau: mantener “Año seleccionado: 2024” y confirmar que no haya filtros adicionales por mes/rango.

> Con el mismo período, ambos motores entregan cifras equivalentes (diferencias sólo por formato y redondeo).

---

## 3) Modelado y cálculos

| Aspecto                 | Tableau                                                                                                         | Power BI                                                                                                                                                                   |
| ----------------------- | --------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Modelo**              | Relacioné `ventas_techstore` con `productos`, `clientes` y metas por claves (Producto Id, Cliente Id, Mes/Año). | Modelo estrella: `ventas_techstore` (hechos), `productos`, `clientes`, `metas_mensuales` y tabla de **Calendario** (Date/InicioMes). Relaciones con cardinalidad adecuada. |
| **Cálculo de columnas** | Calculated fields (ej.: Ventas Netas, Margen %, Cumplimiento %).                                                | **DAX measures** para Ventas Netas, Costo, Margen \$, Margen %, Cumplimiento %, Meta Año/Mes, Estado y Color.                                                              |
| **Fechas**              | Filtros por año/mes; parámetro de año.                                                                          | **Tabla Calendario** + slicer de rango de fechas; Join de metas por **Inicio de Mes**.                                                                                     |
| **Tabla de Vendedores** | “Measure Names/Values” simplifica mostrar varias métricas juntas.                                               | **Matriz** con medidas DAX; fácil agregar subtotales totales y estilo tabular.                                                                                             |

**Notas de aprendizaje en DAX**

* Evitar anidar agregaciones (ej.: `SUM(SUMX(...))`).
* Usar `SUMX(ventas_techstore, [cantidad]*[precio]*...)` para Ventas/Costo.
* Crear **bandas** con `SWITCH(TRUE(), ...)` para estados y colores.

---

## 4) Construcción de las visualizaciones

| Elemento                 | Tableau                                     | Power BI                                                                           |
| ------------------------ | ------------------------------------------- | ---------------------------------------------------------------------------------- |
| **KPI**                  | Text Mark con formateo.                     | Visual de **Tarjeta** con formateo por categoría y tamaño de fuente.               |
| **Top 10 productos**     | Barras horizontales con orden y etiqueta.   | Barras horizontales; sort por Ventas Netas y data labels.                          |
| **Ventas por categoría** | Pie con etiquetas internas personalizadas.  | Pie con **tema JSON** (paleta Manuelita).                                          |
| **Ventas vs Meta**       | Línea por mes (y línea de meta o etiqueta). | **Gráfico dual**: Ventas Netas (eje primario) vs Meta Ventas Mes (eje secundario). |
| **Vendedores**           | Tabla con Measure Names/Values.             | **Matriz** con subtotal general automático.                                        |

---

## 5) Experiencia de desarrollo

**Tableau**

* **Velocidad** para prototipar; arrastrar y soltar campos y medidas.
* “Measure Names/Values” es muy práctico para tablas comparativas.
* Acciones y tooltips enriquecidos sin mucho esfuerzo.

**Power BI**

* **Modelado** robusto (Calendario, relaciones, roles) y **DAX** potente para lógicas de negocio.
* **Slicers** y **cross-highlighting** integrados.
* **Temas JSON** para estandarizar marca y colores.

---

## 6) Rendimiento, despliegue y gobierno

| Tema            | Tableau                                                         | Power BI                                                                 |
| --------------- | --------------------------------------------------------------- | ------------------------------------------------------------------------ |
| **Rendimiento** | Extracts (Hyper) y caché; muy rápido en prototipos.             | Import (en memoria), **DirectQuery**/**Composite** para fuentes grandes. |
| **Publicación** | Tableau Server/Cloud (Creator/Explorer/Viewer).                 | Power BI Service: **Pro/PPU/Fabric** para compartir/refresh/Lineage.     |
| **Gobierno**    | Permisos a nivel de proyecto/vista; Data Sources centralizadas. | **Datasets** reutilizables, Workspaces, **lineage**, roles RLS.          |
| **Costo**       | Licencias por rol (Creator/Explorer/Viewer).                    | Pro/PPU por usuario; opciones Fabric para escalamiento.                  |

---

## 7) Versionado y archivos

| Elemento      | Tableau                                                           | Power BI                                                                                                             |
| ------------- | ----------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **Formato**   | `.twbx` (paquete) / `.twb` (XML).                                 | `.pbix` (binario).                                                                                                   |
| **En Git**    | Fácil dif con `.twb` (XML); con `.twbx` se versiona como binario. | `.pbix` es binario; se recomienda capturas/README y/o **PBIP/TMDL** para descomponer artefactos si se necesita diff. |
| **Evidencia** | Captura `Dashboard tableau.png`.                                  | Captura `Dashboar Power.png`.                                                                                        |

---

## 8) Principales aprendizajes del lab

1. Las **diferencias de cifras** casi siempre se explican por **filtros/fechas distintos**. Alinear ventanas de tiempo es clave.
2. En Power BI, la **tabla Calendario** simplifica metas por mes, acumulados y comparativos.
3. **DAX** exige pensar en **filtrado de contexto** y **agregaciones** (SUMX, DIVIDE, SWITCH).
4. Los **temas** (JSON) en Power BI ayudan a estandarizar la **marca** (como el esquema Manuelita).
5. Ambos entregan el tablero solicitado; la **elección** depende del stack, gobierno y complejidad del modelo.

---

**Repositorio**

* Tableau: `tableau/LAB 1 sisinfo.twbx` + `Dashboard tableau.png` + README
* Power BI: `powerbi/Lab 1 Power.pbix` + `Dashboar Power.png` + README

Ambas carpetas contienen los pasos y capturas que respaldan el trabajo realizado.
