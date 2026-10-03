# Moon Tech Mobile

Catalogo de venta de equipos con visor 3D para los modelos mas recientes.
Un solo `index.html`, sin backend.

**Antes de publicar:** leer [LEEME.md](LEEME.md). Dos de los tres modelos 3D
tienen licencia que prohibe uso comercial, y los precios estan sin cargar a
proposito.

## Que hay que completar

| Que | Donde |
|---|---|
| Numero de WhatsApp | `WHATSAPP_NUMERO` en el script |
| Fotos de producto | `img/` (vacio) y el campo `img` de cada producto |
| Precios | campo `precioBs` — `null` muestra "Consultar" |
| Especificaciones | campo `specs` — vacio no dibuja la lista |

## Arrancar local

```
python3 -m http.server 8080
```

Abrir `http://localhost:8080` **desde la raiz de este repo**. No abrir el
archivo con doble clic ni servir otra carpeta: si el servidor no arranca aqui,
el navegador muestra un listado de archivos y no se ve nada del sitio.

## Licencias de los modelos 3D

| Archivo | Licencia | ¿En una tienda? |
|---|---|---|
| `iphone_18_pro_max__high_quality_3d_model.glb` | CC-BY-4.0 | Si, con credito visible (ya esta en `modeloCredito`) |
| `samsung_galaxy_s26_ultra.glb` | CC-BY-NC-SA-4.0 | **Prohibido uso comercial** |
| `macbook_pro_14-inch_m5.glb` | Sketchfab Standard | **Prohibido uso comercial** |

Detalle y alternativas en [LEEME.md](LEEME.md).

## Desplegar

Sitio estatico: la raiz del repo es la raiz del sitio. En Vercel, importar el repo
y no configurar build command ni output directory.

---

# Moon Tech Mobile — sitio

Catalogo publico con visor 3D para los modelos mas recientes. Un solo archivo
HTML, sin backend.

## Abrirlo

Doble clic en `index.html`. O para probar en el celular, servidor local:

```
python3 -m http.server 8080
```

y abrir `http://<tu-ip>:8080` desde el telefono. **No abras el archivo con
`file://` en iPhone**: Safari mobile no carga bien los modulos ES en ese
esquema y el visor 3D no arranca.

## Que hay que completar antes de publicar

| Que | Donde | Nota |
|---|---|---|
| Numero de WhatsApp | `WHATSAPP_NUMERO` | Hoy es un placeholder inventado. Sin esto todos los botones abren un chat al numero equivocado. |
| Fotos | `img/` | Hoy no existe. Las rutas estan en el campo `img` de cada producto. Si falta el archivo, el `<img>` se borra solo y queda el icono de la categoria. |
| Modelos 3D | `modelos/` | Ya estan los 3. Los de Samsung y MacBook tienen licencia que prohibe uso comercial: ver el bloqueo de arriba. |
| Precios | `precioBs` | `null` = el sitio muestra "Consultar". |
| Especificaciones | `specs` | Idem. Si esta vacio, no se dibuja la lista. |
| Colores | `muestras` | Solo el color del boton y el texto del WhatsApp. No cambia la imagen. |

## Agregar un producto

Todo vive en el array `PRODUCTOS`:

```js
{
  id: "iphone-18-pro-max",
  nombre: "iPhone 18 Pro Max",
  cat: "celulares",
  nuevo: true,
  precioBs: 6499,
  resumen: "Una frase.",
  img: "img/mi-modelo.jpg",
  alt: "descripcion para lectores de pantalla",
  modelo3d: "modelos/mi-modelo.glb",
  modeloOcultar: ["Object_22"],
  modeloCredito: "Modelo 3D de ... CC BY ...",
  muestras: [{ nombre: "Negro", hex: "#1c1c1e" }],
  specs: [{ k: "Pantalla", v: '6.9"' }]
}
```

**La regla de "solo los mas recientes tienen 3D" es `modelo3d`.** Si es `null`,
no aparece el boton. No hay una lista aparte que mantener.

`modeloOcultar` es para los Sketchfab que traen fondo.

## Como funciona el visor 3D

- **three.js 0.186.1 desde unpkg**, fijado por `importmap`.
- **No se descarga hasta que el usuario aprieta "Ver en 3D".**
- Luz de estudio con `RoomEnvironment`, sin `.hdr` externo.
- El encuadre se calcula con `Box3`: no importa la escala del `.glb`.
- **Draco y KTX2 estan cableados** y son requeridos en los tres modelos.
- Destruye renderer, geometria, materiales, texturas y contexto WebGL al cerrar.

### El CSP y el 3D

El meta tag de CSP necesita tres cosas que no estan por defecto:

- `unpkg.com` en `script-src` (three.js y addons)
- `unpkg.com` en `connect-src` (el decificador de Draco se baja aparte)
- `'wasm-unsafe-eval'` en `script-src` (el decificador compila WebAssembly)

Sin el ultimo, **Chrome falla el visor en silencio**. Es el error mas dificil
de ver de todo el 3D.

## Lo que NO hay, y por que

- **No hay carrito ni pasarela.** El pedido sale por WhatsApp.
- **No hay backend.** El precio es texto, no verdad.
- **No hay stock.** El precio "Consultar" es a proposito.
- **No hay SEO por producto.** Todas las tarjetas estan en el mismo HTML.

## Bugs que ya estan resueltos (no los toques)

- El modal destruye el visor activo **antes** de cambiar el contenido.
- El boton "Ver ficha" dentro del visor usa `data-ficha`, no `data-ver3d`.
- Las fotos rotas se borran solas con `onerror`.
- El foco esta atrapado dentro del modal mientras esta abierto.

## Que NO se pudo verificar

No hay navegador en la maquina donde se escribio esto. Se verifico:

- HTML bien formado (parser, sin tags sin cerrar).
- Balance de llaves, parentesis y corchetes del script modulo.
- Modulo compilado con JavaScriptCore: `SINTAXIS OK`.
- Modulo ejecutado con DOM simulado: genera 4 categorias, 10 productos, 3 botones
  de 3D, 20 muestras de color y 10 enlaces de WhatsApp.
- Que todas las URLs de three.js, Draco y KTX2 respondan 200.
- Que las tres rutas de `modelo3d` apunten a archivos que existen.
- Licencias, extensiones, mallas, vertices y tamanos leidos de los tres `.glb`.
- Que el umbral de 6x del oculto de fondos clasifique bien los tres modelos.

**Quedo sin verificar: que el 3D se vea bien.** Eso hay que abrirlo en un
navegador. Lo primero que hay que mirar: que el modelo aparezca centrado, que no
se vea la plataforma del iPhone, y que se pueda girar.

## NODO (el sitio anterior)

Este repo antes era **NODO — Equipos Electronicos**, una tienda de agosto que
leia el catalogo de una Google Sheet por Apps Script. NODO es **otro cliente**,
no el mismo. Su codigo quedo en `_nodo/` y en el historial de git.
