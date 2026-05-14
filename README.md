# Predicción de Recompra de Clientes — Online Retail II

Modelo que identifica qué clientes van a volver a comprar antes de que lo hagan. Combina segmentación no supervisada (KMeans) y clasificación supervisada (HistGradientBoosting) sobre 1M+ transacciones de retail online en 43 países.

## Tecnologías

Python · Pandas · Scikit-learn · XGBoost · Optuna · DALEX · Matplotlib · Jupyter Notebook

## Estructura del repositorio

```
online-retail-rebuying/
├── 01_Documentos/
│   └── online_retail_prediccion_recompra.pdf   # Presentación del proyecto
├── 02_Datos/
│   └── online_retail_II.xlsx                   # Dataset Online Retail II (UCI)
├── 03_Notebooks/
│   └── online_retail_II.ipynb                  # Notebook principal end-to-end
├── 04_Modelos/
│   ├── modelo_optuna.pkl                        # Modelo final (ganador)
│   ├── modelo_grid_search.pkl                   # Modelo GridSearch (comparativa)
│   └── modelo_random_search.pkl                 # Modelo RandomSearch (comparativa)
├── .gitignore
├── requirements.txt
└── README.md
```

## El problema

Dado el historial de compras de cada cliente en los últimos 18 meses, predecir si volverá a comprar en los próximos 6. El resultado es una probabilidad entre 0 y 1 que permite priorizar acciones comerciales por segmento.

## Dataset

Online Retail II (UCI Machine Learning Repository) — transacciones de un retailer online del Reino Unido entre 2009 y 2011.

- 1.067.371 transacciones originales
- 5.942 clientes únicos identificados
- 5.305 productos distintos
- 43 países

Decisiones clave de limpieza: 67.242 duplicados eliminados, 19.102 devoluciones separadas, 22,1% de compras anónimas conservadas como grupo especial, variable país excluida por falta de varianza (84% UK).

## Pipeline

**Features construidas por cliente (ventana 18 meses):**
- Recency — días sin comprar
- Nº de compras — líneas de pedido históricas
- Gasto total — revenue acumulado (£)
- Cadencia — días medios entre pedidos
- Tasa de devolución — % pedidos devueltos

**Segmentación (KMeans — 4 clusters):**

| Segmento | Perfil | Prob. recompra |
|----------|--------|----------------|
| C3 VIPs | Compra masiva y reciente | 91% |
| C2 Fieles | Buenos tickets, pedidos espaciados | 58% |
| C1 Indecisos | Frecuentes con alta tasa devolución (36%) | 49% |
| C0 Desconectados | Una compra hace +9 meses | 26% |

**Clasificación — 3 optimizadores comparados:**
- RandomizedSearchCV
- GridSearchCV
- Optuna (ganador)

4 algoritmos testados: Logistic Regression, Random Forest, XGBoost, HistGradientBoosting.

## Resultados

- **AUC-ROC: 0.864** — sin sobreajuste
- **2.0x** más eficiente que contacto aleatorio en el top 10% de clientes (curva Lift)
- **75%** de las ventas futuras capturadas contactando a menos del 40% de la base
- Balance natural 52/48 — sin necesidad de ajuste

**Importancia de variables (DALEX):**

| Variable | Importancia |
|----------|-------------|
| Tasa devolución | +0.210 |
| Recency | +0.119 |
| Nº compras | +0.065 |
| Gasto total | +0.017 |
| Cadencia | +0.010 |

## Cómo ejecutarlo

```bash
pip install -r requirements.txt

# Ejecutar: 03_Notebooks/online_retail_II.ipynb
```

---

Proyecto académico desarrollado durante el Master en Data Science de Evolve.
