# Guía Operativa de Prueba Local End-to-End (`CRM-TOYOTA`)

## 1. Arquitectura Local en Ejecución (3 Procesos Coordinados)

| Servicio | URL Local | Qué ejecuta internamente |
| :--- | :--- | :--- |
| **1. Microservicio ML (`ml-service`)** | `http://127.0.0.1:8000` (`/docs`) | FastAPI en Python 3.11.17 cargando `models/random_forest.joblib` (`R²: 0.9682`, `WAPE: 9.76%`, `ADI: 1.60`) protegido con `X-Internal-API-Key`. |
| **2. Backend CRM (`backend-crm`)** | `http://localhost:8080` (`/swagger-ui.html`) | Spring Boot 3.4 (Java 21) con perfil `local`: ejecuta automáticamente Flyway `V1` + `V2`, levanta seguridad JWT de 14h, OpenPDF y consume `ml-service` por HTTP real (`ML_MODE=http`). |
| **3. Frontend Mostrador (`frontend-web`)** | `http://localhost:5173` | SPA Vue 3 (`<script setup lang="ts">`) + Pinia + Tailwind Stitch `1366x768` conectada a `http://localhost:8080`. |

---

## 2. Guion Paso a Paso para Probar todas las Funciones (y Demostrar en Entrevistas)

Abre **`http://localhost:5173`** en tu navegador:

### Prueba A — Login de Turno y Marca Blanca (`RNF03` + `D4`)
1. Haz clic en el botón rápido **`Recepcionista`** (o **`Admin`**) y pulsa **Ingresar al Mostrador** (clave de los 3 usuarios demo: `Toyota2026!`).
2. Observa arriba el nombre placeholder **`[Nombre del Taller Automotriz]`**, tu rol, la estación (`Caja 02`) y la píldora verde de **autoguardado en LocalStorage cada 5s**.
3. Si entraste como **`Admin`**, ve al ícono de engranaje (**Marca Blanca** en `/configuracion`), cambia el nombre por el de cualquier taller piloto y guarda: verás cambiar al instante la barra superior y el membrete del PDF.

### Prueba B — Cotizador Express `< 3 min` + Sugerencia de Kit Preventivo + PDF + WhatsApp (`USC01–USC06`, `EXT01`, `EXT02`)
1. En **Cotizador POS (`/pos`)**, verás seleccionado el vehículo `ABC-123` (*Toyota Yaris 1.5 CVT • 2021 • 39,850 km*) y el banner celeste: **"Sugerencia Preventiva: Kit Mantenimiento 40,000 km Yaris"**.
2. Haz clic en **`Cargar Kit`**: en 1 segundo se precargan los 5 insumos compatibles + la mano de obra de 40k km, desglosando `Subtotal sin IGV`, `IGV 18% incluido` y `Total a Pagar` en Soles (`S/`).
3. Prueba escribir un descuento en **Descuento Mostrador (%)**: si eres `RECEPCIONISTA` el sistema lo topa automáticamente en `5%`; si eres `ADMIN` te permite hasta `15%`.
4. Haz clic en **`Descargar PDF`** (genera y descarga en `< 3s` la proforma formal con OpenPDF y leyenda sin valor tributario SUNAT) o en **`Enviar WhatsApp`** (abre `wa.me` con el resumen listo para el cliente).

### Prueba C — Bloqueo Atómico `HTTP 409`, Equivalentes en 1 Clic y Reintegro de Stock (`USC07`, `EXT03`)
1. En la barra izquierda entra a **Cotizaciones (`/cotizaciones`)**.
2. Ubica una cotización `PENDIENTE` con repuesto agotado (o crea una en POS agregando `AIS-PAD-0D1` que tiene stock `0`) y pulsa el botón rojo **`✓` (Aprobar)**:
   - Se abre el **Modal Error 409 • Conflicto de Stock en Almacén** (sin pasar la proforma a rechazada) mostrándote los equivalentes compatibles con stock (`TOYOTA OEM` y `DENSO`).
   - Pulsa **`Sustituir y Aprobar`**: reemplaza la pieza agotada, descuenta el stock atómicamente (`@Transactional` + `@Version`) y cambia el estado a `APROBADA`.
3. Sobre cualquier cotización `APROBADA`, pulsa el botón **`✕` (Anular / Rechazar)**: elige un motivo obligatorio (`Falta de Stock`, `Precio`, etc.) y confirma; el sistema **reintegra atómicamente el stock** al almacén.

### Prueba D — Dashboard Random Forest Batch y Reposición Rápida (`USC09–USC11`, `EXT04`)
1. Entra a **Inventario & ML (`/inventario-ml`)** como `Admin` o `Almacén`.
2. Revisa las 4 tarjetas KPI (**Riesgo Crítico**, **Alertas Preventivas**, **Ventas Perdidas por Falta de Stock** y **Tasa de Conversión**).
3. Pulsa **`Recalcular Demanda ML`** (como `Admin`): Spring Boot envía el lote HTTP al FastAPI de Jorge (`:8000/predict/batch`), consulta `:8000/metrics`, persiste en `demand_forecasts` y actualiza la cabecera en vivo a `FASTAPI_RF` (`R²: 0.97`, `WAPE: 9.8%`, `MAPE(>0): 9.1%`, `ADI: 1.6`).
4. En cualquier fila roja (`CRITICO`), pulsa **`+ Reponer Stock`**, ingresa proveedor y factura/guía, confirma y verás subir el stock y cambiar el semáforo a verde en vivo.

---

## 3. Comandos Manuales (Por si reinicias tu PC)

En 3 terminales PowerShell independientes:

```powershell
# Terminal 1 — Microservicio ML (Puerto 8000)
$env:ML_INTERNAL_API_KEY="crm-toyota-internal-ml-key-2026"
cd "c:\Users\farid\Documents\Cositas pa guardar\Universidad\Gerencia de proyectos en computación\crm-toyota\ml-service"
.\.venv\Scripts\python.exe -m uvicorn app.main:app --host 127.0.0.1 --port 8000

# Terminal 2 — Backend Spring Boot (Puerto 8080)
cd "c:\Users\farid\Documents\Cositas pa guardar\Universidad\Gerencia de proyectos en computación\crm-toyota\backend-crm"
java -jar target\crm-toyota-backend-1.0.0.jar --spring.profiles.active=local

# Terminal 3 — Frontend Vue 3 (Puerto 5173)
cd "c:\Users\farid\Documents\Cositas pa guardar\Universidad\Gerencia de proyectos en computación\crm-toyota\frontend-web"
npm run dev
```
