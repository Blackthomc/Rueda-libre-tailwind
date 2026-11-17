# Bitácora · Rueda Libre v2.5 (Bootstrap y Tailwind)

**Autor:** Thomas Calle Durango
**Proyecto:** Rueda Libre, sitio de alquiler de bicicletas y rutas guiadas en Medellín

Este documento recoge lo que hice en las semanas 6 y 7: pasar el sitio de CSS propio a **Bootstrap 5.3.3** y construir después **Rueda Nocturna** con **Tailwind CSS 4.1.10**. Todos los datos salen de los archivos del proyecto.

---

# Semana 6: Bootstrap (Mini retos 5 a 8)

## 1. Mini reto 5: la flota con la grilla

La flota tiene siete modelos (Urbana Clásica, Urbana Plegable, Montaña Trail, Gravel Aventura, Eléctrica Urbana, Infantil y Tándem). Con Bootstrap la distribución quedó en clases dentro del HTML: `container`, `row`, `g-4` y, en cada tarjeta, `col-12 col-sm-6 col-lg-4 col-xl-3`.

Con esas clases la flota pasa de 1 columna (menos de 576 px) a 2 (desde 576 px), a 3 (desde 992 px) y a 4 (desde 1200 px), sin escribir un solo media query. Los espacios entre tarjetas son parejos gracias a `g-4`, y todo funciona mobile-first desde el principio. El Tándem, como cualquier modelo nuevo, entra a la grilla agregando una `col-*` más dentro del mismo `row`, sin tocar CSS.

Lo que perdí fue control. Los puntos de quiebre y el tamaño de los espacios ahora son los de Bootstrap, no los míos. Tampoco puedo repetir lo que hacía con `auto-fit` y `minmax`, donde las columnas se acomodaban solas según el espacio disponible: ahora el número de columnas es fijo por rango de ancho.

## 2. Mini reto 6: beneficios y franja sin CSS

Cinco clases que busqué en la documentación oficial y usé en `index.html`:

| # | Clase | ¿Qué hace? | Dónde la usé |
|---|-------|------------|--------------|
| 1 | `d-none d-md-block` | Oculta el elemento y lo muestra desde 768 px | Imagen decorativa de la franja "¿Listo para rodar?" |
| 2 | `flex-column flex-md-row` | Apila en columna y pasa a fila desde 768 px | Encabezado y franja final |
| 3 | `text-bg-success` / `text-bg-dark` | Pone un fondo y un color de texto que contrasta con él | Botón "Reservar ahora" y franja oscura |
| 4 | `rounded-pill` | Redondea del todo los bordes, como una píldora | Etiquetas de "¿Por qué Rueda Libre?" |
| 5 | `fs-3` / `fs-4` | Cambia el tamaño de letra sin escribir CSS | Marca del encabezado y texto de la franja |

La sección de beneficios (bicicletas revisadas, casco y candado, guías certificados y 3 sedes) y la franja final se armaron solo con clases de Bootstrap, sin una línea de CSS propio.

## 3. Mini reto 7: integrar Bootstrap en inicio

Al combinar mi CSS con Bootstrap aparecieron tres conflictos. Así quedaron resueltos en el código:

| # | Conflicto | Por qué pasaba | Cómo lo resolví |
|---|-----------|----------------|-----------------|
| 1 | Un `* { margin: auto }` repartía el espacio libre de la última fila de la grilla | Mi regla universal competía con los márgenes de Bootstrap | Eliminé la regla; el reseteo lo hace Bootstrap |
| 2 | `body { background-color: white }` tapaba el color crema de la marca | Mi fondo pisaba el del framework | Lo quité y el crema entra por la variable `--bs-body-bg: #FAF6EF` |
| 3 | El `a:hover` de Bootstrap pintaba la marca y su regla de listas sangraba el menú | Estilos globales de Bootstrap sobre `a` y `ul` | Agregué `.marca:hover` en mi CSS y `list-unstyled` al menú |

