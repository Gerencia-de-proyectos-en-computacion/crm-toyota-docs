---
name: repo-docs-sync
description: >-
  Sincroniza la documentación Docs-as-Code individual de cada repositorio (backend-crm,
  frontend-web, ml-service) junto con la matriz de trazabilidad PMBOK-Código, actas
  Scrum y el inventario de doble presupuesto en project-docs.
---

# Skill: Sincronización Docs-as-Code Multi-Repo y Defensa PMBOK

Ejecuta esta skill al cerrar funcionalidades o preparar evidencia de Sprint:

## 1. Docs-as-Code Local de cada Repositorio
Verifica que cada repositorio afectado tenga al día su carpeta `docs/` y su `README.md`:
- `backend-crm/docs/`: `ARCHITECTURE.md`, `DATABASE_ERD.md` (Mermaid ERD sincronizado con Flyway), `API_ENDPOINTS.md` y `TESTING_COVERAGE.md`.
- `frontend-web/docs/`: `UI_ARCHITECTURE_1366x768.md`, `STATE_AND_RESILIENCE.md` y `WHITE_LABEL_CONFIG.md`.
- `ml-service/docs/`: `CRISP_DM_PIPELINE.md`, `METRICS_INTERMITTENT_DEMAND.md` y `API_CONTRACT.md`.

## 2. Matriz Anti-Preguntas Sorpresa (`project-docs/audit/PMBOK_CODE_TRACEABILITY.md`)
Actualiza la tabla que vincula cada requisito (`USC01–USC11`, `RNF01–RNF04`, `EXT01–EXT04`) y cada afirmación clave del informe PMBOK con:
- **Repositorio y ruta exacta del archivo** donde está implementado.
- **Endpoint / Pantalla** demostrable en vivo.
- **Test automatizado** que lo respalda.

## 3. Inventario del Doble Presupuesto
Si se añadió una librería o servicio cloud nuevo, regístralo en `project-docs/audit/DOUBLE_BUDGET_INVENTORY.md` detallando licencia, costo real académico (`S/ 0.00` / créditos) y costo equivalente comercial mensual/anual.
