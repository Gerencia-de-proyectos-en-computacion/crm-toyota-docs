# Decisiones Arquitectónicas y Reglas de Negocio Cerradas (ADRs)

## ADR-001: Estrategia Multi-Repositorio con Orquestación Local Limpia
- **Decisión:** 1 Organización en GitHub con 4 repositorios independientes (`crm-toyota-backend`, `crm-toyota-frontend`, `crm-toyota-ml`, `crm-toyota-docs`), agrupados localmente bajo `crm-toyota/` y con **Docs-as-Code propio en cada repositorio (`<repo>/docs/`)**.
- **Justificación:** Evita bloqueos y conflictos de Git entre el flujo Java/Vue (Farid), el flujo Python/ML (Jorge) y la auditoría QA/PMBOK (Aaron), permitiendo despliegues independientes por servicio.

## ADR-002: Precios con IGV Incluido y Descuentos Gobernados por Rol
- **Regla Monetaria:** Moneda única Soles (`S/`). Todo precio de catálogo (`parts.sale_price_igv`, `labor_rates.price_igv`) **incluye el 18% de IGV**.
- **Fórmula de Cotización:**
  - `gross_total = sum(qty * unit_price_igv)`
  - `discount_amount = round(gross_total * (discount_percent / 100), 2)`
  - `final_total_igv = gross_total - discount_amount`
  - `taxable_subtotal = round(final_total_igv / 1.18, 2)` *(informativo en UI y PDF)*
  - `igv_amount = final_total_igv - taxable_subtotal` *(informativo en UI y PDF)*
- **Topes de Descuento:** `RECEPCIONISTA` hasta **5.00%**; `ADMIN` hasta **15.00%**; `ALMACEN` no emite cotizaciones.

## ADR-003: Máquina de Estados de Cotización, Bloqueo Informativo y Reintegro de Stock
- **`PENDIENTE`:** Editable libremente; vigencia automática de **7 días calendario** (`COT-2026-00001`). No reserva ni descuenta stock físico.
- **`PENDIENTE -> APROBADA`:** Transacción atómica `@Transactional` con `@Version` en `Part`. Si `current_stock < requested_qty` en uno o más ítems, el backend **NO cambia el estado a `RECHAZADA`**; responde `409 Conflict` detallando qué repuesto falló, cuánto stock real queda y qué equivalentes (`oem_reference_code`) tienen stock disponible para sustitución inmediata.
- **`PENDIENTE -> RECHAZADA`:** Exige `rejection_reason` (`FALTA_STOCK`, `PRECIO`, `TIEMPO_ESPERA`, `OTRO`). No altera stock.
- **`APROBADA -> RECHAZADA` (Desistimiento / Anulación):** Permitido con `rejection_reason` obligatorio; **reintegra atómicamente el stock descontado** y deja asiento de auditoría en `inventory_movements`.
- **`APROBADA -> CERRADA`:** Cierre comercial de mostrador (estado final exitoso).

## ADR-004: Catálogo de Velocidad Extrema y Semáforo Híbrido de Inventario
- **Equivalencias Instantáneas (Opción 1):** Cada repuesto almacena `sku` propio y `oem_reference_code` común indexado por B-Tree, más compatibilidades por **Modelo Toyota + Rango de Años (`year_from`..`year_to`) + Versión/Motorización** (o `ALL`).
- **Semáforo Híbrido (`StockAlertLevel`):** Sea `effective_reorder = coalesce(demand_forecasts.reorder_point, parts.min_stock * 2)`:
  - `CRITICO` (Rojo): `current_stock == 0` OR `current_stock <= min_stock` OR (`reorder_point IS NOT NULL` AND `current_stock <= ceil(reorder_point * 0.5)`).
  - `ALERTA` (Amarillo): No es crítico y `current_stock <= effective_reorder`.
  - `NORMAL` (Verde): `current_stock > effective_reorder`.

## ADR-005: Sesión sin Fricción (14h + Sliding Refresh) y Marca Blanca (`workshop_settings`)
- **Sesión de Mostrador:** Token JWT de **14 horas** + renovación transparente en segundo plano cuando queden `< 2 horas` de vigencia durante actividad, evitando cierres bruscos frente al cliente.
- **Marca Blanca Comercial:** Tabla/endpoint `workshop_settings` con valores iniciales neutros (`"[Nombre del Taller Automotriz]"`, `"Especialidad Toyota"`, RUC referencial, dirección y teléfono), modificables en caliente por `ADMIN` para demostraciones ante distintos talleres sin recompilar código ni PDFs.


## ADR-006: Descarte Definitivo de Microsoft Azure y Adopción de Ruta B (100% Free Docker Cloud)
- **Contexto:** La suscripción académica *Azure for Students Starter* se encuentra expirada/deshabilitada y bloquea la reactivación inmediata.
- **Decisión:** Cancelar por completo el uso de Microsoft Azure y estandarizar el despliegue cloud sobre contenedores `Dockerfile` portables bajo **Ruta B (`S/ 0.00` y cero bloqueo administrativo)**:
  - **Frontend (`crm-toyota-frontend`):** Vercel (CDN global + HTTPS automático).
  - **Base de Datos:** Supabase PostgreSQL (`cowbnhbvctewgznicrde`, cifrado TDE AES-256 y RLS activo bloqueando acceso público directo a PostgREST).
  - **Backend (`crm-toyota-backend`) y ML (`crm-toyota-ml`):** Despliegue en contenedores Docker gratuitos (Hugging Face Spaces Docker SDK con 2 vCPU / 16 GB RAM o Render Free Web Service).
