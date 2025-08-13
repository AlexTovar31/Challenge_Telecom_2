# Challenge_Telecom_2

Informe de Churn – Telecom
#1) Objetivo
   
Predecir la probabilidad de cancelación y entender qué factores la impulsan, para accionar tácticas de retención.

#2) Preparación de datos (resumen)
   
Unificación de categorías “No internet service” / “No phone service” → “No”.

Codificación one-hot de categóricas (con drop_first=True para evitar trampa de dummies).

Target: Churn_Yes (1 = cancela, 0 = se queda).

Selección automática de variables:

Se eliminaron variables con |corr(Churn)| < 0.05.

Se eliminaron variables altamente colineales (|corr| > 0.85) conservando la más informativa.

Split estratificado 80/20 y SMOTE solo en train.

#3) Rendimiento de modelos (test)

Decide el “mejor” modelo según tu criterio de negocio (normalmente F1 o Recall si priorizas detectar churners; ROC-AUC si buscas discriminación global).

Resumen esperado (patrón típico en telco):

XGBoost: suele liderar en F1 y ROC-AUC (mejor equilibrio señal/ruido).

Random Forest: muy competitivo, robusto y estable.

Decision Tree: más interpretable, pero tiende a menor F1/ROC-AUC (sobreajuste si no se regula).

Usa tu resultados_df para confirmar: ordena por F1-score o ROC-AUC y elige el campeón.

#4) Factores que más influyen en la cancelación

Basado en la intersección de feature importances de Random Forest y XGBoost, y en los signos de correlación con Churn_Yes.

Riesgo ↑ (pro-churn):

Contrato “Month-to-month” (implícito cuando Contract_One year / Contract_Two year NO están activos).
Clientes sin compromiso tienden a cancelar más.

Cargos mensuales y/o totales altos (Charges_Monthly, Charges_Total).
Sensibilidad a precio y percepción de valor.

PaymentMethod_Electronic check.
Segmento con mayor fricción/impagos vs. métodos automáticos.

InternetService_Fiber optic (en varios datasets aparece asociado a churn por expectativas/precio).
Posible disonancia entre precio y calidad percibida.

Ausencia de servicios de soporte/seguridad: OnlineSecurity_Yes = 0, TechSupport_Yes = 0, DeviceProtection_Yes = 0.
Menor “pegajosidad” del producto.

Tenure bajo (cliente nuevo).
Alto riesgo en los primeros meses.

Riesgo ↓ (protectores):

Contrato de 1 o 2 años (Contract_One year, Contract_Two year).
Compromiso protege contra la fuga.

Métodos de pago automáticos (PaymentMethod_Credit card (automatic), …Bank transfer (automatic)).
Menos fricción, menos sorpresas.

Servicios de valor añadido activos: OnlineSecurity_Yes, TechSupport_Yes, DeviceProtection_Yes.
Aumentan satisfacción y switching costs.

Tenure alto (antigüedad).
Clientes consolidados abandonan menos.

Confirma en tus gráficos: las barras Top-15 de RF/XGB y la correlación con Churn_Yes deben reflejar este patrón (puede variar por tu muestra).

#5) Segmentos de alto riesgo (accionables)

Mes a mes + cargos altos + tenure bajo
– Foco de onboarding, descuentos iniciales, bundling de valor (seguridad/soporte).

Electronic check + mes a mes
– Nudges a pago automático + oferta de upgrade de contrato.

Fibra + cargos altos + sin soporte
– Revisión de calidad percibida, visitas preventivas, trial de TechSupport/OnlineSecurity.

#6) Estrategias de retención (priorizadas)

Upgrade de contrato (Month-to-month → 1/2 años)

Incentivos (meses con descuento, instalación gratuita, bonus de datos/velocidad).

Momento: días 20–40 de la relación (tenure bajo) y antes de la 2ª/3ª factura alta.

Revisión de precios y paquetización

Bundles que integren seguridad + soporte para elevar valor percibido.

Descuentos segmentados por probabilidad de churn (evitar regalar margen a quien no lo necesita).

Migración a pagos automáticos

Incentivo one-shot (p. ej., descuento del 5–10% en la próxima factura).

Proactive care para fibra

Monitoreo QoS y contactabilidad temprana si sube la latencia/quejas.

Mensajería de valor (por qué fibra cuesta más y qué beneficios tangibles entrega).

Onboarding reforzado (clientes nuevos)

Check-in post-instalación (7–10 días), tips de uso, test de velocidad guiado,
canal de soporte prioritario el primer mes.

#7) Umbral operativo y costos

Ajusta el threshold del score del mejor modelo para maximizar F1 o Recall según tu costo de error:

Si perder un cliente es muy caro → prioriza Recall (aceptas más falsos positivos).

Si contactar de más es muy caro → prioriza Precision.

Implementa una matriz de costos (retener vs. dejar ir) para fijar el umbral óptimo.

#8) Recomendaciones técnicas (siguientes pasos)

Tuning de hiperparámetros (Randomized/Grid/BayesSearch) para RF y XGB.

Calibración de probabilidades (Platt/Isotonic) para campañas basadas en score.

Explainability: SHAP global y por cliente (prioriza acciones por motivo).

A/B testing de ofertas de retención por segmento y por motivo.

Monitor de deriva (data drift) y reentrenos trimestrales.


