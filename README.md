# vivam-sitio

Sitio público de **Vivam** — cuidados domiciliarios con respaldo médico en
Montevideo. Es lo que sirve **vivam.uy**.

Sitio estático: no hay build, no hay dependencias, no hay backend. Vercel
publica los archivos tal cual en cada push a `main`.

## Estructura

- `index.html` — la landing completa.
- `assets/` — logo, mapa, el runtime de Claude Design y React 18 UMD.
- `favicon.ico`, `apple-touch-icon.png`.

## Cómo está hecho

Viene de un export de Claude Design. `index.html` **no es JSX ni un
componente React normal**: usa un custom element `<x-dc>` sobre React 18
UMD, con directivas `sc-if` y `sc-for`, interpolaciones `{{ ... }}`, y al
final del archivo una clase que maneja el estado — el acordeón de las
preguntas frecuentes, el carrusel, el desplegable del header y el
formulario.

`window.__resources`, en el `<head>`, mapea los ids originales del export
a las rutas reales. **Si movés archivos de lugar, hay que actualizar ese
mapa** o la página deja de cargar.

## Secciones

Hero · barra de confianza · dos puertas de entrada (familias / trabajar
como cuidador) · planes por etapa · modalidades · cuatro pasos · seis
razones · testimonios · dirección médica · cobertura por barrios con mapa
· preguntas frecuentes · formulario de contacto.

## Cosas que conviene saber antes de tocarlo

- **El formulario no tiene backend.** Arma un mensaje de WhatsApp y abre
  `web.whatsapp.com/send?phone=59898052210`. Si algún día se quiere
  recibir los contactos por mail o guardarlos en base, hay que agregarlo.
- **Los testimonios son ilustrativos.** Están marcados como tales en el
  propio HTML. Reemplazar por reseñas reales cuando haya.
- **La sección de dirección médica no tiene foto.** La del export era una
  imagen de stock y se sacó. El hueco de 160px está esperando una foto
  real del Dr. González.
- **Las fuentes vienen de Google Fonts** (Fraunces e Inter en la página,
  Source Serif 4 y Hanken Grotesk en header y footer). React y el runtime
  de Claude Design van empaquetados a propósito, para no depender de un
  CDN externo.
- **`.image-slots.state.json` da 404** en la consola. Es un sidecar de
  tiempo de diseño del runtime. Inofensivo.
- El header y el footer usan el wordmark de Vivam. El diseño original
  traía el logo de Integra Medical Group, que no vino en el export.

## Probarlo local

    python3 -m http.server 8000

Y abrir http://localhost:8000
