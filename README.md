# Expediente Público — Corrupción política en Chile

Guía para publicar esta página en internet y para que tú mismo puedas agregar casos nuevos más adelante, sin tocar código.

## 1. Qué archivos tienes

```
expediente-corrupcion-chile/
├── index.html            ← el sitio público (diseño, filtros, gráficos, mapa). No lo edites a mano.
├── casos.json            ← la "base de datos": un array con todos los casos.
├── admin.html            ← panel privado para agregar y editar casos (formulario + CSV + login).
├── casos.csv             ← copia de todos los casos en formato CSV (para Excel / Google Sheets).
├── plantilla-casos.csv   ← CSV vacío con los encabezados y una fila de ejemplo.
├── plantilla-caso.json   ← un caso en blanco en formato JSON.
└── README.md             ← este archivo
```

`index.html` carga `casos.json` automáticamente al abrirse (con `fetch('casos.json')`). Por eso **los dos archivos siempre tienen que estar juntos, en la misma carpeta**, tanto en tu computador como en el hosting. Si subes solo el HTML, la página va a cargar pero se va a ver vacía (sin casos).

Los polígonos del mapa (regiones de Chile y comunas de Santiago) están adentro de `index.html`, no en el JSON, porque no son algo que necesites editar.

## 2. Cómo agregar o editar un caso

Abre `casos.json` con cualquier editor de texto (VS Code, Notepad++, Sublime, o incluso el Bloc de notas). Es una lista `[ ... ]` de objetos `{ ... }`, uno por caso, separados por comas. Para agregar uno nuevo, copia el siguiente bloque, pégalo como un elemento más de la lista (recuerda la coma antes del siguiente `{`) y completa los campos:

```json
{
  "id": "identificador-unico-sin-espacios",
  "titulo": "Nombre corto del caso",
  "personas": "Nombre de la o las personas involucradas y su cargo",
  "partido": "UDI",
  "sector": "Derecha",
  "nivel": "Municipal",
  "comuna": "Providencia",
  "imagen": null,
  "anioInicio": 2026,
  "anioFin": null,
  "monto": 150,
  "montoNota": "Explicación breve de a qué corresponde ese monto, o null si no aplica",
  "categoria": "economico",
  "delito": null,
  "estadoCategoria": "investigacion",
  "estado": "Texto corto que se muestra como 'sello' en la tarjeta, ej: Formalizado; en prisión preventiva",
  "descripcion": "2 a 4 frases explicando el caso, en tono neutro, distinguiendo acusación de condena.",
  "fuente": "https://enlace-a-la-noticia-o-fuente-oficial",
  "fuenteLabel": "Nombre del medio (ej: La Tercera)"
}
```

### Explicación de cada campo

