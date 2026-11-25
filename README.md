# `crm-toyota-docs` — Gobernanza Scrum, Trazabilidad PMBOK, QA Postman y Orquestación

Repositorio transversal del proyecto **CRM-TOYOTA** (Organización `Gerencia-de-proyectos-en-computacion`).

## Contenido Auditado (Docs-as-Code)
- **[Guía Integral del Equipo y Defensa Oral (Empieza aquí — Jorge y Aaron)](./docs/TEAM_ONBOARDING_AND_DEFENSE_GUIDE.md)**
- [Product Backlog Priorizado (MoSCoW) — Sprint 1 y Sprint 2](./docs/PRODUCT_BACKLOG_MOSCOW.md)
- [Decisiones Arquitectónicas y Reglas de Negocio (ADR-001 a ADR-006)](./docs/BUSINESS_RULES_AND_ADRS.md)
- [Matriz Viva de Trazabilidad PMBOK ↔ Código y Doble Presupuesto](./docs/PMBOK_CODE_TRACEABILITY.md)
- [Contrato Cerrado Backend ↔ Microservicio ML](./docs/ML_SERVICE_CONTRACT_DRAFT.md)
- [Guía de Prueba Local End-to-End](./docs/LOCAL_DEMO_GUIDE.md)
- [Guía de Despliegue Cloud Ruta B (`S/ 0.00`)](./docs/CLOUD_DEPLOYMENT_GUIDE.md)
- **[Ficha de Entrevista en Talleres Toyota — Plantilla Imprimible (MD)](./docs/FICHA_ENTREVISTA_TALLERES.md) | [(PDF listo para imprimir)](./docs/FICHA_ENTREVISTA_TALLERES.pdf)**
- [Colección Exportable Postman v2.1 (Auditoría QA Aaron)](./qa/postman/CRM-TOYOTA.postman_collection.json)
- [Entorno Postman Producción (Render + Vercel)](./qa/postman/CRM-TOYOTA-Prod.postman_environment.json)

## URLs Activas en Producción (Piloto)
- **Frontend Web (Vercel):** `https://crm-toyota-frontend.vercel.app`
- **Backend API (Render + Supabase):** `https://crm-toyota-backend.onrender.com` (Swagger: `/swagger-ui.html`)
- **Microservicio ML (Render FastAPI):** `https://crm-toyota-ml.onrender.com` (Health: `/health`, Metrics: `/metrics`)
