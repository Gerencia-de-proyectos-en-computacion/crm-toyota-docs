---
trigger: glob
globs: "**/backend-crm/**"
description: "Reglas de arquitectura, seguridad, concurrencia y pruebas para el backend Java 21 + Spring Boot 3.x."
---

# Reglas de Ingeniería — Backend CRM (`backend-crm`)

1. **Stack y Estilo Java 21:**
   - Usar `record` inmutables para todos los Request/Response DTOs con anotaciones `@Valid` (`jakarta.validation.constraints.*`).
   - Prohibido exponer entidades JPA directamente en Controllers.
   - Manejo global de excepciones con `@RestControllerAdvice` devolviendo `ProblemDetail` (RFC 7807) claro para mostrador (especialmente `409 Conflict` en quiebre de stock o colisión `@Version` `ObjectOptimisticLockingFailureException`).

2. **Base de Datos, Flyway y Concurrencia:**
   - Todo cambio DDL vive en `src/main/resources/db/migration/V*__*.sql` (Flyway). `spring.jpa.hibernate.ddl-auto=validate`.
   - Mantener índices B-Tree explícitos en `customers(document_number)`, `vehicles(license_plate)`, `parts(sku)`, `parts(oem_reference_code)` y `quotes(quote_number, status)`.
   - La entidad `Part` debe incluir `@Version private Long version;`. El paso a `APROBADA` y la reversión `APROBADA -> RECHAZADA` deben ejecutarse en métodos `@Transactional` registrando trazabilidad en `inventory_movements`.

3. **Seguridad y Cumplimiento Ley N.º 29733:**
   - Roles Spring Security: `ROLE_ADMIN`, `ROLE_RECEPCIONISTA`, `ROLE_ALMACEN`.
   - Passwords con `BCryptPasswordEncoder`. JWT stateless de 14h (jornada completa de taller) con endpoint de renovación silenciosa `/api/v1/auth/refresh`.
   - Rechazar alta de cliente si `dataConsentAccepted != true`.

4. **Integración ML Desacoplada y PDF:**
   - Consumir el servicio ML a través de la interfaz `MlForecastClient` con implementaciones intercambiables por propiedad (`crm.ml.mode=mock|http`) y header `X-Internal-API-Key`.
   - Generación de proformas con **OpenPDF** en memoria (`ByteArrayOutputStream`) en `< 3 segundos`, leyendo datos del taller desde `workshop_settings` (marca blanca).

5. **Calidad y Docs-as-Code:**
   - Cobertura objetivo **>= 95%** (JaCoCo) sobre `service` y `domain` con JUnit 5 + Mockito, más pruebas de contrato REST con MockMvc.
   - Todo cambio en endpoints o migraciones debe reflejarse en `backend-crm/docs/`.
