# Análisis Predictivo de E-Commerce — Olist Dataset

Proyecto de análisis de datos y modelado predictivo sobre el dataset público de Olist (marketplace brasileño de e-commerce). Cubre desde la carga y limpieza de datos hasta la construcción de un modelo de regresión lineal.

---

## Estructura del Proyecto

```
predict/
├── Copia_de_Te_damos_la_bienvenida_a_Colaboratory.ipynb   # Notebook principal
├── README.md
└── Dataset/
    ├── olist_orders_dataset.csv
    ├── olist_customers_dataset.csv
    ├── olist_geolocation_dataset.csv
    ├── olist_order_items_dataset.csv
    ├── olist_order_payments_dataset.csv
    ├── olist_order_reviews_dataset.csv
    ├── olist_products_dataset.csv
    ├── olist_sellers_dataset.csv
    └── product_category_name_translation.csv
```

---

## Dataset

Se utilizan los **9 archivos CSV** del dataset público de Olist, que contienen información real de pedidos realizados entre 2016 y 2018:

| Archivo | Descripción |
|---|---|
| `olist_orders_dataset.csv` | Pedidos: estado, fechas de compra y entrega |
| `olist_customers_dataset.csv` | Clientes y sus estados (UFs) |
| `olist_order_items_dataset.csv` | Ítems por pedido: precio y flete |
| `olist_products_dataset.csv` | Catálogo de productos con categorías |
| `olist_order_payments_dataset.csv` | Métodos y valores de pago |
| `olist_order_reviews_dataset.csv` | Reseñas de clientes |
| `olist_sellers_dataset.csv` | Información de vendedores |
| `olist_geolocation_dataset.csv` | Coordenadas por código postal |
| `product_category_name_translation.csv` | Traducción de categorías PT → EN |

---

## Flujo del Análisis

### 1. Carga y Exploración de Datos
- Importación de todos los datasets con `pandas`.
- Exploración inicial con `.head()` y `.dtypes`.
- Ranking de los **top 5 estados con más pedidos entregados**, cruzando `df_orders` con `df_customers`.

### 2. Manipulación de Fechas y Tiempos
- Conversión de columnas de texto a formato `datetime`:
  - `order_purchase_timestamp`
  - `order_delivered_customer_date`
  - `order_estimated_delivery_date`
- Cálculo de la nueva columna **`dias_envio`**: diferencia en días entre compra y entrega real.
- Detección de retrasos mediante la columna booleana **`es_retraso`**: `True` si la entrega real superó la fecha estimada.

### 3. Mega-Merge Financiero
Construcción de una **tabla maestra** encadenando 3 cruces (`merge`):

```
pedidos_entregados → df_items → df_products → df_translations
```

- **Cruce 1:** Pedidos entregados ✕ Ítems → obtiene precios y fletes por pedido.
- **Cruce 2:** Resultado anterior ✕ Productos → agrega la categoría del producto.
- **Cruce 3:** Resultado anterior ✕ Traducciones → nombre de categoría legible en inglés.

**Agrupación múltiple** por categoría calculando:
- `Ingreso_Total`: suma de precios.
- `Flete_Promedio`: promedio del costo de envío.
- `Cantidad_Vendida`: conteo de ítems vendidos.

Resultado: **Top 10 categorías** por ingreso total, presentado como reporte ejecutivo.

### 4. Modelo Predictivo — Regresión Lineal
**Objetivo:** predecir el precio (`price`) a partir del costo de envío (`freight_value`).

Pasos:
1. Verificación y eliminación de valores nulos en `df_items`.
2. División del dataset: **80% entrenamiento / 20% prueba** (`train_test_split`, `random_state=42`).
3. Entrenamiento del modelo con `LinearRegression` de scikit-learn.
4. Evaluación con métricas:
   - **MSE** (Error Cuadrático Medio)
   - **R² = 0.18** (coeficiente de determinación)
5. Visualización: gráfico de dispersión (datos reales en azul) con la línea de regresión superpuesta (rojo).

---

## Resultados y Conclusiones

- **Correlación positiva** entre flete y precio: productos más caros tienden a tener mayor costo de envío.
- **R² = 0.18** indica que el flete explica apenas el 18% de la variación en el precio. El precio está determinado por factores adicionales como el peso del producto, la categoría, la distancia, entre otros.
- El **reporte financiero** reveló las categorías con mayor ingreso, lo que complementa la interpretación del modelo.
- Este análisis sirve como **base para modelos más complejos** (e.g., regresión múltiple, Random Forest) incorporando más variables.

---

## Tecnologías Utilizadas

- **Python 3**
- **pandas** — manipulación y análisis de datos
- **numpy** — operaciones numéricas
- **scikit-learn** — modelado predictivo (`LinearRegression`, `train_test_split`, métricas)
- **matplotlib** — visualización de resultados
- **Jupyter Notebook / Google Colab**

---

## Cómo Ejecutar

1. Clona el repositorio y asegúrate de que los archivos CSV estén en la carpeta `Dataset/` (o ajusta las rutas de `pd.read_csv` según corresponda).
2. Instala las dependencias:
   ```bash
   pip install pandas numpy scikit-learn matplotlib
   ```
3. Abre el notebook `Copia_de_Te_damos_la_bienvenida_a_Colaboratory.ipynb` en Jupyter o Google Colab y ejecuta las celdas en orden.
