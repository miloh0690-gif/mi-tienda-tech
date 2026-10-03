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

Catálogo público con visor 3D para los modelos más recientes. Un solo archivo
HTML, sin backend.

La raíz del repo es la raíz del sitio (así Vercel lo publica sin configuración).

```
index.html          ← todo el sitio
LEEME.md            ← este archivo
img/                ← fotos de producto (vacía)
modelos/            ← 3 .glb, ya comprimidos
```

## 🚨 Bloqueo: dos de los tres modelos no se pueden publicar

La licencia viene metida dentro del `.glb`, en `asset.extras`. La leí de los
archivos:

| Archivo | Autor | Licencia | ¿En una tienda? |
|---|---|---|---|
| `iphone_18_pro_max__high_quality_3d_model.glb` | Pro Animator (Riju.mandal) | **CC-BY-4.0** | Sí, **con crédito visible obligatorio** |
| `samsung_galaxy_s26_ultra.glb` | zhe_kan | **CC-BY-NC-SA-4.0** | **NO. Prohibido uso comercial.** |
| `macbook_pro_14-inch_m5.glb` | Apple User | **Sketchfab Standard** | **NO. Prohibido uso comercial.** |

`NC` significa *NonCommercial*, y la Sketchfab Standard también. Poner el modelo
en el catálogo de una tienda que lo vende es uso comercial, no importa que el
pedido se cierre por WhatsApp.

**Qué hay que hacer antes de publicar:**

1. **iPhone 18 Pro Max** → se puede usar. La CC-BY exige atribución, así que el
   campo `modeloCredito` tiene que quedar lleno. Ya está, y el visor lo muestra
   debajo de los botones. **No lo borres.**
2. **Samsung y MacBook** → buscar otros modelos con licencia comercial, o
   renderizar desde cero en Blender, o comprar en una biblioteca de 3D con
   licencia de tienda (Sinverg Studios, CGTrader, TurboSquid con plan
   comercial). Mientras tanto están cargados pero **no se deben publicar**.

Los tres son modelos de la comunidad de Sketchfab, unofficial. Apple y Samsung
no reparten derechos sobre su diseño industrial para 3D.

---

## Abrirlo

Doble clic en `index.html`. O para probar en el celular, servedor local:

```
python3 -m http.server 8080
```

y abrir `http://<tu-ip>:8080` desde el teléfono. **No abras el archivo con
`file://` en iPhone**: Safari mobile no carga bien los módulos ES en ese
esquema y el visor 3D no arranca.

## Qué hay que completar antes de publicar

| Qué | Dónde | Nota |
|---|---|---|
| Número de WhatsApp | `WHATSAPP_NUMERO` (línea ~380) | Hoy es un placeholder inventado `591000000000`. Sin esto todos los botones abren un chat al número equivocado. |
| Fotos | `img/` | Hoy no existe. Las rutas están en el campo `img` de cada producto. Si falta el archivo, el `<img>` se borra solo y queda el ícono de la categoría. |
| Modelos 3D | `modelos/` | **Ya están los 3.** Los de Samsung y MacBook tienen licencia que prohíbe uso comercial: ver el bloqueo de arriba. |
| Precios | `precioBs` | `null` = el sitio muestra "Consultar". No inventes precio: es lo más fácil de cambiar y después es una pelea con el cliente. |
| Especificaciones | `specs` | Ídem. Si está vacío, no se dibuja la lista. |
| Colores | `muestras` | Solo son el color del botón y el texto del mensaje de WhatsApp. **No cambia la imagen**, la foto la tenés que subir por color. |

## Agregar un producto

Todo vive en el array `PRODUCTOS`. Un objeto:

