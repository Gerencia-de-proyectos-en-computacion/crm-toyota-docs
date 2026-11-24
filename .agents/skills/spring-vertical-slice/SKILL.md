---
name: spring-vertical-slice
description: >-
  Implementa un módulo vertical completo en backend-crm (Spring Boot 3 + Java 21):
  migración Flyway con índices B-Tree, entidad JPA (@Version cuando aplique),
  Repository, Service @Transactional, DTOs record con Bean Validation, Controller
  documentado con Springdoc OpenAPI, tests JUnit 5/Mockito/MockMvc (cobertura >= 95%
  en lógica de negocio) y actualización Docs-as-Code del repositorio backend.
---

# Skill: Spring Boot Vertical Slice (`backend-crm`)

Ejecuta esta secuencia cada vez que implementes o amplíes un dominio en `backend-crm`:

## 1. Persistencia y Migración Flyway
1. Añade o ajusta la migración numerada en `backend-crm/src/main/resources/db/migration/V<N>__<descripcion>.sql`.
2. Incluye `CONSTRAINTS` explícitos e índices B-Tree en columnas de búsqueda frecuente (`document_number`, `license_plate`, `sku`, `oem_reference_code`, `quote_number`).
3. Crea la entidad JPA en `domain/entity/`. Si la tabla maneja stock concurrente (`parts`), agrega `@Version private Long version;`.

## 2. Lógica de Dominio y Transaccionalidad
1. Crea `record` DTOs de entrada/salida con `@NotBlank`, `@PositiveOrZero`, `@Pattern`, etc.
2. Implementa el `Service`:
   - Lecturas con `@Transactional(readOnly = true)`.
   - Escrituras críticas (`approveQuote`, `rejectQuote`, `registerStockReplenishment`) con `@Transactional`.
   - En quiebre de stock al aprobar, lanza `InsufficientStockException` (HTTP `409 Conflict`) con lista de repuestos faltantes y sus equivalentes `oem_reference_code` disponibles, **sin cambiar** el estado de la cotización.
   - Valida topes de descuento por rol (`RECEPCIONISTA <= 5%`, `ADMIN <= 15%`).

## 3. Exposición REST y OpenAPI
1. Crea el `@RestController` bajo `/api/v1/...` con `@PreAuthorize` explícito según la matriz RBAC (`ADMIN`, `RECEPCIONISTA`, `ALMACEN`).
2. Documenta cada endpoint con `@Tag`, `@Operation` y `@ApiResponse` (Springdoc).

## 4. Verificación (>= 95% Cobertura de Negocio) y Docs-as-Code
1. Escribe tests unitarios exhaustivos en `src/test/java/` cubriendo caminos felices, bordes y excepciones (`400`, `403`, `404`, `409`), más test `@WebMvcTest` del controlador.
2. Ejecuta `.\mvnw.cmd test jacoco:report` en PowerShell y verifica que pase en verde.
3. Actualiza `backend-crm/docs/API_ENDPOINTS.md` y `backend-crm/docs/DATABASE_ERD.md` si hubo cambios de contrato o tablas.
