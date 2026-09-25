# Menú AR de Jugos: boceto open source

El cliente escanea un QR en la mesa, se abre la web y apunta la cámara a un producto de la carta. Encima aparece **ese** jugo en 3D, con su nombre y precio en un recuadro pequeño. Si mueve la cámara a otro producto, cambia el 3D.

## Archivos

| Archivo | Para qué sirve |
|---|---|
| `index.html` | Página de inicio (a la que lleva el QR) |
| `ar.html` | La experiencia de realidad aumentada |
| `carta.html` | Carta de prueba con 4 productos para mostrar en pantalla o imprimir |
| `jugos.js` | **Tu menú**: nombres, precios y colores |
| `img/producto-N.png` | Imagen de cada producto en la carta |
| `targets.mind` | Las 4 imágenes "aprendidas" por la cámara |
| `generar.html` | Regenera las imágenes y `targets.mind` si cambias `jugos.js` |

## Herramientas (todas gratis / open source)

| Pieza | Herramienta | Licencia |
|---|---|---|
| Motor 3D web | [A-Frame](https://aframe.io) (sobre three.js) | MIT |
| Realidad aumentada con imagen | [MindAR](https://github.com/hiukim/mind-ar-js) | MIT |
| Modelos 3D reales (opcional) | [Blender](https://www.blender.org) | GPL |
| AR "poner el vaso en la mesa" (opcional) | [model-viewer](https://modelviewer.dev) | Apache 2.0 |
| Hosting con HTTPS | GitHub Pages / Netlify / Cloudflare Pages | Gratis |

## Paso 1: Probar en el PC (con la webcam)

```powershell
cd $HOME\menu-ar-jugos
python -m http.server 8000
```
Abre http://localhost:8000 en Chrome, entra a "Abrir cámara AR" y permite la cámara.

## Paso 2: Mostrarle la carta a la cámara

Abre `carta.html` en otra pantalla (o imprímela) y apunta la cámara a uno de los 4 cuadros. Debe aparecer el vaso de ese producto girando, con un recuadro pequeño abajo que muestra el nombre, los ingredientes y el precio. Si apuntas a otro cuadro, cambia el jugo.

## Paso 3: Editar tu menú

Cambia nombres, precios y colores en `jugos.js`. Si cambias algo que se ve en la carta (nombre, precio o colores), vuelve a generar las imágenes y `targets.mind`:
1. Con el servidor local corriendo, abre http://localhost:8000/generar.html.
2. Espera a que diga "LISTO" y descarga los archivos.
3. Reemplaza `img/producto-N.png` y `targets.mind`.

## Paso 4: Usar las fotos reales de TU carta

1. Recorta la foto de cada producto de tu carta como una imagen aparte.
2. Súbelas **todas juntas y en orden** al compilador gratuito de MindAR, que corre en el navegador: https://hiukim.github.io/mind-ar-js-doc/tools/compile
3. Descarga `targets.mind` y reemplaza el de esta carpeta.
4. Deja `jugos.js` en el mismo orden: la primera imagen es el producto 1, la segunda el producto 2, y así sucesivamente.

Consejos: usa fotos con detalle y contraste, evita imágenes muy parecidas entre sí, imprime cada foto de al menos 5–6 cm y no pases de unos 10–15 productos por archivo.

## Paso 5: Probar en el celular (requiere HTTPS)

Los celulares solo dan acceso a la cámara en sitios HTTPS. La forma gratis:
1. Crea un repositorio en GitHub y sube esta carpeta.
2. Ve a Settings → Pages → Deploy from branch → `main`.
3. Tu menú quedará en `https://TU-USUARIO.github.io/menu-ar-jugos/`.

## Paso 6: El QR para la mesa

Genera un QR con esa URL usando cualquier generador gratuito (o `pip install qrcode` y luego `qr "https://..." > qr.png`). Imprímelo junto a la tarjeta del menú.

## Paso 7 (siguiente nivel): modelos 3D realistas

El vaso actual está hecho con figuras básicas para el boceto. Para darle realismo:
1. Modela el vaso en Blender o descarga uno gratis con licencia CC0/CC-BY (Poly Pizza, Sketchfab con filtro "downloadable" o Poly Haven).
2. Expórtalo como `.glb` (File → Export → glTF 2.0) y mantenlo por debajo de ~3 MB.
3. En `ar.html`, reemplaza las figuras por:
   ```html
   <a-gltf-model src="./jugo-naranja.glb" scale="0.5 0.5 0.5"></a-gltf-model>
   ```

## Alternativa: AR sin tarjeta (vaso "sobre la mesa real")

Con `<model-viewer>` + un `.glb` el cliente puede colocar el jugo en la mesa, a tamaño real, sin necesidad de una imagen objetivo. En Android usa Scene Viewer y en iPhone usa Quick Look (este último necesita además un `.usdz`, que puedes exportar desde Blender).
```html
<script type="module" src="https://cdn.jsdelivr.net/npm/@google/model-viewer/dist/model-viewer.min.js"></script>
<model-viewer src="jugo.glb" ios-src="jugo.usdz" ar ar-modes="webxr scene-viewer quick-look"
              camera-controls auto-rotate style="width:100%;height:400px"></model-viewer>
```