Además adapté los colores de la marca a Bootstrap sobrescribiendo sus variables en `:root` (`--bs-success`, `--bs-warning`, `--bs-dark-rgb`, `--bs-secondary-rgb`, entre otras). Así los componentes del framework usan mi verde ruta, mi naranja pedal y mi azul noche.

Comportamiento de `index.html` según sus clases:

- **Menos de 768 px:** el encabezado se apila (marca arriba, menú debajo), las rutas, los beneficios y el pie quedan en una columna, y la imagen decorativa de la franja final se oculta.
- **Desde 768 px:** el encabezado pasa a fila, la portada se divide en dos columnas, las rutas quedan en tres, y la franja final muestra la imagen.
- **Desde 1200 px:** la flota llega a cuatro columnas y los beneficios quedan en una sola fila de cuatro.

## 4. Mini reto 8: comparativa final (CSS propio vs Bootstrap)

| Dato | Bootstrap (v2.0-beta) |
|------|-----------------------|
| Líneas de `styles.css` | 90 en total, 68 con contenido |
| Páginas migradas | 4: `index`, `servicios`, `reservas` y `contacto` |
| Página de prueba | `pruebas/catalogo-bootstrap.html` |

**Ventajas que observé.** Casi todo el layout (encabezado, grilla de la flota, pie) salió de clases en el HTML. Mi CSS quedó reducido a lo que es propio de la marca: las variables de color sobre Bootstrap, la tarjeta de la flota con su sombra y su movimiento al pasar el mouse, la tabla de tarifas y el botón de enviar.

**Desventajas que observé.** Tuve que corregir estilos que Bootstrap aplica por defecto, como los de enlaces y listas. Las tarjetas de la flota todavía mezclan una clase mía (`.tarjeta`) con las del framework. Y perdí libertad sobre los puntos de quiebre.

**Conclusión.** Bootstrap me dejó armar las cuatro páginas con muy poco CSS propio, a cambio de aceptar sus puntos de quiebre y de revisar qué estilos globales me pisaban.

---

# Semana 7: Tailwind CSS (Rueda Nocturna)

## 5. Del botón de Bootstrap al botón de Tailwind

En los ejercicios anteriores armé los botones con Bootstrap, que trae componentes ya definidos. En Tailwind construí el botón juntando clases pequeñas directamente en el HTML: color de fondo, espacio interno, redondeado, tipografía y los estados `hover:` y `focus:`. Un ejemplo real es el botón principal verde esmeralda de `nocturna/index.html`.

La diferencia principal que identifiqué es que Bootstrap da componentes con una apariencia más definida, mientras que Tailwind permite construir el diseño con utilidades de bajo nivel. Eso me da más control sobre cada elemento, pero me exige elegir las clases y mantenerlas coherentes.

## 6. Qué construí

Dentro de la carpeta `nocturna/` hay tres páginas:

- `prototipo.html`: la primera versión, con Tailwind cargado por CDN (`@tailwindcss/browser@4`). Sirve para probar rápido, sin instalar nada.
- `index.html`: el sitio de Rueda Nocturna, con tres rutas guiadas por Medellín (Luces del Centro, Miradores de la Ciudad y Luna en Las Palmas). Cada una muestra su dificultad, distancia, duración, punto de encuentro y precio por persona.
- `componentes.html`: el kit de componentes v0.1, con botones (primario, secundario y outline), una tarjeta de ruta, tres alertas (información, confirmación y recomendación) y campos de formulario.

Las dos últimas ya no usan el CDN: cargan el CSS compilado desde `src/output.css`.

## 7. Verificación del CSS generado

`src/input.css` tiene una sola línea:

```css
@import "tailwindcss";
```

`src/output.css` se compiló con Tailwind CSS 4.1.10 a partir de las clases de `prototipo.html`, `index.html` y `componentes.html`. La compilación incluyó 156 candidatos de clase y el archivo pesa 27.260 bytes (unos 26,6 KB).

Para comprobar que Tailwind genera solo lo que se usa, busqué dos utilidades en el CSS generado: `bg-slate-950`, que sí uso, aparece; `bg-pink-500`, que no uso, no aparece. Tailwind lee mis archivos y deja únicamente las reglas que necesito.

