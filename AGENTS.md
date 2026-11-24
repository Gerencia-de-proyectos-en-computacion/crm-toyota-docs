# CRM-TOYOTA — Constitución Global del Workspace (Multi-Repo)

Proyecto: **CRM de Ventas Web basado en Random Forest Regressor para Talleres Automotrices Pequeños especializados en Toyota (`CRM-TOYOTA`)**.

## 1. Topología Multi-Repositorio (Organización GitHub)
Este workspace limpio agrupa los 4 repositorios independientes de la organización para desarrollo e integración local coordinada:
- `backend-crm/` (`crm-toyota-backend`): Java 21 + Spring Boot 3.x + Spring Security (JWT 14h + BCrypt) + Spring Data JPA + Flyway + PostgreSQL 16 (Supabase en nube) + OpenPDF + Springdoc OpenAPI + JUnit 5/Mockito/JaCoCo (>= 95% en lógica de dominio).
- `frontend-web/` (`crm-toyota-frontend`): Vue 3 (`<script setup lang="ts">` estricto) + Vite + Pinia + TypeScript + Tailwind CSS + `@vueuse/core` + Vitest. Despliegue en **Vercel**.
- `ml-service/` (`crm-toyota-ml`): Python 3.11 (`uv`) + FastAPI + scikit-learn (`RandomForestRegressor`) + pandas + joblib + Pytest. Despliegue contenedor Docker gratuito (**Ruta B: Hugging Face Spaces Docker / Render**, Azure descartado).
- `backend-crm/` despliegue: Contenedor Docker gratuito (**Ruta B: Hugging Face Spaces Docker / Render**) conectado a **Supabase PostgreSQL** (`cowbnhbvctewgznicrde`).
- `project-docs/` (`crm-toyota-docs`): Artefactos transversales Scrum, trazabilidad PMBOK, contratos integrados, colección Postman QA y doble presupuesto (Real vs. Ideal comercial).

## 2. Invariantes de Negocio Innegociables
1. **Alcance Fase 1 (Mostrador):** Clientes (DNI/RUC + consentimiento Ley N.º 29733), Vehículos Toyota (Yaris, Corolla, Hilux, RAV4, Avanza con año, versión y km preventivo), Catálogo OEM + Equivalentes (`oem_reference_code`: Toyota OEM, Denso, Aisin, Alternativo), Kits preventivos sugeridos por km, Tarifas de Mano de Obra, Cotizador POS (`1366x768` sin scroll horizontal, `< 3 min`), PDF `< 3s`, WhatsApp `wa.me`, Reposición rápida de stock y Dashboard de Alertas ML.
2. **Precios e Impuestos (Perú):** Moneda `S/`. Los precios de catálogo **ya incluyen IGV (18%)**. El sistema calcula descuentos según tope de rol (`RECEPCIONISTA` <= 5%, `ADMIN` <= 15%) y desglosa informativamente Base Imponible (`Total / 1.18`) e IGV en UI y PDF. Vigencia de proforma: **7 días calendario** (`COT-2026-XXXXX`, sin valor tributario SUNAT).
3. **Máquina de Estados y Stock Atómico (`@Transactional` + `@Version`):**
   - `PENDIENTE` (editable, sin tocar stock) -> `APROBADA` (descuenta stock atómicamente; si falta stock de cualquier ítem, **bloquea con HTTP 409** detallando el quiebre sin pasar a rechazada) | `RECHAZADA` (no altera stock, exige `rejection_reason`: `FALTA_STOCK`, `PRECIO`, `TIEMPO_ESPERA`, `OTRO`).
   - `APROBADA` -> `CERRADA` (terminal) | `RECHAZADA` (reversión permitida: exige motivo y **reintegra atómicamente el stock** registrando movimiento en kardex).
4. **Inferencia ML Batch:** Nunca síncrona al cotizar. Corre semanalmente o vía botón Admin *"Recalcular Demanda"*, persistiendo en `demand_forecasts`. Lectura desde Spring Boot `< 500 ms`. Métricas para demanda intermitente (`ADI >= 1.32`): `R2 >= 0.80`, `WAPE <= 15%` (principal), `RMSE`, y `MAPE` solo donde `demanda > 0`.
5. **Marca Blanca Configurable (`workshop_settings`):** Nombre comercial, RUC, dirección, teléfono y lema del taller nunca van hardcodeados; usan configuración dinámica con placeholder neutro por defecto para demos multi-taller.

## 3. Regla Estricta Docs-as-Code por Repositorio
Cada repositorio (`backend-crm/docs/`, `frontend-web/docs/`, `ml-service/docs/`) mantiene su propia documentación viva sincronizada en el mismo commit o bloque de trabajo donde cambie código, endpoints, variables de entorno o esquemas.

## 4. Entorno y Protocolo de Cierre de Respuesta
- Entorno Windows x64 + PowerShell: usar rutas y comandos nativos PowerShell (`.\mvnw.cmd`, `uv`, `npm`).
- **Obligatorio al final de CADA respuesta al usuario:** Incluir siempre una sección breve **"Recomendación de Modelo IA para el siguiente prompt"**, indicando si conviene **Carbon**, **Neon** o **Hydrogen** (nunca Argon) y si activar o no **Modo Orquestador**, justificando en 1-2 líneas según la tarea que sigue.