| Campo | Qué va ahí | Valores válidos |
|---|---|---|
| `id` | Identificador único interno. No se muestra. | Texto sin espacios ni tildes, ej: `"m-lasrosas-2026"`. No puede repetirse entre casos. |
| `titulo` | Título de la tarjeta en la línea de tiempo. | Texto libre. |
| `personas` | Quién está involucrado y su cargo/partido. | Texto libre. |
| `partido` | Partido o etiqueta política tal como se muestra en la tarjeta. | Texto libre, ej: `"UDI"`, `"Independiente (afín a RN)"`. La página detecta automáticamente la sigla (UDI, RN, PS, PPD, DC, PC, Republicano, RD, PRI, Independiente) dentro de este texto para mostrar la insignia de color — no hace falta configurarla aparte. |
| `sector` | Determina el color y en qué categoría del gráfico de torta/dona cae. | Exactamente uno de: `"Izquierda"`, `"Centro"`, `"Derecha"`, `"Independiente"`, `"Multipartidista"`, `"Sin clasificar"`. |
| `nivel` | Nivel del caso. Afecta el filtro "Nivel". | `"Nacional"`, `"Regional"` o `"Municipal"`. |
| `comuna` | Comuna o región asociada, para que aparezca en el mapa. | El nombre exacto de una comuna chilena, o `null` si es un caso nacional sin comuna (como Penta o SQM). **Para que aparezca como polígono clickeable en el mapa de Santiago, el nombre debe coincidir exactamente con una de las 37 comunas del Gran Santiago que trae el mapa** (ver lista al final). Otras comunas de Chile igual funcionan para el filtro y para agrupar por región, pero no van a tener un polígono propio dentro del detalle de Santiago. |
| `imagen` | Foto de la persona principal del caso. | URL de una imagen (`"https://..."`), o `null` si no tienes una — en ese caso se muestra automáticamente una silueta genérica (como el avatar por defecto de Facebook), nunca un espacio roto. Si la URL deja de funcionar más adelante, también cae de vuelta a la silueta sola. Usa fotos con derechos claros para usar (por ejemplo Wikimedia Commons) — evita copiar la URL de una foto de portada de un diario, que casi siempre es protegida. |
| `anioInicio` | Año en que parte el caso. Ordena la línea de tiempo y se usa en el filtro de años. | Número, ej: `2024`. |
| `anioFin` | Año en que termina/se resuelve, si ya se sabe. | Número o `null`. |
| `monto` | Monto en **millones de pesos chilenos**. | Número (puede tener decimales), o `null` si no corresponde a un monto (por ejemplo, en casos de la categoría "sin dinero involucrado"). |
| `montoNota` | Aclaración corta de qué compone ese monto. | Texto o `null`. |
| `categoria` | Si el caso va en la pestaña normal o en "Sin dinero involucrado". | `"economico"` (o simplemente omite el campo) para casos de corrupción/dinero. `"no_economico"` para delitos sexuales, violencia intrafamiliar u otros sin plata de por medio. |
| `delito` | Solo para casos `"no_economico"`: qué tipo de delito, se muestra como etiqueta. | Texto corto, ej: `"Delito sexual"`, `"Violencia intrafamiliar"`. `null` en casos económicos. |
| `estadoCategoria` | Determina el color/categoría del "sello" y en qué grupo cae para los filtros de estado. | Exactamente uno de: `"investigacion"`, `"formalizado"`, `"condenado"`, `"cerrado_sin_condena"`. |
| `estado` | Texto exacto que se muestra en el sello y en la tarjeta. | Texto libre, corto. |
| `descripcion` | Resumen del caso. | Texto libre. Recuerda mantener presunción de inocencia: usa "formalizado por", "investigado por", no afirmes culpabilidad si no hay condena. |
| `fuente` | Enlace a la noticia o fuente oficial. | URL completa, con `https://`. |
| `fuenteLabel` | Nombre corto de esa fuente. | Texto, ej: `"El Mostrador"`, `"Cooperativa"`. |

### Sobre las insignias de partido (UDI, RN, PS…) y los logos oficiales

La página muestra una insignia de color con las iniciales del partido junto a cada caso, generada automáticamente a partir del texto que pongas en `partido` — no es necesario configurar nada. No se usan los logos oficiales de los partidos porque son marcas registradas y no había forma de obtener archivos de imagen confiables para incrustar desde aquí.

Si más adelante quieres reemplazar una insignia por el logo real, edita dentro de `index.html` el bloque `PARTY_BADGE` (búscalo con Ctrl+F) y completa el campo `logoUrl` del partido correspondiente con la URL de una imagen, por ejemplo:

```js
UDI: {label:"UDI", color:"#1b3a6b", logoUrl:"https://upload.wikimedia.org/.../Logo_UDI.png"},
```

Si esa URL deja de funcionar en el futuro, la página vuelve a mostrar la insignia de texto automáticamente — no se rompe el diseño.

### Antes de subir un cambio: valida el JSON

Un solo error de sintaxis (una coma de más, una comilla que falta) hace que **toda la página deje de cargar casos**. Antes de subir tu `casos.json` editado:

