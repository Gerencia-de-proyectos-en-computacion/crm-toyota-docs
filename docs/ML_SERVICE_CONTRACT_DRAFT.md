# Contrato Cerrado de Integración: `backend-crm` ↔ `ml-service`

Contrato sincronizado con la entrega de Jorge Chipoco (`ml-service` FastAPI + `RandomForestRegressor`) y adaptado en `RoutingMlForecastClient.java`.

## 1. Endpoints Reales Conectados
- **`GET /health`**: Verifica carga en memoria de `models/random_forest.joblib`.
- **`GET /metrics`**: Expone `models/metrics.json` con `r2`, `wape_pct`, `mape_positive_pct`, `rmse` e intervalo de intermitencia `intermittency_adi`.
- **`POST /predict/batch`**:
  - **Request (`snake_case`):** `items[]` con `part_id` (homologa SKUs `TOY-*`/`DEN-*`/`AIS-*` a `REP-001..REP-020`), `vehicle_model`, `part_category`, `avg_mileage`, `vehicles_served`, `month`, `week_of_year`, `lag_1`, `lag_2`, `lag_4`, `rolling_mean_4`, `rolling_std_4`.
  - **Response (`snake_case`):** Lista JSON con `part_id`, `predicted_demand`, `reorder_point`, `horizon_days: 7` y `model_version`.

## 2. Seguridad y Resiliencia
- Header `X-Internal-API-Key` validado cuando la variable de entorno `ML_INTERNAL_API_KEY` está activa en nube.
- Si `ml-service` no responde o está apagado en desarrollo local, `RoutingMlForecastClient` conmuta sin caída a `MockMlForecastClient`.
