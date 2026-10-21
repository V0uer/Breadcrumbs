# Breadcrumbs — Especificación del MVP

19 oct 2026 · @Jorge

## Resumen del producto

Breadcrumbs (nombre provisional) es una app móvil que convierte una meta grande en pasos pequeños, llamados **migajas**, generados por IA. Cada migaja completada da una pequeña sensación de logro y mantiene a la persona avanzando.

- **Para quién:** personas que se abruman con metas grandes o que empiezan con ganas y abandonan a las pocas semanas.
- **Problema:** no saber por dónde empezar, y perder el hilo al retomar.
- **Propuesta de valor:** escribes tu meta y en segundos tienes un sendero de pasos concretos. Al terminar una sesión dejas anotada tu siguiente migaja, así retomar es casi automático.
- **Diferenciadores** frente a otras apps de tareas con IA: la migaja para mañana, el re-desglose cuando te atoras y un progreso sin castigos por días perdidos.
- **Modelo de negocio:** freemium con prueba gratuita del plan premium.

## Alcance del MVP

El MVP debe probar una sola cosa: **que la gente vuelve a la app para completar migajas.** Todo lo demás espera.

| Entra en el MVP | Queda para después |
| --- | --- |
| Registro con correo, Google y Apple | Estadísticas avanzadas |
| Crear una meta y generar migajas con IA | Widgets en la pantalla de inicio |
| Editar, reordenar y borrar migajas | Temas y personalización |
| Marcar migajas como hechas, con animación de logro | Metas compartidas o en grupo |
| Sendero visual del progreso | Recordatorios inteligentes con IA |
| Migaja para mañana al cerrar sesión | Versión web |
| Re-desglose: dividir una migaja difícil en pasos más chicos | Integración con calendario |
| Recordatorio diario simple a una hora elegida | |
| Suscripción con prueba gratuita | |

**Regla para decidir si algo entra:** si no ayuda a completar la siguiente migaja, va a la columna de después.

## Flujo del usuario

El flujo tiene dos partes: crear la meta una vez y luego un ciclo diario de completar migajas.

Si una migaja cuesta, la IA la divide en pasos más chicos en lugar de marcarla como fallida.

## Pantallas

Son siete pantallas. La pantalla principal siempre muestra la siguiente migaja, para que abrir la app y avanzar tome segundos.

| Pantalla | Qué hace |
| --- | --- |
| Bienvenida | 3 tarjetas que explican la idea y botón de registro. |
| Registro / inicio de sesión | Correo, Google o Apple. |
| Inicio | La siguiente migaja de cada meta activa, con botón grande de **Hecho**. Abajo, acceso a crear meta. |
| Nueva meta | Campo para escribir la meta, preguntas opcionales (fecha límite, minutos al día) y botón **Generar migajas**. Muestra la vista previa para editar antes de guardar. |
| Detalle de meta | El sendero completo: migajas hechas, la actual resaltada y las que faltan. Editar, reordenar, borrar, añadir y **Dividir en pasos más chicos**. |
| Cierre de sesión | Al marcar una migaja, pregunta cuál es la siguiente mini acción para mañana y la guarda como nota. |
| Ajustes y suscripción | Hora del recordatorio, plan actual, botón para ver premium, restaurar compras y cerrar sesión. |

El paywall aparece solo cuando la persona llega a un límite del plan gratis, nunca al abrir la app.

## Cómo funciona la IA

La IA hace dos cosas: desglosar una meta en migajas y dividir una migaja difícil en pasos más chicos. Ambas usan **Claude Haiku 5.5**, llamado siempre desde el servidor, nunca desde la app.

### 1. Desglose de meta

- **Entrada:** la meta, fecha límite opcional, minutos disponibles al día e idioma.
- **Salida:** entre 5 y 15 migajas para la primera etapa, no la meta completa. Cada una se puede hacer en 25 minutos o menos y empieza con un verbo concreto.
- Si la meta es muy grande, la IA genera solo la primera etapa y ofrece generar la siguiente cuando se complete.

### 2. Re-desglose

- Se activa con el botón **Dividir en pasos más chicos**, o si una migaja lleva 3 días sin completarse.
- Convierte una migaja en 2 a 4 pasos de 5 a 10 minutos.