```js
{
  id: "iphone-17-pro-max",   // único, sin espacios
  nombre: "iPhone 17 Pro Max",
  cat: "celulares",          // tiene que coincidir con un id de CATEGORIAS
  nuevo: true,
  precioBs: 6499,            // número entero de bolivianos, o null
  resumen: "Una frase. Va al mensaje de WhatsApp y al subtítulo.",
  img: "img/mi-modelo.jpg",
  alt: "descripción para lectores de pantalla",
  modelo3d: "modelos/mi-modelo.glb",   // null = sin 3D
  modeloOcultar: ["Object_22"],        // opcional: mallas del fondo a esconder
  modeloCredito: "Modelo 3D de ... CC BY ...",  // si la licencia exige crédito
  muestras: [
    { nombre: "Negro", hex: "#1c1c1e" }
  ],
  specs: [
    { k: "Pantalla", v: "6.9\"" },
    { k: "Chip", v: "A19 Pro" }
  ]
}
```

**La regla de "solo los más recientes tienen 3D" es `modelo3d`.** Si el campo es
`null`, no aparece el botón. No hay una lista aparte que mantener.

`modeloOcultar` es para los Sketchfab que traen fondo. Ver abajo.

El orden en que aparecen sale de `CATEGORIAS`, no del array.

## Agregar una categoría

Una entrada más en `CATEGORIAS` con `id`, `titulo`, `bajada`. El id también tiene
que estar en la navegación del `<header>` si querés link en la barra superior.

## Cómo funciona el visor 3D

- **three.js 0.186.1 desde unpkg**, fijado por `importmap`. No se copia al
  archivo ni se instala nada.
- **No se descarga hasta que el usuario aprieta "Ver en 3D".** `modulos()` hace
  el `import()` diferido. Si nadie abre el visor, three.js nunca baja. Eso
  mantiene la primera carga liviana.
- Al cerrar el modal se destruye el renderer, la geometría, los materiales, las
  texturas y el contexto WebGL. Si no, abrir y cerrar 10 veces deja la GPU
  trabada en el teléfono.
- La luz es `RoomEnvironment` (una escena de estudio incluida en three), no un
  `.hdr` externo. No tenés que buscar nada.
- El encuadre se calcula con `Box3` sobre el modelo: no importa si tu `.glb`
  está en centímetros, pulgadas o centímetros ridículos, siempre entra en
  pantalla.
- **Draco y KTX2 están cableados.** Si tu export salió comprimido, funciona. Si
  no, los decoders no se descargan nunca.

## Formato de los modelos

Preferí `.glb` (un solo archivo). Si usás `.gltf`, el `.bin` y las texturas
tienen que estar **en la misma carpeta y con nombres relativos correctos**, y el
`.gltf` tiene que referenciarlos tal cual. Con `.glb` no hay ese problema.

### El problema de los fondos de Sketchfab

Las descargas de Sketchfab traen la escena del autor: una plataforma, un piso o
un fondo. Si eso se cuela en el encuadre, el producto se ve como una mota.

Lo que traen tus tres archivos:

| Archivo | Nodo de fondo | Tamaño del fondo | Producto real |
|---|---|---|---|
| iPhone 18 Pro Max | `Object_22` | 260 x 13 x 260 | 8.97 |
| Galaxy S26 Ultra | `Object_25` y `Object_77` | 0.70 x 1.63 x 0.03 | 0.164 |
| MacBook Pro | ninguno | — | 31.17 |

Por eso existe `modeloOcultar`: son los nombres de las mallas que hay que
esconder, y el visor arma el encuadre **solo con las que quedan visibles**.

Si no ponés la lista, el visor la arma solo: toma la mediana de las 10 mallas más
grandes y esconde todo lo que pase de 6 veces esa mediana. Con tus tres archivos
da el resultado correcto:

- iPhone → esconde `Object_22` (30x la mediana)
- Samsung → esconde `Object_25` y `Object_77` (9.9x)
- MacBook → no esconde nada (la mediana ya es del tamaño del producto)

La lista manual manda sobre el cálculo. Si el cálculo se equivoca con un modelo
nuevo, pasás los nombres a mano.

