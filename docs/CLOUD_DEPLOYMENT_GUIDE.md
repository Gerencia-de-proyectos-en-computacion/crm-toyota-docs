# Guía Oficial de Despliegue en la Nube (`S/ 0.00` — Ruta B sin Azure)

## ¿Por qué NO Netlify para el Backend Java / ML Python?
**Netlify** y **Vercel** son plataformas CDN + Serverless para Frontends y funciones efímeras Node/Go; **no alojan contenedores Docker persistentes de Java 21 (Spring Boot) ni procesos FastAPI con modelos `.joblib` cargados en memoria**.
Por ello la topología productiva ideal y 100% gratuita es:

| Capa | Servicio Cloud (`S/ 0.00`) | Estado Actual |
| :--- | :--- | :--- |
| **Base de Datos PostgreSQL 17** | **Supabase** (`cowbnhbvctewgznicrde` — `us-west-2`) | **YA APROVISIONADA Y CON SEED + RLS ACTIVO** |
| **Frontend Web (`Vue 3`)** | **Vercel** (`crm-toyota-frontend`) | **DESPLEGADO POR CLI / CONECTADO A GITHUB** |
| **Backend (`Spring Boot 3.4`)** | **Render.com Free Docker** *o* **Hugging Face Spaces (Docker SDK — 16 GB RAM gratis)** | **`Dockerfile` + `render.yaml` LISTOS EN GITHUB** |
| **Microservicio ML (`FastAPI`)** | **Render.com Free Docker** *o* **Hugging Face Spaces (Docker SDK — 16 GB RAM gratis)** | **`Dockerfile` + `render.yaml` LISTOS EN GITHUB** |

---

## Paso 1: Obtener la Cadena JDBC Pooler de tu Supabase (`1 minuto`)
1. Entra a tu panel de Supabase del proyecto `InjustShin's Project` (`cowbnhbvctewgznicrde`) ➔ botón superior **Connect** ➔ pestaña **JDBC** ➔ modo **Session Pooler (IPv4 compatible, puerto `5432`)** o **Transaction Pooler (puerto `6543` agregando `?prepareThreshold=0`)**:
   - `DATABASE_URL`: `jdbc:postgresql://aws-0-us-west-2.pooler.supabase.com:6543/postgres?prepareThreshold=0&sslmode=require` *(verifica el host exacto que muestra tu modal Connect en Supabase)*
   - `DATABASE_USERNAME`: `postgres.cowbnhbvctewgznicrde`
   - `DATABASE_PASSWORD`: *(la contraseña que definiste al crear tu proyecto Supabase)*

---

## Paso 2: Desplegar `crm-toyota-ml` y `crm-toyota-backend` (Elige Opción A o B — 3 minutos)

### Opción A (Recomendada por potencia — 16 GB RAM gratis, sin tarjeta): Hugging Face Spaces Docker
1. Crea cuenta gratis en `https://huggingface.co` ➔ **New Space**:
   - **Space 1 (`crm-toyota-ml`):** Elige **Docker (Blank)** ➔ Público ➔ Sube o sincroniza el repo `Gerencia-de-proyectos-en-computacion/crm-toyota-ml`. En *Settings > Variables and secrets*, agrega secret `ML_INTERNAL_API_KEY = crm-toyota-internal-ml-key-2026`. Tu URL pública será `https://<tu-usuario>-crm-toyota-ml.hf.space`.
   - **Space 2 (`crm-toyota-backend`):** Elige **Docker (Blank)** ➔ Público ➔ Sincroniza `Gerencia-de-proyectos-en-computacion/crm-toyota-backend`. En *Settings > Variables and secrets*, agrega:
     - `DATABASE_URL` = *(tu cadena JDBC Pooler del Paso 1)*
     - `DATABASE_USERNAME` = `postgres.cowbnhbvctewgznicrde`
     - `DATABASE_PASSWORD` = *(tu clave de BD Supabase)*
     - `ML_MODE` = `http`
     - `ML_SERVICE_URL` = `https://<tu-usuario>-crm-toyota-ml.hf.space`
     - `ML_INTERNAL_API_KEY` = `crm-toyota-internal-ml-key-2026`
     - `CORS_ALLOWED_ORIGINS` = `https://*.vercel.app,http://localhost:5173`

### Opción B (Conexión directa en 2 clics con tu Organización GitHub): Render.com Free Docker
1. Entra en `https://dashboard.render.com` con tu cuenta GitHub (`InjustShin`).
2. Clic en **New + ➔ Blueprint** (o **Web Service**):
   - Conecta `Gerencia-de-proyectos-en-computacion/crm-toyota-ml` (detecta automáticamente `render.yaml` y `Dockerfile`). Copia su URL `https://crm-toyota-ml-xxxx.onrender.com`.
   - Conecta `Gerencia-de-proyectos-en-computacion/crm-toyota-backend` y pega `DATABASE_URL`, `DATABASE_USERNAME`, `DATABASE_PASSWORD` y `ML_SERVICE_URL`.

---

## Paso 3: Conectar la URL del Backend en Vercel (`30 segundos`)
Una vez tengas la URL HTTPS de `crm-toyota-backend` (ej. `https://...-crm-toyota-backend.hf.space` o `https://crm-toyota-backend.onrender.com`), ejecuta en PowerShell dentro de `crm-toyota\frontend-web`:

```powershell
vercel env add VITE_API_BASE_URL production
# Pega la URL HTTPS de tu backend (sin barra final)
vercel --prod
```
¡Y tu URL de Vercel queda 100% conectada en vivo a Backend + ML + Supabase!