### Formato de respuesta

JSON estricto, para que la app lo lea sin errores.

```json
{
  "etapa": "Primeras bases",
  "migajas": [
    { "titulo": "Descargar Duolingo y elegir inglés", "minutos": 5 },
    { "titulo": "Completar la primera lección", "minutos": 10 }
  ]
}
```

**Reglas de seguridad del prompt:** rechazar metas dañinas (autolesión, dietas extremas, actividades ilegales) y responder con un mensaje amable. En metas de salud, finanzas o legales, sugerir consultar a un profesional.

**Costo estimado:** menos de $0.002 USD por desglose, con base en precios de octubre de 2026.

## Modelo de datos y seguridad

Cuatro tablas en Supabase bastan para el MVP. Cada fila pertenece a un usuario y solo ese usuario puede verla.

| Tabla | Campos principales |
| --- | --- |
| `perfiles` | id, nombre, hora_recordatorio, zona_horaria, plan (gratis / premium), creado_en |
| `metas` | id, usuario_id, titulo, fecha_limite, minutos_dia, estado (activa / pausada / lograda), creada_en |
| `migajas` | id, meta_id, usuario_id, titulo, minutos, orden, etapa, hecha, hecha_en, padre_id (si salió de un re-desglose) |
| `uso_ia` | id, usuario_id, tipo (desglose / re-desglose), tokens_entrada, tokens_salida, creado_en |

La nota de migaja para mañana se guarda como una migaja nueva al inicio del sendero, así no hace falta otra tabla.

### Reglas de seguridad obligatorias

- Activar **Row Level Security** en todas las tablas: cada usuario solo lee y escribe sus filas.
- La clave de la API de la IA vive solo en una **Edge Function** de Supabase, nunca en el código de la app.
- La Edge Function revisa la tabla `uso_ia` antes de llamar a la IA, para respetar los límites del plan gratis.
- El plan premium lo confirma **RevenueCat desde el servidor**; la app nunca decide sola si alguien pagó.
- Botón para **borrar la cuenta** y todos sus datos, que Apple y Google exigen.

## Plan gratis vs premium

El plan gratis debe alcanzar para completar una meta pequeña; el premium quita los límites. La prueba de 7 días empieza al crear la primera meta, no al instalar.

| Función | Gratis | Premium |
| --- | --- | --- |
| Metas activas | 1 | Ilimitadas |
| Desgloses con IA | 3 al mes | Ilimitados |
| Re-desgloses con IA | 5 al mes | Ilimitados |
| Sendero visual y migaja para mañana | Sí | Sí |
| Recordatorio diario | Sí | Sí |
| Re-desglose automático tras 3 días atorado | No | Sí |

Precios sugeridos para probar: **$3 USD al mes** o **$24 USD al año**, con precios regionales más bajos en México y Latinoamérica.

### Preguntas abiertas

- [ ] ¿3 desgloses al mes son suficientes para que alguien sienta el valor?
- [ ] ¿Precio mensual en pesos mexicanos?

## Tecnología y etapas de construcción

La app se construye en 6 etapas con Claude Code. Al final de cada etapa se prueba en el celular y se guarda en Git antes de seguir.

| Pieza | Herramienta |
| --- | --- |
| App iPhone y Android | React Native con Expo (TypeScript) |
| Usuarios y base de datos | Supabase |
| Llamadas a la IA | Supabase Edge Function + API de Claude (Haiku 5.5) |
| Suscripciones y prueba gratis | RevenueCat |
| Notificaciones | Expo Notifications |
| Pruebas en el celular | Expo Go, luego EAS Build |

### Etapas

1. **Pantallas con datos falsos.** Todas las pantallas y la navegación, sin servidor.
2. **Usuarios y base de datos.** Registro, tablas y Row Level Security.
3. **IA.** Edge Function de desglose y re-desglose, con límites del plan gratis.
4. **Sendero y migaja para mañana.** Animación de logro, cierre de sesión y recordatorio diario.
5. **Pagos.** RevenueCat, prueba de 7 días y paywall.
6. **Publicación.** Íconos, capturas, política de privacidad y envío a las tiendas.
