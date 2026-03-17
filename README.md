# 🏭 Sistema Predictivo de Mantenimiento IIoT
### Priorización de Intervenciones bajo Restricciones Operativas Reales

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python)
![XGBoost](https://img.shields.io/badge/XGBoost-1.7+-orange)
![Scikit-Learn](https://img.shields.io/badge/Scikit--Learn-1.3+-red?logo=scikit-learn)
![Status](https://img.shields.io/badge/Status-Completado-green)

---

## 📌 Resumen

En una planta industrial, predecir el fallo de un equipo aislado carece de utilidad si no se enmarca en las restricciones físicas de mantenimiento. El problema operativo real es decidir **cómo asignar un número limitado de cuadrillas (ej. 12) sobre el parque total de activos**, priorizando por impacto económico y no por probabilidad bruta.

Este proyecto presenta un **Proof of Concept (PoC)** de un motor de priorización predictiva. Validado sobre una muestra representativa de 50 equipos, el pipeline establece una arquitectura de datos diseñada para escalar al universo completo de activos de una planta industrial. El sistema integra señales de degradación IIoT con la criticidad financiera de cada nodo, entregando un plan de intervención óptimo por turno.

> 📁 **Dataset:** 350,966 registros horarios de señales de telemetría (vibración, termodinámica y consumo eléctrico) sobre 50 equipos industriales, incluyendo eventos de fallo empírico y censura por la derecha.

---

## 🎯 El Problema

| Problema | Impacto |
|:---|:---|
| Mantenimiento correctivo puro | Absorción del 100% del costo por paradas no planificadas. |
| Priorización por probabilidad bruta | Riesgo de intervenir un sensor secundario antes que un compresor crítico de línea. |
| Capacidad de mantenimiento limitada | Las cuadrillas físicas son un cuello de botella; cada intervención mal asignada genera un costo de oportunidad directo. |
| Fuga de datos (Data Leakage) en series temporales | La imputación deficiente de paquetes de red perdidos introduce información del futuro, invalidando el modelo en producción. |

---

## 🛠️ Las Tres Herramientas que Entrega el Sistema

 **Motor de Priorización por Valor Esperado** — Clasifica cada activo mediante el producto de su riesgo relativo y el costo real de parada. Un equipo con 30% de riesgo y costo de $150k se prioriza sobre uno con 80% de riesgo y costo de $2k.

 **Alerta de Déficit Estructural** — Monitorea el riesgo residual. Cuando el valor esperado de pérdida de los equipos no atendidos supera un umbral crítico frente a la pérdida evitada, el sistema emite una justificación financiera para la ampliación temporal de la fuerza de mantenimiento.

 **Auditoría Monte Carlo** — Evalúa la estabilidad del ranking prescriptivo ante 50 configuraciones aleatorias de disponibilidad de planta, garantizando la robustez de la matriz de decisión ante fluctuaciones estocásticas.

---

## 📊 Resultados (Entorno de Validación)

El objetivo central es rankear máquinas por urgencia operativa, no minimizar el error de regresión a meses vista. 

| Modelo | RMSE (h) | Avg Precision | Precision@12 Proyectada |
|:---|---:|---:|---:|
| Baseline (Predice la media) | ~980h | ~0.013 | < 2.0% |
| Regresión Lineal (Ridge) | ~420h | ~0.310 | ~58% |
| **XGBoost Regressor (PoC Final)** | **~180h** | **0.993** | **75% - 85%** |

> *Nota de Transparencia Técnica:* Debido a la naturaleza determinista de la degradación en el dataset de validación, el ensamble XGBoost alcanzó un límite teórico de `Precision@12 = 100%`. En un entorno industrial empírico, con ruido de sensor y estocasticidad mecánica, un rendimiento operativo sostenido del **75% al 85%** se considera un objetivo realista y de alto impacto financiero.

---

## 💰 Evaluación Económica — Decisión de Negocio

La arquitectura asume que la métrica final no es el F1-Score, sino el ROI del turno de mantenimiento.

### Impacto Financiero por Turno (Simulación PoC)

| Indicador | Valor |
|:---|:---|
| Pérdida esperada evitada | Calculado dinámicamente en función del vector de criticidad. |
| Costo de intervención preventiva | Estimado en ~5–15% del costo de falla funcional por activo. |
| ROI del turno | `Pérdida_evitada − Costo_intervención` |
| Cobertura de riesgo con 12 cuadrillas | ~52% del riesgo financiero total |

> Los importes paramétricos se inyectan en la Sección 6. Para un despliegue en producción, el pipeline exige acoplar el maestro de equipos (`df_fallos`) a los módulos PM/MM del ERP de la planta (ej. SAP, IBM Maximo) para heredar los costos fijos y variables en tiempo real.

---

### Impacto por Escenario Operativo

| Escenario | Consecuencia |
|:---|:---|
| Fallo no detectado en activo clase A | $50k–$150k de pérdida no planificada |
| Intervención desperdiciada en activo sano | Costo de mano de obra + capacidad perdida para activo real |
| Riesgo residual > 1.5× pérdida evitada | Alerta de déficit: el sistema recomienda ampliar capacidad |

---

## 🔑 Decisiones Técnicas Clave

**GroupKFold estricto para Series Temporales**
La validación estándar (KFold) provocaría fuga de datos al filtrar registros del mismo equipo en los conjuntos de entrenamiento y prueba. `GroupKFold` garantiza que el ensamble aprenda patrones físicos de degradación generalizables, aislando la firma temporal de cada activo.

**Aislamiento de la Métrica Operativa (Precision@K)**
El error cuadrático (RMSE) carece de interpretabilidad en planta. La evaluación se centra en `Precision@K` para responder a la restricción física: *de los K equipos intervenidos hoy, ¿cuántos justificaban realmente la parada preventiva?*

**Snapshot Cross-Sectional**
Extraer únicamente el último registro de cada máquina para evaluar el modelo genera métricas artificialmente infladas. El sistema se audita simulando cortes transversales aleatorios en la línea de tiempo, replicando la prevalencia real de fallos inminentes en un turno cualquiera.

---

## 💻 Stack Tecnológico

```text
Python 3.10+
├── pandas / numpy              → Linaje de datos, purga de leakage y vectorización
├── scikit-learn                → GroupKFold, modelos lineales de baseline
├── xgboost                     → Modelo núcleo de regresión RUL
├── matplotlib / seaborn        → Análisis exploratorio y diagnóstico visual
└── joblib                      → Serialización del pipeline y pesos del ensamble
```

---

## 🚀 Cómo Ejecutar

1. Abrí el notebook en Google Colab:

2. El dataset se carga automáticamente desde Google Drive al ejecutar la celda de carga.

3. Ejecutá todas las celdas en orden. El notebook está diseñado para correr de principio a fin sin intervención manual.

---



## 🔭 Próximos Pasos

1. **Inyección de ruido adversarial** — Validar la ventaja real de XGBoost sobre Ridge bajo solapamiento de distribuciones en la frontera de decisión
2. **Pivote a clasificación binaria** — `y = (RUL ≤ 168h)` para incorporar el 100% de la planta al entrenamiento sin necesidad de etiquetas RUL deterministas
3. **Modelos de supervivencia** — Weibull AFT o Cox proporcional para manejo nativo de censura por la derecha
4. **API de inferencia en tiempo real** — Endpoint para integración con sistemas SCADA

---

## 🧠 Limitaciones y Consideraciones

| Limitación | Implicación | Mitigación Actual |
|:---|:---|:---|
| Dataset sintético con degradación determinista | Separación limpia entre clases por diseño, sin solapamiento real | Evaluación sobre planta con 98.7% de máquinas sanas |
| Entrenamiento solo con máquinas que fallaron | Comportamiento estacionario a largo plazo no parametrizado | Las censuradas se incluyen en inferencia y evaluación final |
| Costos financieros simulados | El ROI exacto depende del ERP real de la planta | Arquitectura preparada para `pd.merge()` con SAP/Maximo sin modificar el pipeline |
| Calibración Platt sobre distribución sintética | Requiere recalibración con datos del primer mes en producción | Conjunto de calibración completamente aislado del ensamble |

---



> **"Saber que una máquina va a fallar no es suficiente.
> El problema real es decidir cuál de las 5.755 máquinas merece
> las 12 cuadrillas disponibles hoy. Ese es el problema que resuelve este sistema."**