Los comandos configurados en `package.json` son:

```bash
npm install
npm run watch:css   # compila y se queda observando cambios
npm run build:css   # compilación minificada para entregar
```

`node_modules/` está excluida con `.gitignore`. El `output.css` sí se conserva en el repositorio, porque GitHub Pages sirve archivos estáticos y no ejecuta npm.

## 8. Reglas del kit de componentes

- Espaciado con una escala coherente: `p-*`, `px-*`, `py-*`, `mb-*` y `gap-*`.
- Esquinas redondeadas consistentes: `rounded-lg`, `rounded-xl`, `rounded-2xl` y `rounded-3xl`, según el tamaño del elemento.
- El verde esmeralda identifica las acciones principales y los estados positivos; el azul informa y el ámbar advierte.
- La dificultad de las rutas está escrita (Fácil, Medio, Difícil) y no depende solo del color.
- Botones y campos tienen `hover:` y `focus:` visibles, y esos estados no mueven el contenido.
- Las tarjetas mantienen la misma estructura; cambian los datos, la imagen y el color de la dificultad.
- Cada campo del formulario tiene su etiqueta vinculada con `for` e `id` y un texto de ayuda enlazado con `aria-describedby`.
- La alerta de confirmación usa `role="status"` para que los lectores de pantalla la anuncien.

## 9. Responsive en Tailwind

En Tailwind los puntos de quiebre también son mobile-first: escribo primero la versión de celular y agrego prefijos para pantallas más grandes. En mis páginas usé `sm:`, `md:` y `xl:`.

- **Menos de 768 px:** la portada y las tarjetas de ruta quedan en una columna.
- **Desde 768 px (`md:`):** la portada se divide en dos columnas y las tarjetas de ruta pasan a dos columnas.
- **Desde 1280 px (`xl:`):** las tarjetas de ruta pasan a tres columnas.

## 10. Comparativa: Bootstrap vs Tailwind

| Dato | Bootstrap | Tailwind |
|------|-----------|----------|
| Cómo se carga | CDN, sin instalar nada | CLI con npm; `output.css` ya compilado |
| CSS que escribí a mano | 90 líneas (68 con contenido) | 1 línea (`@import`) |
| Dónde vive el diseño | En clases del HTML y algo de CSS propio | Casi todo en clases del HTML |
| Peso del CSS final | Lo trae el CDN | Unos 26,6 KB, solo con lo usado |

**Ventajas que observé.** Con Tailwind no escribí CSS propio y no tuve que pelear con estilos por defecto, como me pasó con enlaces y listas en Bootstrap. Puedo darle a cada pieza el aspecto exacto que quiero; por ejemplo, el borde de cada tarjeta cambia de color según la dificultad de la ruta. Y el CSS final incluye solo lo que uso.

**Desventajas que observé.** El HTML queda largo y repetitivo. El botón esmeralda, con las mismas clases, aparece 9 veces entre `index.html` y `componentes.html`, y si quiero cambiarlo tengo que tocar todas. Además Tailwind necesita un paso de compilación con npm: si agrego una clase nueva, debo volver a compilar, algo que con el CDN de Bootstrap no existe.

**Conclusión.** Bootstrap me sirvió para sacar páginas completas rápido con componentes ya hechos. Tailwind me sirvió más cuando quería un diseño propio, como el de Rueda Nocturna. A cambio de ese control, el HTML se vuelve más largo y dependo de la compilación. Si un diseño se repite mucho, lo lógico es armar componentes reutilizables para no copiar la misma cadena de clases.

## 11. Detalles por mejorar

- En `index.html` el Tándem todavía dice "(texto provisional)", y su precio de $20.000 también es provisional.
- El Tándem no aparece en la lista de modelos del formulario de `reservas.html`, que tiene 6 opciones en vez de 7.
- El botón "Ir a reservas" de Rueda Nocturna apunta a `../reservas.html`, un formulario que todavía no incluye las rutas nocturnas.
