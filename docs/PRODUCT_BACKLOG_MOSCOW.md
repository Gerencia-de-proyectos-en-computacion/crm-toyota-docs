# Product Backlog Priorizado (MoSCoW) y Planificación Táctica de 2 Sprints

**Inicio Real:** Martes 24 de noviembre de 2026  
**Estrategia Operativa Acordada:** Despliegue temprano del MVP + ML entre **Martes noche y Miércoles 25 temprano** para ejecutar entrevistas reales con usuarios el miércoles, presentar evidencias sólidas en el **Hito 1 (Jueves 26)**, e incorporar mejoras avanzadas + correcciones QA entre viernes y sábado para cerrar al 100% en el **Hito 2 (Domingo 29)**.

---

## 1. Mapeo Oficial Consolidado de Historias (`USC01–USC11`), Extras (`EXT01–EXT04`) y `RNF01–RNF04`

> **Nota de Trazabilidad PMBOK:** En la matriz preliminar del curso figuraba una `USC12` separada para roles (con rol "Mecánico", hoy fuera de alcance de bahía) y el estado "Facturada" en `USC07`. En esta línea base definitiva:
> - La seguridad RBAC (`ADMIN`, `RECEPCIONISTA`, `ALMACEN`) + Ley N.º 29733 queda consolidada transversalmente en **`RNF03`** (manteniendo referencia cruzada a `ex-USC12` en la matriz de Aaron para que ninguna pregunta del jurado quede suelta).
> - El estado terminal comercial en `USC07` es **`CERRADA`** (nunca "Facturada").

### SPRINT 1 — MVP Core + Despliegue Piloto Temprano (Meta: Miércoles 25 AM para Entrevistas / Corte Oficial Hito 1: Jueves 26 Nov)

| ID | Prioridad MoSCoW | Requisito / Historia | Criterio clave de Aceptación (DoD) | Repo Principal |
| :--- | :---: | :--- | :--- | :--- |
| **RNF03** *(ex-USC12)* | **MUST** | Seguridad RBAC (`ADMIN`, `RECEPCIONISTA`, `ALMACEN`), BCrypt, JWT de turno completo (14h + sliding refresh) y consentimiento explícito Ley N.º 29733. | Login funcional por rol, rutas protegidas y persistencia `data_consent_accepted` + timestamp al crear cliente. | `backend-crm`, `frontend-web` |
| **USC01** | **MUST** | Gestión de clientes particulares (**DNI 8 dígitos**) y corporativos (**RUC 11 dígitos** `10`/`20`). | Alta/edición `< 1s`, bloqueo `409` en documento duplicado, rechazo `400` sin check Ley 29733. | `backend-crm`, `frontend-web` |
| **USC02** | **MUST** | Gestión de vehículos Toyota vinculados al cliente (placa MTC `ABC-123` + flag placa especial, modelo Yaris/Corolla/Hilux/RAV4/Avanza, año, versión y kilometraje preventivo). | Validación de placa duplicada `409`, alerta preventiva si el nuevo km es menor al anterior. | `backend-crm`, `frontend-web` |
| **USC03** | **MUST** | Catálogo maestro de repuestos OEM Toyota y equivalentes (`DENSO`, `AISIN`, `ALTERNATIVO`) vinculados por `oem_reference_code` y compatibilidad por Modelo + Rango de Años + Versión. | Búsqueda multicriterio `< 500 ms` filtrando compatibilidad exacta con el vehículo seleccionado. | `backend-crm`, `frontend-web` |
| **USC04** | **MUST** | Visibilidad inmediata de stock físico y semáforo híbrido (`CRITICO`, `ALERTA`, `NORMAL`) durante la atención en mostrador. | Indicador en tiempo real y visualización instantánea de equivalentes disponibles si el OEM está en stock `0`. | `backend-crm`, `frontend-web` |
| **USC08** | **MUST** | Tarifario estándar de mano de obra por tipo de servicio/mantenimiento Toyota (precios con IGV 18% incluido). | CRUD para `ADMIN` y selector rápido en el cotizador. | `backend-crm`, `frontend-web` |
| **USC05** | **MUST** | **Cotizador Express POS en 1 pantalla (`<= 3 pasos`, `< 3 min`)**: cálculo automático con IGV 18% incluido (desglosando base e IGV), descuentos con tope por rol (`5%` Recepcionista / `15%` Admin). | Flujo fluido en `1366x768` sin scroll horizontal; edición permitida mientras esté `PENDIENTE`. | `backend-crm`, `frontend-web` |
| **USC06** | **MUST** | Exportación de proforma PDF profesional (`OpenPDF < 3s`) con correlativo `COT-2026-XXXXX`, vigencia **7 días**, leyenda sin valor tributario SUNAT y **marca blanca configurable** (`workshop_settings`). | Descarga `< 3s` + hoja `@media print` como fallback de impresión directa. | `backend-crm`, `frontend-web` |
| **USC07** | **MUST** | Máquina de estados atómica (`PENDIENTE -> APROBADA -> CERRADA | RECHAZADA`) con `@Transactional` y `@Version`. | `APROBADA` descuenta stock (bloquea con `409` detallando faltantes si no alcanza, sin rechazar automático); `APROBADA -> RECHAZADA` reintegra stock al almacén. | `backend-crm`, `frontend-web` |
| **EXT02** | **SHOULD** *(Sprint 1)* | **Envío rápido de resumen de cotización por WhatsApp (`wa.me`)** prellenado con cliente, placa, ítems, total `S/` y vigencia 7 días. | Abre WhatsApp con mensaje formateado en 1 clic desde el cotizador o historial. | `frontend-web` |
| **EXT03-A** | **MUST** *(Sprint 1)* | Captura obligatoria de `rejection_reason` (`FALTA_STOCK`, `PRECIO`, `TIEMPO_ESPERA`, `OTRO`) al rechazar cualquier cotización. | Validación `400` si se intenta rechazar sin motivo; trazabilidad lista en BD. | `backend-crm`, `frontend-web` |
| **USC09 + USC10** | **MUST** | Pipeline de demanda intermitente + servicio FastAPI `RandomForestRegressor` de Jorge integrado vía `MlForecastClient` (con fallback Mock activo mientras se conecta URL productiva). | Inferencia batch persistiendo en `demand_forecasts` con métricas `R2 >= 0.80`, `WAPE <= 15%`, `RMSE` y `MAPE` (`demanda > 0`). | `ml-service`, `backend-crm` |
| **USC11** | **MUST** | Dashboard de alertas preventivas de reabastecimiento leyendo `demand_forecasts` en `< 500 ms` + botón Admin *"Recalcular Demanda"*. | Semáforo accionable con demanda proyectada y `reorder_point`. | `backend-crm`, `frontend-web` |
| **RNF01, RNF02, RNF04** | **MUST** | Rendimiento (`< 2s` API, `< 500ms` alertas, `< 3s` PDF), Resiliencia (autoguardado LocalStorage cada 5s + `@media print`) y Despliegue Cloud HTTPS temprano. | MVP accesible en URL pública para entrevistas del miércoles 25. | Todos |