1. Copia todo el contenido del archivo.
2. Pégalo en un validador gratis como [jsonlint.com](https://jsonlint.com) o [jsonformatter.org](https://jsonformatter.org/json-validator).
3. Si marca error, te dice en qué línea está. Corrígelo y vuelve a validar.
4. Solo cuando diga "Valid JSON" (o similar), sube el archivo.

Errores típicos: olvidar una coma entre dos casos, dejar una coma después del último campo de un caso (antes del `}`), o usar comillas normales `"` en vez de tipográficas si copiaste desde Word.

## 3. Probarlo en tu computador antes de publicar

Como el HTML carga `casos.json` con `fetch()`, **no puedes simplemente abrir `index.html` haciendo doble clic** — los navegadores bloquean esa carga por seguridad cuando el archivo se abre como `file://`. Vas a ver la página vacía o un aviso de error arriba.

Para probarlo en tu computador, necesitas un pequeño servidor local (es más fácil de lo que suena):

**Si tienes Python instalado** (Mac y Linux ya lo traen; en Windows se instala gratis desde python.org):
1. Abre una terminal / símbolo del sistema.
2. Entra a la carpeta con `cd ruta/a/expediente-corrupcion-chile`.
3. Ejecuta: `python3 -m http.server 8000` (en Windows puede ser `python -m http.server 8000`).
4. Abre tu navegador en `http://localhost:8000`.
5. Para detener el servidor, `Ctrl+C` en la terminal.

**Alternativa sin instalar nada**: la extensión "Live Server" de VS Code hace lo mismo con un clic derecho → "Open with Live Server".

## 4. Publicarlo en internet (elige una opción)

### Opción A — GitHub Pages (recomendada: gratis, permanente, sin límite de tráfico)

1. Crea una cuenta gratis en [github.com](https://github.com) si no tienes una.
2. Arriba a la derecha, clic en el **+** → **New repository**.
3. Ponle un nombre (ej: `expediente-corrupcion-chile`), márcalo como **Public**, y crea el repositorio (no hace falta marcar ninguna otra opción).
4. Dentro del repositorio recién creado, clic en **"Add file" → "Upload files"**.
5. Arrastra los tres archivos (`index.html`, `casos.json`, y opcionalmente este `README.md`) y confirma con **"Commit changes"**.
6. Ve a la pestaña **Settings** del repositorio → en el menú izquierdo, **Pages**.
7. En "Branch", elige `main` y carpeta `/ (root)`, luego **Save**.
8. Espera 1-2 minutos. GitHub te va a mostrar la URL pública, algo como `https://tu-usuario.github.io/expediente-corrupcion-chile/`.

**Para agregar casos después**: entra al repositorio en github.com, abre `casos.json`, clic en el ícono de lápiz (editar), pega el JSON ya validado, y "Commit changes". El sitio se actualiza solo en 1-2 minutos, sin que tengas que volver a hacer nada más.

### Opción B — Netlify Drop (más rápida para probar, sin cuenta obligatoria al inicio)

1. Ve a [app.netlify.com/drop](https://app.netlify.com/drop).
2. Arrastra la carpeta completa (`index.html` + `casos.json`) directo al navegador.
3. En segundos te da una URL pública (algo como `random-name-123.netlify.app`).
4. Para poder editarla después y que no expire, te pide crear una cuenta gratis y "reclamar" el sitio.

### Opción C — Vercel, Cloudflare Pages, o cualquier hosting propio

Cualquier hosting que sirva archivos estáticos (HTML/JSON/CSS/JS tal cual) funciona igual: sube ambos archivos a la misma carpeta y listo. No necesitas base de datos, PHP, ni backend — es un sitio 100% estático.

## 5. Las 37 comunas con polígono propio en el mapa de Santiago

Estas son las únicas comunas que van a aparecer como forma clickeable dentro del detalle "Metropolitana" del mapa (cualquier otra comuna de Chile funciona igual en los filtros y en la línea de tiempo, solo que no tiene polígono propio ahí dentro):

La Reina, Maipú, Quilicura, Lo Barnechea, San Bernardo, Puente Alto, San Joaquín, Cerrillos, Peñalolén, Ñuñoa, Santiago, Las Condes, La Pintana, La Florida, Renca, Huechuraba, Vitacura, Estación Central, Macul, Pudahuel, San José de Maipo, Pirque, Lo Espejo, San Ramón, Recoleta, El Bosque, Conchalí, Cerro Navia, La Granja, Independencia, Quinta Normal, Providencia, Pedro Aguirre Cerda, Lo Prado, Padre Hurtado, La Cisterna, San Miguel.

## 6. Equivalencias de montos

En la barra de filtros, a la izquierda, hay un filtro **"Ver montos como"** con siete opciones: Pesos (CLP), 🏥 Hospital, 🩺 CESFAM, 🏫 Escuela, 🚒 Carro de bomberos, ⚽ Cancha y 👵 Pensión (PGU). Al elegir una, **todos** los montos del sitio (la cifra principal arriba, la ficha de casos económicos, la línea de tiempo, el detalle del mapa y el gráfico de montos acumulados) cambian de "$X millones" al número de esa unidad equivalente, con su ícono — por ejemplo, si eliges Hospital, un caso de $150.000 millones se muestra como "5,2 🏥".

Las cifras de referencia que usa están adentro de `index.html`, en el bloque `EQUIVALENCIAS` (búscalo con Ctrl+F) — cada una tiene su fuente real, no son promedios inventados. Si quieres actualizar algún valor de referencia (por inflación, por ejemplo) o agregar una unidad nueva, edita o agrega una entrada ahí; aparece automáticamente en el filtro de la izquierda sin tocar nada más.

## 7. Panel de administración (admin.html)

`admin.html` es una página aparte, pensada solo para ti, con la que puedes **agregar y modificar casos con un formulario**, **importar y exportar CSV** y **publicar los cambios** sin editar JSON a mano. Se sube al mismo repositorio que el resto de los archivos y queda disponible en `https://tu-usuario.github.io/tu-repositorio/admin.html`.

### Cómo funciona el login (y qué tan seguro es)

Tu sitio es estático: no tiene servidor propio donde guardar usuarios y contraseñas. Por eso el panel usa **GitHub como sistema de acceso**: para ingresar necesitas un *token personal* de GitHub, y solo quien tenga ese token puede guardar cambios en el repositorio. Dicho de otro modo:

- La página `admin.html` es pública (cualquiera puede abrirla), pero **sin un token válido no puede ver ni modificar nada**: la lista de casos se lee y se guarda a través de la API de GitHub, que exige el token.
- El token **nunca está escrito en el código**. Se guarda únicamente en la memoria de la pestaña mientras la tienes abierta, y se borra al cerrar sesión o cerrar la pestaña.
- Cada guardado queda registrado en GitHub (pestaña *Commits*): quién cambió qué y cuándo, y puedes volver a cualquier versión anterior si algo sale mal.

Límites que conviene conocer: no hay usuarios distintos ni permisos por rol (cualquiera con un token válido puede editar todo), y un token filtrado equivale a una llave filtrada. Si más adelante necesitas varias personas con cuentas y permisos separados, el siguiente paso es una base de datos con autenticación propia (por ejemplo Supabase o Firebase); la estructura de campos de este panel se puede reutilizar tal cual.

### Cómo crear el token (una sola vez)

1. En GitHub: tu foto de perfil → **Settings** → **Developer settings** → **Personal access tokens** → **Fine-grained tokens** → **Generate new token**.
2. *Resource owner*: tu cuenta. *Repository access*: **Only select repositories**, y elige solo el repositorio del expediente.
3. *Permissions* → *Repository permissions* → **Contents: Read and write**. No necesita ningún otro permiso.
4. Pon una fecha de vencimiento (por ejemplo 90 días), genera el token y cópialo. GitHub lo muestra una sola vez.
5. Si lo pierdes o sospechas que lo vio alguien más, revócalo en esa misma pantalla y crea otro.

### Uso diario

1. Abre `.../admin.html`, escribe tu usuario de GitHub, el nombre del repositorio, la rama (`main`) y pega el token. El panel recuerda los datos del repositorio, **nunca el token**.
2. Busca y filtra casos. Usa **Editar**, **Duplicar** o **Eliminar**, o **Nuevo caso** para abrir el formulario. El formulario valida los datos antes de guardar (campos obligatorios, valores permitidos, enlaces, años, montos, ID único).
3. Los cambios quedan como **borrador** (un contador arriba te dice cuántos hay). Cada caso nuevo o editado se marca en la tabla. **Deshacer todo** descarta el borrador.
4. Cuando estés conforme, **Publicar cambios**: te muestra un resumen (agregados, editados, eliminados) y guarda una nueva versión de `casos.json` en GitHub. El sitio público se actualiza en 1 a 2 minutos.

Si al publicar aparece un aviso de "el archivo cambió en GitHub", significa que alguien lo modificó mientras trabajabas: usa **Descargar JSON** para respaldar tu borrador, **Recargar desde GitHub** y vuelve a aplicar tus cambios. La primera vez que publiques, el archivo `casos.json` se reordena (los campos quedan siempre en el mismo orden); es normal.

> El panel no funciona dentro de la vista previa de claude.ai (esa vista bloquea las conexiones externas). Úsalo desde tu sitio publicado o en tu computador con `python3 -m http.server`.

### Formato CSV

El CSV tiene **una fila por caso** y estas 19 columnas, en este orden (los encabezados deben escribirse exactamente así):

`id; titulo; personas; partido; sector; nivel; comuna; imagen; anioInicio; anioFin; monto; montoNota; categoria; delito; estadoCategoria; estado; descripcion; fuente; fuenteLabel`

Reglas:

- **Codificación UTF-8** (para que se vean bien las tildes y la ñ). El panel acepta separador **punto y coma** (el que usa Excel en español) o **coma**, y lo detecta solo.
- **Textos con comas, punto y coma o saltos de línea** van entre comillas dobles (`"..."`); una comilla dentro del texto se escribe doble (`""`). Excel y Google Sheets lo hacen automáticamente al guardar como CSV.
- **`id`**: identifica al caso. Si el `id` ya existe, ese caso se **actualiza**; si no existe, se **agrega** como caso nuevo. Si lo dejas vacío en una fila nueva, se genera uno solo.
- **Casos nuevos**: deben traer todos los campos obligatorios (todos menos `comuna`, `imagen`, `anioFin`, `monto`, `montoNota` y `delito`).
- **Casos existentes**: las columnas que no incluyas y las celdas vacías **no modifican nada**. Para borrar un dato (por ejemplo quitar un monto) usa el formulario.
- **`sector`**: Izquierda, Centro, Derecha, Independiente, Multipartidista o Sin clasificar. **`nivel`**: Nacional, Regional o Municipal. **`categoria`**: `economico` o `no_economico`. **`estadoCategoria`**: `investigacion`, `formalizado`, `condenado` o `cerrado_sin_condena`.
- **`monto`** en millones de pesos, con punto como decimal (`147.29`) y sin separador de miles. También se entiende la coma decimal (`147,29`).
- **`anioInicio` / `anioFin`**: años de 4 dígitos.
- Los textos no pueden contener los caracteres `<` ni `>`.

Al importar, el panel muestra una **vista previa** (cuántos casos son nuevos, cuáles se actualizan, cuáles quedan sin cambios y qué filas tienen errores, con el número de fila y el motivo) antes de tocar nada. Las filas con errores se omiten; el resto se aplica al borrador y recién se publica cuando tú lo decides. Hay dos modos: **Fusionar** (actualiza y agrega, no borra) y **Reemplazar todo** (el CSV pasa a ser la lista completa y elimina los casos que no estén en él; pide confirmación).

**Para mantener la información al día:** cuando necesites datos actualizados, pídemelos en este mismo formato y te entrego un CSV con estas 19 columnas (UTF-8, separador `;`) que puedes cargar con **Importar CSV**. Si te sirve revisarlo antes en Excel, `casos.csv` y `plantilla-casos.csv` son el punto de partida.

## 8. Ideas para más adelante (opcional)

- Si el archivo `casos.json` crece mucho y se vuelve incómodo de editar a mano, puedes migrar a una hoja de Google Sheets y exportarla a JSON, o a una base de datos real (Airtable, Supabase). Mientras el formato final que le llega a `index.html` sea un array JSON con estos mismos campos, la página sigue funcionando igual.
- Si quieres un dominio propio (en vez de `.github.io` o `.netlify.app`), tanto GitHub Pages como Netlify permiten conectar un dominio comprado en cualquier proveedor (NIC Chile, GoDaddy, etc.) desde su panel de configuración.
