# Menú AR de Jugos: boceto open source

El cliente escanea un QR en la mesa, se abre la web, apunta la cámara a la tarjeta del menú y aparece un vaso de jugo en 3D encima. Con las flechas cambia de sabor, precio e ingredientes.

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

## Paso 2: Mostrarle la tarjeta a la cámara

Abre esta imagen en tu celular (o imprímela) y ponla frente a la webcam:
https://cdn.jsdelivr.net/gh/hiukim/mind-ar-js@1.2.5/examples/image-tracking/assets/card-example/card.png

Debe aparecer el vaso girando, y abajo un panel con nombre, ingredientes y precio.

## Paso 3: Editar tu menú

En `ar.html`, busca el arreglo `JUGOS` y cambia los nombres, las descripciones, los precios y los colores.

## Paso 4: Usar TU propia imagen (logo, carta, individual de mesa)

1. Entra al compilador gratuito de MindAR, que corre en el navegador: https://hiukim.github.io/mind-ar-js-doc/tools/compile
2. Sube tu imagen. Funcionan mejor las imágenes con mucho detalle y contraste; evita logos planos de un solo color.
3. Descarga el archivo `targets.mind` y ponlo en esta carpeta.
4. En `ar.html` cambia `imageTargetSrc: https://...card.mind` por `imageTargetSrc: ./targets.mind`.

Truco: si subes varias imágenes a la vez (una por jugo), cada una tendrá su `targetIndex: 0, 1, 2...` y podrás poner un jugo distinto sobre cada una.

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
