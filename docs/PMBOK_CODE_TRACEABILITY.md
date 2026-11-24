# Matriz Viva de Trazabilidad: Informe PMBOK ↔ Código Fuente y Doble Presupuesto

Este documento responde en segundos cualquier consulta de auditoría oral del docente (*"¿Esto está implementado?", "¿Dónde está la función que mencionan en el informe?"*).

## 1. Mapa Funcional Exacto

| Código | Función Prometida | Estado Backend | Estado Frontend | Ruta Exacta en Código Backend | Endpoint REST | Prueba que lo Valida |
| :--- | :--- | :---: | :---: | :--- | :--- | :--- |
| **RNF03** *(ex-USC12)* | Seguridad RBAC (`ADMIN`, `RECEPCIONISTA`, `ALMACEN`), JWT 14h + BCrypt | **LISTO** | **LISTO** (`LoginView.vue`, `auth.ts`) | `backend-crm/.../security/SecurityConfig.java`, `JwtService.java`, `AuthService.java` | `POST /api/v1/auth/login`, `/refresh` | `CrmToyotaFullFlowIntegrationTest`, `posAndAuth.spec.ts` |
| **USC01 / RNF03** | Alta Clientes DNI/RUC + Consentimiento Ley N.º 29733 | **LISTO** | **LISTO** (`CustomersVehiclesView.vue`, `CustomerVehicleQuickModal.vue`) | `backend-crm/.../service/CustomerVehicleService.java` | `GET/POST/PUT /api/v1/customers` | `CrmToyotaFullFlowIntegrationTest` + `ServiceEdgeCasesCoverageTest` |
| **USC02** | Ficha Vehicular Toyota (Placa MTC, Modelo, Año, Versión, Km preventivo) | **LISTO** | **LISTO** (`CustomersVehiclesView.vue`) | `backend-crm/.../service/CustomerVehicleService.java` | `GET/POST/PUT /api/v1/vehicles` | `CrmToyotaFullFlowIntegrationTest` |
| **USC03 / USC04** | Catálogo OEM + Equivalentes (`oem_reference_code`) + Semáforo Híbrido | **LISTO** | **LISTO** (`PosCounterView.vue`, `InventoryForecastView.vue`) | `backend-crm/.../service/CatalogInventoryService.java`, `StockSemaphorePolicy.java` | `GET/POST/PUT /api/v1/parts` | `CrmToyotaFullFlowIntegrationTest` |
| **USC08** | Tarifario estándar de mano de obra Toyota (inc. IGV) | **LISTO** | **LISTO** (`PosCounterView.vue`) | `backend-crm/.../service/CatalogInventoryService.java` | `GET/POST /api/v1/labor-rates` | `ServiceEdgeCasesCoverageTest` |
| **USC05 / EXT02** | Cotizador POS (`< 3 min`), IGV 18% incluido, Descuentos rol (`5%`/`15%`) + WhatsApp `wa.me` | **LISTO** | **LISTO** (`PosCounterView.vue`, `quoteDraft.ts`) | `backend-crm/.../service/QuoteService.java` | `POST/PUT /api/v1/quotes` | `CrmToyotaFullFlowIntegrationTest`, `posAndAuth.spec.ts` |
| **USC06** | Exportación PDF `< 3s` (OpenPDF) + Marca Blanca (`workshop_settings`) | **LISTO** | **LISTO** (`PosCounterView.vue`, `SettingsView.vue`) | `backend-crm/.../service/QuotePdfService.java`, `WorkshopSettingsService.java` | `GET /api/v1/quotes/{id}/pdf` | `CrmToyotaFullFlowIntegrationTest` |
| **USC07 / EXT03** | Máquina de estados atómica (`@Version`), bloqueo `409` con equivalentes, reversión de stock y ventas perdidas | **LISTO** | **LISTO** (`QuotesManagementView.vue`, `StockConflictModal.vue`, `RejectQuoteModal.vue`) | `backend-crm/.../service/QuoteService.java`, `InsufficientStockException.java` | `POST /api/v1/quotes/{id}/approve`, `/substitute-part`, `/reject`, `/close` | `CrmToyotaFullFlowIntegrationTest`, `posAndAuth.spec.ts` |
| **EXT01** | Sugerencia inteligente de Kits Preventivos Toyota por Modelo y Km | **LISTO** | **LISTO** (`PosCounterView.vue`) | `backend-crm/.../service/CatalogInventoryService.java` (`suggestKitForVehicle`) | `GET /api/v1/maintenance-kits/suggest` | `CrmToyotaFullFlowIntegrationTest`, `posAndAuth.spec.ts` |
| **EXT04** | Ingreso rápido de compras/reposición de stock y kardex | **LISTO** | **LISTO** (`InventoryForecastView.vue`) | `backend-crm/.../service/CatalogInventoryService.java` (`replenishStock`) | `POST /api/v1/inventory/replenishments` | `CrmToyotaFullFlowIntegrationTest` |
| **USC09–USC11** | Random Forest Batch (`R2: 0.9682`, `WAPE: 9.76%`) + Alertas `< 500ms` | **LISTO** | **LISTO** (`InventoryForecastView.vue`) | `ml-service/app/main.py`, `backend-crm/.../ForecastDashboardService.java` | `GET /api/v1/forecasts/dashboard`, `POST /recalculate` | `test_service.py` (Pytest), `RoutingMlForecastClientContractTest` |

---

## 2. Inventario del Doble Presupuesto (Ruta B Cloud Sin Azure — Auditoría Aaron)

| Componente / Herramienta | Categoría | Licencia | Costo Real Ejecutado (Piloto Académico) | Costo Ideal Comercial (Producción Taller PYME) |
| :--- | :--- | :--- | :--- | :--- |
| Java 21 (Temurin) + Spring Boot 3.4 + OpenPDF 2.0 | Backend & PDF | GPLv2+CE / Apache 2.0 / MPL 2.0 | `S/ 0.00` | `S/ 0.00` (Open Source empresarial) |
| Vue 3 + Vite + Pinia + Tailwind CSS + VueUse | Frontend SPA | MIT | `S/ 0.00` | `S/ 0.00` (Open Source empresarial) |
| Python 3.11 (`uv`) + FastAPI + scikit-learn + pandas | Motor ML | PSF / MIT / BSD-3 | `S/ 0.00` | `S/ 0.00` (Open Source empresarial) |
| Supabase PostgreSQL (`cowbnhbvctewgznicrde`, TDE AES-256) | Base de Datos Cloud | Managed Cloud | `S/ 0.00` (Free Tier) | ~`USD 25/mes` (`S/ 95/mes` — Supabase Pro con PITR y backups diarios) |
| Vercel Hosting CDN + TLS | Frontend Cloud | Managed Cloud | `S/ 0.00` (Hobby Tier) | ~`USD 20/mes` (`S/ 76/mes` — Vercel Pro comercial) |
| Contenedores Backend + ML (Ruta B: HF Spaces Docker / Render) | Compute Cloud | Managed Docker PaaS | `S/ 0.00` (Free Tier Docker — Azure descartado) | ~`USD 35–50/mes` (`S/ 135–190/mes` — Instancias dedicadas sin cold start) |
