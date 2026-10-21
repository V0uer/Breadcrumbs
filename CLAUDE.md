# Breadcrumbs — guía para Claude Code

App móvil (React Native + Expo + TypeScript) que convierte metas en migajas generadas por IA. La especificación completa está en `docs/ESPECIFICACION.md` y la estructura de carpetas en `docs/ESTRUCTURA.md`.

## Forma de trabajar

- Se trabaja **por etapas** (ver especificación). Haz solo la etapa que se pida; no adelantes trabajo de etapas siguientes.
- Antes de escribir código de una etapa nueva, explica el plan y espera aprobación.
- Al terminar una etapa: probar en el celular (Expo Go) y hacer commit.
- Regla de alcance: si algo no ayuda a completar la siguiente migaja, no entra al MVP.

## Convenciones

- Textos de la app, nombres de pantallas y tipos de datos en **español** (`Meta`, `Migaja`, `hecha`).
- TypeScript estricto. Componentes funcionales con hooks.
- Colores, tipografía y espaciado solo desde `src/theme/tokens.ts`.

## Seguridad (obligatorio)

- Nunca poner la clave de la API de Claude ni secretos en la app o en el repositorio. Solo en Edge Functions de Supabase.
- Toda tabla de Supabase lleva Row Level Security: cada usuario solo lee y escribe sus filas.
- El estado premium lo confirma RevenueCat desde el servidor; la app nunca lo decide sola.
- Las Edge Functions revisan `uso_ia` antes de llamar a la IA.
