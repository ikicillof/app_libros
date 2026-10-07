---
name: skill-library
description: Índice de skills de BIBLIOTECA (no cargados por defecto) para app_libros. Usalo cuando la tarea toque rendimiento o caché del feed, SEO de páginas públicas, tests E2E, accesibilidad o diseño de UI, animaciones, integración con APIs de libros (Open Library, Google Books), emails o notificaciones, deploy o Docker, internacionalización, manejo de errores, decisiones de arquitectura, Prisma Postgres/Compute o adapters de Prisma, o mantenimiento de la instalación de ECC.
---

# Biblioteca de skills de app_libros

Este proyecto separa los skills en dos grupos:

- **DAILY**: están en `.claude/skills/` y se cargan en cada sesión. Cubren el stack real del repo: Next.js 16, React 19, TypeScript, Tailwind 4, Prisma 7 con PostgreSQL en Supabase y Supabase Auth.
- **LIBRARY**: se mantienen accesibles pero no se cargan por defecto. Este archivo es el índice para encontrarlos.

## Cómo abrir un skill de la biblioteca

1. **Skills de ECC**: con el plugin ECC habilitado, invocalos con la herramienta Skill como `ecc:<nombre>`. Si el plugin está deshabilitado, leé `~/.claude/plugins/cache/ecc/ecc/<versión>/skills/<nombre>/SKILL.md`.
2. **Skills oficiales de Prisma**: leé `.agents/skills/<nombre>/SKILL.md`. Están versionados en el repo y registrados en `skills-lock.json`.

Leé el SKILL.md solo cuando la tarea lo necesite. No copies su contenido acá.

## Índice por tema

| Si la tarea trata de… | Skill(s) |
|---|---|
| Feed lento, re-renders, bundle grande | `react-performance` |
| Caché de feeds o contadores, sesiones | `redis-patterns`, `content-hash-cache-pattern` |
| SEO de páginas públicas de libros o reseñas, metadata, sitemap | `seo` |
| Tests end-to-end, QA en navegador, regresiones | `e2e-testing`, `browser-qa`, `ai-regression-testing` |
| Accesibilidad (WCAG), lectores de pantalla | `accessibility`, `frontend-a11y` |
| Sistema de diseño, dirección visual, pulido de UI | `design-system`, `frontend-design-direction`, `make-interfaces-feel-better` |
| Animaciones y transiciones | `motion-foundations`, `motion-patterns`, `motion-advanced` |
| Integrar APIs externas de libros (Open Library, Google Books) | `api-connector-builder` |
| Emails transaccionales, notificaciones por mail | `mailtrap-email-integration` |
| Deploy, entornos, contenedores | `deployment-patterns`, `docker-patterns` |
| Traducciones, más de un idioma | `i18n-sync` |
| Estrategia de errores, boundaries, logging | `error-handling` |
| Registrar decisiones de arquitectura, capas | `architecture-decision-records`, `hexagonal-architecture` |
| Convenciones de ramas y commits | `git-workflow` |
| Reconfigurar o auditar la instalación de ECC | `configure-ecc`, `agent-sort`, `skill-stocktake`, `strategic-compact` |

### Prisma (oficiales, en `.agents/skills/`)

| Si la tarea trata de… | Skill |
|---|---|
| Prisma Postgres, el hosting de Prisma. **No aplica**: la base es Supabase | `prisma-postgres-setup` |
| Prisma Compute, el hosting de apps de Prisma | `prisma-compute` |
| Implementar un driver adapter propio (no usarlo) | `prisma-driver-adapter-implementation` |
| Migrar desde Prisma 6 o desde MongoDB | `prisma-upgrade-v7`, `prisma-mongodb-upgrade` |
| Alias deprecados: usá `prisma-orm-setup` o `prisma-postgres-setup` | `prisma-database-setup`, `prisma-postgres` |

## Fuera del stack: no cargar

ECC también trae skills de Django, Laravel, Rails, Spring Boot, Quarkus, FastAPI, NestJS, Nuxt/Vue, Angular, React Native, Flutter/Dart, Kotlin, Swift, Go, Rust, C++, C#/.NET, F#, Perl y Python/PyTorch, además de dominios como healthcare, redes/homelab, finanzas, logística, trading/DeFi y video. Este repo no usa ninguno. Si eso cambia, volvé a correr `/ecc:agent-sort`.
