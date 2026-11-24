---
name: stitch-to-vue
description: >-
  Convierte diseños, capturas o exportaciones HTML/Tailwind de Google Stitch en vistas
  y componentes Vue 3 (<script setup lang="ts">) dentro de frontend-web, conectándolos
  con Pinia y Axios, garantizando ergonomía 1366x768 sin scroll horizontal, marca blanca
  configurable, pruebas Vitest y Docs-as-Code del repositorio frontend.
---

# Skill: Google Stitch a Vue 3 Mostrador (`frontend-web`)

Usa este procedimiento al construir o adaptar cualquier interfaz en `frontend-web`:

## 1. Descomposición Limpia a Vue 3 + TypeScript
1. Extrae la estructura visual en componentes `.vue` usando estrictamente `<script setup lang="ts">` e interfaces tipadas en `src/types/`.
2. Reemplaza cualquier nombre fijo de taller o RUC por lecturas reactivas del store `useWorkshopConfigStore()` (marca blanca configurable).

## 2. Chequeo de Viewport Taller (`1366x768`)
1. Verifica que el contenedor raíz respete `max-w-full overflow-x-hidden` y aproveche la altura útil (`~650px`) con paneles con scroll interno controlado (`overflow-y-auto`) en tablas largas o el carrito POS.
2. Asegura densidad ergonómica: botones de acción primarios visibles sin hacer scroll hasta el fondo de la página, badges claros de semáforo de stock (`CRITICO`, `ALERTA`, `NORMAL`) y atajos rápidos en buscadores.

## 3. Resiliencia e Integración
1. En el Cotizador Express, vincula el borrador activo a `localStorage` cada 5s mediante `@vueuse/core` (`useDebounceFn` + `useStorage`) y mantén activa la hoja `@media print` para impresión directa de emergencia.
2. Conecta llamadas API a través del cliente Axios centralizado con manejo amigable de `ProblemDetail` (especialmente el bloqueo `409` de stock ofreciendo en 1 clic los equivalentes OEM/Denso/Aisin).

## 4. Verificación y Docs-as-Code
1. Añade o actualiza la prueba unitaria en `src/__tests__/` con **Vitest + `@vue/test-utils`**.
2. Ejecuta `npm run type-check` y `npm run test:unit -- --run`.
3. Registra la vista/store en `frontend-web/docs/UI_ARCHITECTURE_1366x768.md`.
