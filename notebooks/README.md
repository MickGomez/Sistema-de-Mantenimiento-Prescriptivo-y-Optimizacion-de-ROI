Documentación del proceso experimental para la optimización del ROI de mantenimiento.

##  Flujo de Trabajo Técnico
El notebook principal, `Industrial_Asset_Prioritization_Value_Based.ipynb`, implementa una arquitectura de decisión robusta:

1. **Ingeniería Cinemática:** Creación de variables de estado (derivadas y medias móviles EMA) para capturar la tendencia de degradación.
2. **Validación Cruzada por Grupos:** Uso de `GroupKFold` para asegurar que el modelo aprenda patrones generales y no memorice IDs de máquinas específicas.
3. **Calibración de Probabilidades:** Implementación de un Calibrador Platt (sigmoide) para transformar los scores de XGBoost en probabilidades financieras reales.
4. **Optimización bajo Restricción:** Algoritmo de priorización para 12 cuadrillas operativas simultáneas.

##  Auditoría de Estabilidad
Se incluye una sección de **Simulación Monte Carlo** que valida la resiliencia del ranking ante variaciones en los costos operativos y disponibilidad de personal.
