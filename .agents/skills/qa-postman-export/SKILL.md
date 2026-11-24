---
name: qa-postman-export
description: >-
  Genera y sincroniza la colección exportable de Postman v2.1 y entornos QA a partir
  de los endpoints de backend-crm y ml-service, incluyendo scripts pm.test de códigos
  HTTP, latencia (< 2000ms), RBAC por rol y casos de borde para la auditoría de Aaron.
---

# Skill: Sincronización de Colección Postman QA (`project-docs/qa`)

Activa esta skill al cerrar un bloque de endpoints o antes de cada entrega de hito:

## 1. Estructura de la Colección (`CRM-TOYOTA.postman_collection.json`)
Organiza las carpetas por flujo de negocio auditable:
1. `01 - Auth & Roles (JWT 14h)` — Login de `ADMIN`, `RECEPCIONISTA` y `ALMACEN` guardando `{{jwt_admin}}`, `{{jwt_recep}}`, `{{jwt_almacen}}` automáticamente en `pm.collectionVariables`.
2. `02 - Clientes (Ley 29733) & Vehículos Toyota` — Alta DNI/RUC, rechazo `400` sin consentimiento de datos, alta de placa y validación de duplicados `409`.
3. `03 - Catálogo OEM, Equivalentes, Kits & Reposición Stock` — Consulta `< 500ms`, filtro por modelo/año/versión, carga de kit preventivo e ingreso de compra de almacén.
4. `04 - Cotizador Express, Stock Atómico & PDF` — Crear `PENDIENTE`, editar, bloqueo `409` por sobreventa, aprobar (`APROBADA` descuenta stock), revertir a `RECHAZADA` con `rejection_reason` (reintegra stock), cerrar (`CERRADA`) y descargar PDF (`< 3000ms`, `Content-Type: application/pdf`).
5. `05 - Forecast ML Batch & Dashboard Alertas` — `GET /health` ML, disparo `POST /api/v1/forecasts/recalculate` y lectura de alertas `< 500ms` con métricas `R2 >= 0.80` y `WAPE <= 15%`.

## 2. Assertions Obligatorias en cada Request
Incluye scripts `pm.test` verificando:
- Código HTTP esperado.
- Tiempo de respuesta (`pm.expect(pm.response.responseTime).to.be.below(2000)` o `3000` en PDF).
- Presencia de campos clave del contrato JSON.
