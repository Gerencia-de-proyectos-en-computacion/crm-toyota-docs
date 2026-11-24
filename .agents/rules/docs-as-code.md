---
trigger: always_on
description: " Garantiza Docs-as-Code vivo dentro de cada repositorio independiente y trazabilidad con el informe del curso."
---

# Estándar Docs-as-Code Multi-Repositorio

Cada uno de los repositorios de la organización (`backend-crm`, `frontend-web`, `ml-service` y `project-docs`) es una unidad auditada de forma independiente:

1. **Sincronización obligatoria en `/docs` de cada repo:**
   - Si creas o modificas un endpoint, tabla Flyway o regla en `backend-crm`, actualiza `backend-crm/docs/` (`API_ENDPOINTS.md`, `DATABASE_ERD.md`, `ARCHITECTURE.md`).
   - Si creas o modificas una vista, store o variable en `frontend-web`, actualiza `frontend-web/docs/`.
   - Si ajustas features, modelo o endpoints en `ml-service`, actualiza `ml-service/docs/`.
2. **Trazabilidad Defensa Oral (Profesor / Auditoría QA):**
   - Toda funcionalidad entregada debe quedar mapeada en `project-docs/audit/PMBOK_CODE_TRACEABILITY.md` indicando archivo exacto, endpoint y pantalla donde vive, para responder en segundos cualquier pregunta tipo *"¿dónde está implementada esta función del documento?"*.
