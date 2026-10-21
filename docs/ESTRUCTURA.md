# Estructura de carpetas propuesta

> **Estado: propuesta, pendiente de aprobación.** No se escribe código de la etapa 1 hasta aprobarla.

Usa [Expo Router](https://docs.expo.dev/router/introduction/): cada archivo dentro de `app/` es una pantalla, así la navegación se lee directo del árbol de carpetas.

```
breadcrumbs/
├── app/                          # Pantallas (Expo Router)
│   ├── _layout.tsx               # Layout raíz: fuentes, tema, proveedor de datos
│   ├── (auth)/                   # Grupo sin sesión iniciada
│   │   ├── _layout.tsx
│   │   ├── bienvenida.tsx        # 3 tarjetas + botón de registro
│   │   └── registro.tsx          # Correo, Google, Apple
│   ├── (app)/                    # Grupo con sesión iniciada
│   │   ├── _layout.tsx
│   │   ├── index.tsx             # Inicio: siguiente migaja de cada meta
│   │   ├── meta/
│   │   │   ├── nueva.tsx         # Nueva meta + vista previa de migajas
│   │   │   └── [id].tsx          # Detalle de meta: el sendero
│   │   ├── cierre.tsx            # Cierre de sesión: migaja para mañana (modal)
│   │   ├── ajustes.tsx           # Recordatorio, plan, cerrar sesión, borrar cuenta
│   │   └── premium.tsx           # Paywall (modal, solo al llegar a un límite)
├── src/
│   ├── components/               # Piezas reutilizables
│   │   ├── ui/                   # Botón, Tarjeta, Campo, Chip, Interruptor…
│   │   ├── TarjetaMigaja.tsx     # Migaja con botón Hecho (Inicio)
│   │   ├── Sendero.tsx           # Lista vertical del progreso (Detalle)
│   │   └── AnimacionLogro.tsx    # Animación al marcar Hecho
│   ├── data/
│   │   ├── tipos.ts              # Perfil, Meta, Migaja, UsoIA (igual que las tablas)
│   │   ├── mock.ts               # Datos falsos (etapa 1)
│   │   └── store.ts              # Estado de la app; en la etapa 2 cambia mock → Supabase
│   ├── theme/
│   │   └── tokens.ts             # Colores, tipografía, espaciado, radios
│   └── lib/                      # Clientes de Supabase, RevenueCat, notificaciones (etapas 2–5)
├── supabase/                     # Etapa 2 en adelante
│   ├── migrations/               # Tablas + Row Level Security en SQL
│   └── functions/
│       ├── desglose/             # Edge Function: meta → migajas
│       └── re-desglose/          # Edge Function: migaja → 2–4 pasos
├── assets/                       # Íconos, splash, fuentes
├── docs/                         # Especificación y decisiones
├── app.json
├── package.json
└── tsconfig.json
```

## Decisiones

- **Expo Router** en lugar de React Navigation manual: menos código y las rutas coinciden con las pantallas de la especificación.
- **Nombres en español** para pantallas, tipos y datos (`Meta`, `Migaja`), igual que las tablas de Supabase. Nombres técnicos genéricos (`_layout`, `components`) siguen la convención de Expo.
- **`src/data/store.ts`** aísla el origen de los datos: en la etapa 1 lee de `mock.ts`; en la etapa 2 se cambia a Supabase sin tocar las pantallas.
- **Sin librería de estado externa** al inicio: React Context + `useReducer` alcanza para el MVP.
- **`supabase/`** vive en el mismo repositorio para versionar migraciones y Edge Functions junto con la app. La clave de la API de Claude nunca entra al repositorio.
