---
trigger: model_decision
description: "Aplicar al redactar o actualizar entregables Scrum, trazabilidad USC/RNF, colección Postman QA o inventario del doble presupuesto Real vs Ideal."
---

# Gobernanza Scrum, QA y Doble Presupuesto (`project-docs`)

1. **Trazabilidad de Requisitos:**
   - Mantener alineación explícita entre historias `USC01–USC11` (+ mejoras de mostrador `EXT01–EXT04`), requisitos no funcionales `RNF01–RNF04` (latencia `< 2s`, PDF `< 3s`, Ley N.º 29733 + JWT/BCrypt, layout `1366x768` + autoguardado LocalStorage) y casos de prueba.
2. **Auditoría QA para Aaron:**
   - Cualquier cambio en contratos REST debe mantener exportable la colección Postman v2.1 con pruebas `pm.test` para los 3 roles (`ADMIN`, `RECEPCIONISTA`, `ALMACEN`) y casos borde (`400`, `401`, `403`, `409 Conflict`).
3. **Doble Presupuesto de Software e Infraestructura:**
   - Registrar cada dependencia y servicio cloud en dos columnas comparativas:
     - **Presupuesto Real Ejecutado (Piloto Académico):** Open Source (MIT/Apache 2.0) + capas gratuitas / créditos estudiantiles (`S/ 0.00` desembolso cloud).
     - **Presupuesto Ideal Comercial (Producción Taller PYME):** Tiers gestionados Pro con SLA comercial, backups dedicados y horas-hombre de ingeniería según línea base PMBOK.
