# app_libros — Diseño

> **Estado: BORRADOR.** Secciones 1 y 2A aprobadas. 2B presentada, falta aprobarla. Faltan 3 a 7.
> No se escribe código hasta que el diseño completo esté aprobado.
>
> **Para retomar:** aprobar 2B y seguir con la sección 3 (modelo de datos), luego feeds y reglas,
> libros, actividad, moderación y seguridad, errores y testing, despliegue.

## Objetivo

Red social cuyo objetivo principal es ser un foro literario: publicaciones cortas sobre libros
(discusiones, recomendaciones, teorías), con perfiles que muestran una biblioteca personal.
Producto público, en español, que arranca con una comunidad chica.

**Stack:** Next.js 16 (App Router, `src/app`), Supabase (Postgres + Auth), Prisma 7.

## Decisiones tomadas

| Tema | Decisión |
|---|---|
| Feeds | Dos pestañas: "Todo" (global) y "Siguiendo". Un usuario nuevo ve "Todo" por defecto hasta que sigue a alguien. Cronológicos, scroll infinito. |
| Spoilers | Casilla "Contiene spoilers" en posts y respuestas. El contenido no se muestra; se ve "⚠️ Contiene spoilers sobre *[libro]* · Ver igual". El libro asociado sí se ve. |
| Fuente de libros | Open Library, consultada solo desde el servidor, con caché. Copia local del libro al usarlo. Portada generada (título + autor) si falta. |
| Hosting | Ahora: Vercel Hobby + Supabase Free. Al lanzar: Supabase Pro. Con monetización: Vercel Pro. Código desplegable en cualquier hosting de Node. |
| Monetización | Anuncios + suscripción sin anuncios. Última fase, fuera del MVP. Nada preparado de antemano. |
| Moderación | Reportes, panel de admin (ocultar contenido, suspender), límite de publicaciones por minuto, normas de la comunidad, bloquear y silenciar. |
| Registro | Email + contraseña y Google (Supabase Auth). Nombre de usuario único elegido en el primer ingreso. Servicio de email propio (ej. Resend) al lanzar. |
| Arquitectura | Todo por el servidor: Supabase solo para Auth y como base. Lectura/escritura vía Prisma en una capa de servicios. El navegador nunca habla directo con la base; la API directa de Supabase queda cerrada con RLS. |

### Bloquear y silenciar

- **Bloquear:** ninguno de los dos ve los posts del otro, no pueden responderse ni seguirse, no hay notificaciones entre ellos.
- **Silenciar:** dejás de ver sus posts en tus feeds; el otro no se entera y puede seguir interactuando.

### Valores por defecto aprobados

| Tema | MVP | Después |
|---|---|---|
| Largo del post | Hasta 500 caracteres | — |
| Contenido | Solo texto + libro asociado | Imágenes adjuntas |
| Etiqueta | Opcional, una: Discusión, Recomendación o Teoría | — |
| Hilos | Cada respuesta es un post con padre; se puede responder a respuestas. La vista muestra la conversación previa y las respuestas | — |
| Likes | Solo "me gusta", contador visible | — |
| Editar / borrar | Editar dentro de 15 minutos, marca "(editado)". Borrar siempre; con respuestas queda "[post eliminado]" | — |
| Perfil | Público: @usuario, nombre, bio 160 caracteres, avatar de Google o iniciales, contadores, biblioteca | Foto propia, perfil privado |
| Biblioteca | 3 listas públicas; puntaje 1–5 entero; formato físico/digital; fecha de terminado opcional (si falta, fecha de alta) | Media estrella, reseñas largas, "Leyendo ahora" |
| Búsqueda | Libros (Open Library) y usuarios por @ | Texto de los posts |
| Menciones | — | @menciones con notificación |
| Feeds | Cronológicos | "Destacados" con algoritmo |
| Idioma / plataforma | Español; web adaptada a celular | PWA o app nativa |

## 1. Alcance y fases

### Entra en el MVP

- **Cuentas:** registro con email y contraseña o Google, nombre de usuario único, onboarding de 3 libros con sugerencia de usuarios que tienen esos libros.
- **Foro:** posts de 500 caracteres, respuestas, likes, etiqueta, libro asociado, spoilers, editar y borrar.
- **Feeds:** "Todo" y "Siguiendo".
- **Perfil:** bio, avatar, seguidores y biblioteca pública.
- **Biblioteca:** leídos (puntaje, formato, fecha de terminado), quiero comprar, pendientes. Un libro está en una sola lista a la vez.
- **Libros:** búsqueda en Open Library y página de cada libro (posts, cantidad de lectores, puntaje promedio).
- **Actividad:** notificaciones en la app (respuestas, likes, seguidores), pregunta de la semana fijada por el admin, desafío de lectura anual.
- **Moderación:** reportes, panel de admin, suspender, bloquear, silenciar, límite de publicaciones.

### Orden de construcción

Cada fase termina con algo que funciona y se puede probar.

1. Cuentas, perfil y nombre de usuario.
2. Libros y biblioteca personal.
3. Posts, respuestas, likes, spoilers y feed "Todo".
4. Seguir usuarios, feed "Siguiendo" y onboarding.
5. Bloquear, silenciar, reportes, panel de admin y límite de publicaciones.
6. Notificaciones, estadísticas del libro, pregunta de la semana y desafío anual.
7. Lanzamiento: Supabase Pro, servicio de email propio, dominio, normas de la comunidad.

### Después del MVP (orden aproximado)

1. Club de lectura del mes.
2. Resumen semanal por email.
3. Menciones, búsqueda en posts y lista "Leyendo ahora".
4. Foto de perfil propia, imágenes en posts y perfil privado.
5. Notificaciones en tiempo real y feed "Destacados".
6. Moderación automática.
7. App instalable.
8. Al final: anuncios y suscripción sin anuncios.

## 2. Organización del código

### 2A. Módulos (aprobado)

Organización por funcionalidad en `src/modules`:

| Módulo | Responsabilidad |
|---|---|
| cuentas | Registro, sesión, nombre de usuario, onboarding |
| libros | Búsqueda en Open Library, copia local de libros |
| biblioteca | Las 3 listas, puntajes, desafío anual |
| foro | Posts, respuestas, likes, spoilers |
| feeds | Arma "Todo" y "Siguiendo", aplica bloqueos y silenciados |
| social | Seguir, bloquear, silenciar |
| notificaciones | Crea y lista notificaciones |
| moderacion | Reportes, suspensiones, límite de publicaciones |

Cada módulo tiene tres capas:

- **Pantallas** (`src/app`): solo muestran datos.
- **Acciones:** reciben lo que manda el usuario, validan y llaman al servicio.
- **Servicios:** aplican las reglas y usan Prisma. Única capa que toca la base.

Nota técnica: renombrar `prisma7.config.ts` a `prisma.config.ts` en la Fase 1.

### 2B. Flujo de un pedido (presentado, falta aprobar)

Ejemplo: publicar un post.

1. El usuario escribe el post y toca "Publicar".
2. La acción revisa que haya sesión iniciada.
3. La acción valida el texto (hasta 500 caracteres), la etiqueta y el libro.
4. El servicio de moderación revisa que no esté suspendido ni haya pasado el límite de publicaciones.
5. El servicio del foro guarda el post con Prisma.
6. Si es una respuesta, se crea una notificación para el autor del post original, salvo que lo tenga bloqueado.
7. La pantalla se actualiza y muestra el post.

Si algo falla, se muestra un mensaje claro (ej.: "Estás publicando muy rápido, esperá un minuto") y el texto escrito no se pierde.

## 3–7

_Pendientes._
