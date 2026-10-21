# Breadcrumbs

App móvil que convierte una meta grande en pasos pequeños —**migajas**— generados por IA. Escribes tu meta, obtienes un sendero de pasos de 25 minutos o menos, y al terminar cada sesión dejas anotada la migaja para mañana.

> Nombre provisional. Estado: **etapa 0 — base del repositorio.**

## Documentación

- [Especificación del MVP](docs/ESPECIFICACION.md): producto, pantallas, IA, datos, planes y etapas.
- [Estructura propuesta](docs/ESTRUCTURA.md): carpetas del proyecto (pendiente de aprobación).

## Tecnología

| Pieza | Herramienta |
| --- | --- |
| App iPhone y Android | React Native + Expo (TypeScript) |
| Usuarios y base de datos | Supabase (con Row Level Security) |
| IA | Supabase Edge Function + API de Claude (Haiku 5.5) |
| Suscripciones | RevenueCat |
| Notificaciones | Expo Notifications |

## Etapas

- [x] 0. Base del repositorio (especificación, estructura, convenciones)
- [ ] 1. Pantallas con datos falsos
- [ ] 2. Usuarios y base de datos
- [ ] 3. IA: desglose y re-desglose
- [ ] 4. Sendero y migaja para mañana
- [ ] 5. Pagos
- [ ] 6. Publicación

Cada etapa se prueba en el celular y se guarda en Git antes de seguir.
