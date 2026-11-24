---
trigger: glob
globs: "**/ml-service/**, **/contracts/**"
description: "Reglas del microservicio FastAPI Random Forest, demanda intermitente y contrato con Spring Boot."
---

# Reglas de Ingeniería — Microservicio ML y Contrato (`ml-service`)

1. **Arquitectura de Inferencia Batch:**
   - Entorno Python 3.11 gestionado con `uv` (`FastAPI + scikit-learn + pandas + joblib`).
   - El modelo `RandomForestRegressor` nunca se invoca en el flujo síncrono de cotización. Se ejecuta en lote (semanal o bajo demanda de `ADMIN`) y sus resultados alimentan `demand_forecasts` en PostgreSQL.
   - Exponer siempre `GET /health` y proteger endpoints de inferencia con header `X-Internal-API-Key`.

2. **Métricas Válidas para Demanda Intermitente (`ADI >= 1.32`):**
   - Todo entrenamiento, validación y payload de métricas hacia el panel Admin debe reportar:
     - `r2` (meta `>= 0.80`)
     - `wape` — Weighted Absolute Percentage Error (`sum(|y - y_hat|) / sum(y)`, meta `<= 0.15` / `15%`), métrica principal inmune a semanas con demanda `0`.
     - `rmse` — Root Mean Squared Error en unidades físicas.
     - `mape_nonzero` — MAPE calculado **únicamente** sobre periodos donde `y_real > 0` (jamás dividir entre cero ni descartar silenciosamente series intermitentes).
     - `adi` y `cv2` informativos (clasificación Syntetos & Boylan).

3. **Calidad y Docs-as-Code:**
   - Validar esquemas Pydantic v2, cálculo de `reorder_point` y métricas con **Pytest**.
   - Sincronizar cualquier ajuste del contrato JSON con `ml-service/docs/API_CONTRACT.md` y con el adaptador `MlForecastClient` de `backend-crm`.
