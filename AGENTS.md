# AGENTS.md — Vitalocio

## 1. Objetivo del Producto
Plataforma web de **promoción de la salud y prevención de la enfermedad**. Combina una landing page ultrarrápida (Astro) con islas interactivas (Vue 3) para agendamiento de experiencias/programas, alquiler de espacios y gestión corporativa.

## 2. Stack y Arquitectura Mínima
- **Astro (última versión):** Renderizado estático (SSG) por defecto para toda la landing page (Zero-JS). Usar renderizado bajo demanda (`prerender = false`) y endpoints (`src/pages/api/`) solo para sesiones, portales privados y lógica de servidor.
- **Vue 3 (`@astrojs/vue`):** Composition API con `<script setup lang="ts">`. Usar exclusivamente como islas (`client:visible` o `client:load`) para flujos con estado interactivo.
- **Tailwind CSS v4 (`@tailwindcss/vite`):** Tokens de diseño en `src/styles/global.css`. Prohibido usar colores hexadecimales sueltos en componentes.
- **TypeScript estricto:** Prohibido `any`. Validar datos externos con Zod.
- **Supabase:** PostgreSQL + Supabase Auth + **Row Level Security (RLS)** obligatorio en todas las tablas. Cliente único en `src/lib/supabase.ts`.
- **Roadmap Futuro (No implementar en MVP inicial):** Envío de OTP por WhatsApp y notificaciones por correo con **Resend** (pendiente de dominio propio).

## 3. Dominio de Negocio: Perfiles, Relaciones y Flujos
1. **Perfiles (`profiles`):**
   - `admin`: Control total sobre servicios, agendas, espacios, validación de intenciones, asignación de planes/paquetes y gestión de todos los perfiles.
   - `member`: Usuario beneficiario que asiste a actividades, programas o experiencias.
     - *Titular / Acudiente:* Un `member` puede agendar para sí mismo o inscribir a un tercero. Al inscribir a un tercero, **se crea también como un `member` independiente** vinculado mediante relación de acudiente/pagador, permitiendo que en el futuro gestione sus propias reservas.
     - *Historial compartido:* Tanto el titular/acudiente (pagador) como el beneficiario (`member`) pueden consultar reservas futuras e historial de asistencias.
   - `host_partner`: Profesional de la salud que alquila consultorios (*coworking*) o usuario que reserva espacios privados (ej. terraza para eventos). Consulta disponibilidad, reserva y ve su histórico.
   - `corporate_client`: Representante de empresa que consulta las campañas inscritas y el historial de actividades extramurales realizadas.
2. **Autenticación e Intención (Fase 1):**
   - Login mediante **código OTP al correo electrónico**, **Google** o **Microsoft (cuenta personal)** vía Supabase Auth.
   - Desde el flujo de ingreso se captura la intención del usuario (servicio/programa de interés) y sus datos.
   - El `admin` revisa la intención/datos ingresados y marca/asigna si el usuario cuenta con algún **plan o paquete prepagado a la medida** (individual o grupal).

## 4. Flujo de Trabajo (Spec-Driven Development)
1. **Consulta obligatoria:** Antes de escribir código, lee `docs/specs/`, usa el MCP `astro-docs` para validar sintaxis actual y aplica las skills en `.opencode/skills/`.
2. **Planificación primero:** Redacta o actualiza la especificación en `docs/specs/` y espera aprobación explícita antes de crear o editar código.
3. **Definition of Done:** Todo cambio debe pasar `npm run check` y `npm run build` sin errores.