### Peso y complejidad de los tuyos

| Archivo | Peso | Vértices | Mallas | Texturas | Veredicto |
|---|---|---|---|---|---|
| `samsung_galaxy_s26_ultra.glb` | 2.23 MB | 44.470 | 64 | 3 (0.33 MB) | Bien |
| `iphone_18_pro_max__high_quality_3d_model.glb` | 5.95 MB | 28.628 | 18 | 5 (3.84 MB) | Aceptable |
| `macbook_pro_14-inch_m5.glb` | 10.19 MB | 145.692 | 44 | 11 (2.23 MB) | **Pesado** |

El del MacBook pesa 10 MB: en 4G son 8-12 segundos solo de modelo. Bajale las
texturas: 10 PNG en un `.glb` es lo que más engorda. `gltf-transform optimize
--texture-compress webp` o pasarlo por Blender con texturas de 1024 px.

Ninguno de los tres usa Draco ni KTX2, así que los decoders del visor no se
descargan nunca. Si los recomprimís con Draco+KHR_texture_basisu, los soportamos
sin tocar el código.

Objetivos razonables: menos de 4 MB, menos de 150 mil vértices, texturas de
1024 px en JPEG o KTX2, y materiales combinados (el del MacBook tiene 28 con
nombres como `zNRfbdNyoCOxSDD`, que es basura del exportador).

## Lo que NO hay, y por qué

- **No hay carrito ni pasarela de pago.** El pedido sale por WhatsApp. Es lo que
  hace el sitio de [[Moon Company]] y evita todo el tema de claves de cobro,
  webhooks y devolución de dinero.
- **No hay backend.** Por lo tanto **el precio es texto, no una verdad**. Si
  mañana esto cobra de verdad, el precio se tiene que calcular en el servidor en
  enteros de centavos, no acá. Ver la regla de la skill `app-security`.
- **No hay stock.** El precio "Consultar" es a propósito: mejor eso que un
  número viejo que hace perder la venta.
- **No hay SEO por producto.** Todas las tarjetas están en el mismo HTML. Si
  querés que cada modelo se comparta y se indexe por separado, cada uno necesita
  su URL, y ahí sí se cae el "un solo archivo".

## Bugs que ya están resueltos (no los toques)

- El modal destruye el visor activo **antes** de cambiar el contenido, así que
  abrir la ficha desde el visor no deja un renderer huérfano.
- El botón "Ver ficha" dentro del visor usa `data-ficha`, no `data-ver3d`. Con
  `data-ver3d` se re-abría el visor en vez de la ficha.
- Las fotos rotas se borran solas con `onerror` y aparece el ícono de categoría.
- El foco está atrapado dentro del modal mientras está abierto.

## Qué NO se pudo verificar

No hay navegador ni Node en la máquina donde se escribió esto. Se verificó:

- HTML bien formado (parser, sin tags sin cerrar).
- Balance de llaves, paréntesis y corchetes del script módulo.
- Que todas las URLs de three.js, Draco y KTX2 respondan 200.
- Que no queden caracteres corruptos.
- Que las tres rutas de `modelo3d` apunten a archivos que existen.
- Licencias, extensiones, mallas, vértices y tamaños leídos de los tres `.glb`.
- Que el umbral de 6× del oculto de fondos clasifique bien los tres modelos.

**Quedó sin verificar: que el 3D se vea bien.** Eso hay que abrirlo en un
navegador. Lo primero que hay que mirar: que el modelo aparezca centrado, que no
se vea la plataforma del iPhone, y que se pueda girar.

## Para probarlo rápido

Levantá el servidor local y apretá "Ver en 3D" en el **iPhone 18 Pro Max**:

```
python3 -m http.server 8080
```

Es el caso más duro de los tres: 6 MB y una plataforma de 260 unidades que hay
que esconder. Si ese sale bien, el Samsung y el MacBook salen.
