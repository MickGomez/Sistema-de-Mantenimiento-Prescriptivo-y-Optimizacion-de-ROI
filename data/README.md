Este directorio contiene el conjunto de datos utilizado para el desarrollo del **Motor de Priorización Predictiva**.

##  Estructura del Dataset
El archivo principal (`datos_signals.csv`) consta de **350,966 registros** que simulan el flujo de datos de un sistema SCADA real.

| Dimensión | Detalle |
| :--- | :--- |
| **Frecuencia** | Muestreo horario de señales de sensores. |
| **Features** | Telemetría de vibración, temperatura, presión y carga operativa. |
| **Target** | RUL (Remaining Useful Life) calculado hasta el evento de fallo. |

##  Registro de Eventos (CMMS)
El archivo `datos_fallos.csv` actúa como el registro histórico de intervenciones y averías reales.

* **Función:** Proporciona las etiquetas de entrenamiento para el cálculo del RUL (Remaining Useful Life).
* **Contenido:** Incluye el ID del activo, el tipo de falla detectada y la marca temporal precisa del colapso funcional.
