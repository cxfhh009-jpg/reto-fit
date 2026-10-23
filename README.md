# Reto Fit

App para el reto de entrenamiento: cada una elige sus días, sube foto de prueba, las faltas suman multa, se paga con comprobante, hay premios por constancia, ranking y sección de dieta.

- **La app** (lo que se ve) vive en **GitHub Pages**: `index.html` y los demás archivos de esta carpeta.
- **Los datos y las fotos** viven en **Supabase** (plan gratis).
- En el iPhone se instala desde Safari como app (ícono en la pantalla de inicio).

Costo: $0.

---

## Archivos

| Archivo | Para qué |
|---|---|
| `index.html` | Toda la app |
| `config.js` | Dirección y clave pública de tu Supabase (lo editas una vez) |
| `manifest.json`, `sw.js`, íconos `.png` | Para que se instale como app en el teléfono |
| `.nojekyll` | Para que GitHub Pages publique los archivos tal cual |
| `supabase/reto_fit.sql` | La base de datos (se pega en Supabase, no hace falta subirlo a GitHub) |

---

## Paso 1 — Supabase (una sola vez)

1. Entra a [supabase.com](https://supabase.com) → **New project**. Usa un proyecto **nuevo**, aparte del de M&A Capital, para que las cuentas de las chicas no se mezclen con las del dashboard. El plan gratis permite 2 proyectos.
2. **SQL Editor → New query** → pega completo `supabase/reto_fit.sql` → **Run**.
   Al final muestra una fila con el **código para unirse** (por ejemplo `C61DFA`). Anótalo; también se ve después en la app.
   Se puede correr otra vez si cambias algo: no borra datos.
3. **Authentication → Sign In / Providers → Email**: apaga **Confirm email**. Así cada una crea su cuenta y entra de una vez, sin tener que confirmar el correo. Sin el código del grupo nadie puede entrar al reto.
4. **Authentication → URL Configuration**: en **Site URL** pon la dirección de GitHub Pages del paso 2 (por ejemplo `https://tuusuario.github.io/reto-fit/`). Sirve para el enlace de "Olvidé mi contraseña".
5. **Project Settings → API** (o **Data API**): copia **Project URL** y la clave **anon public** (o *Publishable key*) y pégalas en `config.js`:

   ```js
   SUPABASE_URL: "https://xxxxxxxx.supabase.co",
   SUPABASE_ANON_KEY: "eyJhbGciOi...",
   ```

   Esa clave es pública y puede ir en GitHub. **Nunca** pongas la clave *service_role* / *secret*.

## Paso 2 — GitHub Pages

1. En GitHub: **New repository** → nombre `reto-fit` → **Public** → Create.
2. **Add file → Upload files** → arrastra todos los archivos de esta carpeta (con `config.js` ya editado) → **Commit**.
3. **Settings → Pages** → *Source*: **Deploy from a branch** → *Branch*: **main** / **(root)** → **Save**.
4. En 1–2 minutos queda en `https://tuusuario.github.io/reto-fit/`.

## Paso 3 — Primer ingreso

1. Abre el enlace → **Crear cuenta** → correo y contraseña.
2. Pon tu nombre, el código del grupo y tu emoji. **La primera persona que entra queda como administradora** (que sea Martina o quien maneje el reto).
3. Avatar (arriba a la derecha) → **Administrar el reto**:
   - **Datos de la cuenta del reto**: banco, tipo y número de la cuenta de ahorros. Se muestran a todas cuando van a pagar.
   - Revisa las **reglas** (ver abajo).
   - **Compartir invitación**: manda por WhatsApp el enlace con el código ya puesto.

## Paso 4 — Instalar en el iPhone

1. Abrir el enlace en **Safari**.
2. Botón **Compartir** (cuadrado con flecha) → **Agregar a pantalla de inicio** → **Agregar**.
3. Abrirla desde el ícono y entrar una vez con su correo. Ojo: la app instalada no comparte la sesión con Safari, por eso se entra una vez más.

En Android: Chrome → menú ⋮ → **Instalar app**.

---

## Cómo funciona

- **Días**: cada semana cada una elige sus días (3 por defecto). Si no elige, se repiten los de la semana anterior. Los de la semana en curso quedan fijos; los de la próxima se cambian hasta el domingo. Quien entra a mitad de semana elige entre los días que quedan.
- **Prueba**: un día cuenta solo con foto. Hay 1 día extra para subirla (si entrenó tarde y se le pasó). Las demás pueden **rechazar** una foto con motivo mientras el día esté abierto; la dueña puede subir otra.
- **Faltas y multas**: cuando el día cierra sin prueba, se anota la falta con su multa. Por defecto: $0.25 las 3 primeras del mes, $0.50 las 3 siguientes, luego $1.00, $2.00… y cada mes vuelve a empezar.
- **Cierre de días**: lo hace Supabase solo cada hora (pg_cron) y también cada vez que alguien abre la app.
- **Pagos**: se sube la foto del comprobante; **otra** participante lo confirma y recién ahí baja la deuda.
- **Premios**: un mes cuenta si se cumplieron todos los días elegidos de ese mes calendario. Cada mes completo da un premio de 1 mes (elegir entre borrar la deuda y reiniciar multas, o cheat meal); a los 3 meses seguidos, el set de ejercicio. La administradora puede agregar más premios.
- **Ranking**: días cumplidos, % de cumplimiento, racha de semanas y entrenos totales (incluye días extra). Muestra el **pozo** (lo pagado y confirmado) y lo que falta cobrar.
- **Dieta**: cada comida con foto, marcada buena o mala. No suma ni resta. Las demás la ven y comentan.
- **Correcciones** (administradora): anular una falta (queda en $0) y ajustar deudas a mano con motivo.

## Reglas por confirmar con Martina

Hay detalles que no quedaron claros en su mensaje. Los dejé con un valor razonable y **se cambian desde la app** (Administrar → Reglas), sin tocar código:

| Pregunta | Valor actual | Dónde se cambia |
|---|---|---|
| Después de $1, ¿cómo sigue la multa? | Se duplica: $2, $4… | "La multa se multiplica por" y "Multa máxima" |
| ¿El conteo de faltas vuelve a cero cada mes? | Sí | "Las multas vuelven al monto inicial cada mes" |
| ¿Se pueden mover los días de la semana en curso? | No | "Permitir mover días de la semana actual" |
| ¿Basta con subir la foto o las demás la aprueban? | Basta la foto; las demás pueden rechazarla | "La prueba necesita aprobación" |
| ¿Cuánto tiempo hay para subir la prueba? | Ese día y el siguiente | "Días extra para subir la prueba" |
| ¿Qué premios hay después de los 3 meses? | Solo los que ella dijo | Administrar → Premios → Nuevo premio |

## Límites del plan gratis

- **Fotos**: 1 GB. La app las comprime a ~200 KB, así que caben unas 5,000.
- **Pausa**: Supabase pausa el proyecto si pasa una semana sin que nadie lo use. Con el reto activo no pasa; si pasa, se reactiva con un clic en el panel de Supabase.

## Actualizar la app

Sube los archivos cambiados al repositorio. Si cambiaste `index.html`, cambia también la línea `const VERSION = 'reto-fit-v1'` de `sw.js` (por ejemplo a `v2`) para que los teléfonos tomen la versión nueva al abrirla.

## No incluido en esta versión

- Notificaciones push ("hoy te toca entrenar"): se pueden agregar después con una Edge Function de Supabase.
