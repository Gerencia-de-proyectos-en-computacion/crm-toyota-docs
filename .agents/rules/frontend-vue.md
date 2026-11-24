---
trigger: glob
globs: "**/frontend-web/**"
description: "Reglas de UI/UX 1366x768, Vue 3 Composition API, resiliencia en mostrador y marca blanca."
---

# Reglas de Ingeniería — Frontend Web (`frontend-web`)

1. **Estándar Vue 3 + TypeScript Estricto:**
   - Usar exclusivamente `<script setup lang="ts">` (Composition API). **Prohibido** Options API y prohibido `any` explícito o implícito.
   - Estado global en stores modulares de **Pinia** (`auth`, `quotePos`, `catalog`, `alerts`, `workshopConfig`).

2. **Ergonomía Estricta de Mostrador (`1366x768`):**
   - Diseñar toda vista asumiendo un monitor de taller de `1366x768` (~`650px` de alto útil de navegador). **Cero scroll horizontal** (`overflow-x-hidden`).
   - El **Cotizador Express** opera en layout POS de 2 columnas en una sola pantalla (`<= 3 pasos` visuales y `< 3 minutos`):
     - Izquierda: Búsqueda instantánea Cliente/Placa Toyota + Buscador de Repuestos/Equivalentes OEM/Kits sugeridos por kilometraje/Mano de Obra.
     - Derecha fija: Carrito con precios IGV incluido, tope de descuento según rol (`5%` Recepcionista / `15%` Admin), estado de autoguardado y acciones rápidas (Guardar, Aprobar, PDF, WhatsApp `wa.me`).

3. **Resiliencia en Mostrador (Sin Offline Complejo):**
   - Autoguardar el borrador activo de cotización en `localStorage` cada 5 segundos usando `@vueuse/core` (`useStorage` / `useDebounceFn`), mostrando indicador visual de respaldo y botón de recuperación si se recarga el navegador.
   - Incluir hoja `@media print` limpia para imprimir la proforma directamente desde el navegador si fallara la descarga del PDF.
   - Renovar silenciosamente el JWT en el interceptor Axios cuando reste `< 2h` de sesión activa para nunca interrumpir una atención en mostrador.

4. **Marca Blanca (`workshopConfig`):**
   - Nunca fijar en duro el nombre de un taller en componentes; consumir siempre el store `workshopConfig` (con placeholder neutro `"[Nombre del Taller Automotriz]"` editable por `ADMIN`).

5. **Pruebas y Docs-as-Code:**
   - Cubrir cálculos de carrito, topes de descuento, sugerencia de kits y autoguardado con **Vitest + `@vue/test-utils`**.
   - Mantener actualizado `frontend-web/docs/` ante nuevas vistas, stores o variables `VITE_*`.