---

### SPRINT 2 — Mejoras Inteligentes de Mostrador, Cobertura >= 95%, Pulido Post-Entrevistas y Cierre (Viernes 27 – Domingo 29 Nov)

| ID | Prioridad MoSCoW | Requisito / Mejora | Criterio clave de Aceptación (DoD) | Repo Principal |
| :--- | :---: | :--- | :--- | :--- |
| **EXT01** | **SHOULD** | **Sugerencia Inteligente de Kits Preventivos Toyota por Modelo y Kilometraje** (ej. Yaris 10k / 20k / 40k km precarga filtros compatibles, aceite e ítem de mano de obra en 1 clic). | Al elegir placa, sugiere el próximo paquete según `current_mileage_km` y permite cargarlo/editarlo en el carrito POS. | `backend-crm`, `frontend-web` |
| **EXT04** | **SHOULD** | **Formulario rápido de Ingreso de Compras / Reposición de Stock** (`ALMACEN` / `ADMIN`) con proveedor, documento referencia y actualización en kardex `inventory_movements`. | Recarga stock físico en segundos y actualiza el semáforo de rojo a verde en vivo. | `backend-crm`, `frontend-web` |
| **EXT03-B** | **COULD** | **Analítica de Ventas Perdidas por Motivo de Rechazo** (KPI de `S/` no facturados por `FALTA_STOCK` vs. `PRECIO` / `TIEMPO_ESPERA`). | Tarjeta ejecutiva en Dashboard Admin que demuestra el ROI directo del modelo Random Forest. | `backend-crm`, `frontend-web` |
| **QA-FULL** | **MUST** | Suite completa de pruebas automatizadas (**JaCoCo >= 95%** en dominio/servicios Java, Vitest en Vue, Pytest en FastAPI) + Colección Postman v2.1 auditada por Aaron + corrección de feedback de entrevistas del miércoles. | 100% de pipelines en verde, evidencias en `/docs` y entorno productivo blindado para Semana 15. | Todos |
