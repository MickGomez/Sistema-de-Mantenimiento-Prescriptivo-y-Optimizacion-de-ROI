Este directorio contiene el conjunto de datos utilizado para el desarrollo del **Motor de Priorización Predictiva**.

##  Nota sobre el Volumen de Datos (GitHub Quota)
Debido a las restricciones de tamaño de archivo de GitHub (límite de 25MB para visualización), el archivo de señales incluido en esta carpeta es una **versión reducida (subset)**:

* **Archivo en el repositorio:** `datos_signals_reducido.csv` (Versión truncada).
* **Dataset Original:** El modelo final fue entrenado y validado utilizando el dataset completo de **350,966 registros**. Los resultados, métricas de ROI y la Auditoría Monte Carlo visibles en el notebook reflejan el procesamiento de la población total de datos.

##  Estructura del Dataset (Full Version)
El flujo de datos simula un sistema SCADA industrial con las siguientes especificaciones técnicas:

| Dimensión | Detalle |
| :--- | :--- |
| **Frecuencia** | Muestreo horario de señales de sensores. |
| **Features** | Telemetría de vibración, temperatura, presión y carga operativa. |
| **Target** | RUL (Remaining Useful Life) calculado hasta el evento de fallo. |

## 📝 Registro de Eventos (CMMS)
El archivo `datos_fallos.csv` actúa como el registro histórico de intervenciones y averías reales.

* **Contenido:** Incluye el ID del activo, el tipo de falla detectada y la marca temporal precisa del colapso funcional.


##  Replicabilidad
Para ejecutar el notebook y obtener los mismos indicadores financieros de éxito, se requiere el dataset completo. El notebook está diseñado para realizar la carga y unión (merge) de estos archivos de forma automática si se dispone de la fuente total.
