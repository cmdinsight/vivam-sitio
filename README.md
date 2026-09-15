# vivam-sitio

Sitio público de **Vivam** — cuidados domiciliarios con respaldo médico en
Montevideo. Es lo que sirve **vivam.uy**.

Sitio estático: no hay build, no hay dependencias, no hay backend. Vercel
publica los archivos tal cual en cada push a `main`.

## Estructura

- `index.html` — la landing completa.
- `privacidad.html` — política de privacidad. Página aparte, HTML plano,
  sin el runtime de Claude Design. Enlazada desde el footer y desde la
  casilla de aceptación del formulario.
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
como cuidador) · servicios · planes · cuatro pasos · seis razones ·
dirección médica · cobertura por barrios con mapa · preguntas frecuentes
· formulario de contacto.

## Cosas que conviene saber antes de tocarlo

- **El formulario no tiene backend.** Arma un mensaje de WhatsApp y abre
  `web.whatsapp.com/send?phone=59898052210`. Si algún día se quiere
  recibir los contactos por mail o guardarlos en base, hay que agregarlo.
- **La sección de dirección médica no tiene foto.** La del export era una
  imagen de stock y se sacó. El hueco de 160px está esperando una foto
  real del Dr. González.
- **Las horas y el cupo de cada plan salen de `lib/planes.ts`** del
  sistema de gestión, que son valores por defecto **editables en vivo**
  por un admin desde /profesionales. Si alguien los cambió después de la
  carga inicial, el sitio queda desactualizado sin que nadie se entere.
  Contrastarlos contra la pantalla de configuración cada tanto.
- **Cinco respuestas de la sección de preguntas frecuentes** afirman
  políticas de negocio que siguen sin confirmar. Están listadas en un
  comentario dentro de `index.html`, arriba de `faqData`. Se consultó al
  sistema de gestión y ninguna de las cinco está documentada en el
  código: son decisiones operativas que hay que validar con el equipo,
  no con el software. En particular, el modelo de datos **no tiene
  ningún concepto de cuidador suplente** — el único backup que existe es
  para guardia médica (`TurnoGuardia.medicoBackupId`).
- **La promesa de "menos de 72 horas" se retiró del sitio.** Aparecía en
  el CTA final, en "Por qué Vivam" y en las preguntas frecuentes, y nadie
  la podía respaldar: el sistema no mide el tiempo entre primer contacto
  e inicio de servicio, y `Cliente.createdAt` no sirve como proxy. Ahora
  el sitio habla de "días, no semanas" y promete un plazo concreto recién
  cuando se conoce el caso. Si algún día se mide el plazo real, se puede
  volver a poner un número — pero con dato detrás.
- **No queda ninguna promesa de tiempo con número en el sitio.** Las dos
  que sobrevivían del diseño original —"Respuesta en menos de 1 hora" en
  el hero y "Respuesta en menos de 1h" en la barra de confianza— pasaron
  a "el mismo día". Si algún día se mide el tiempo de respuesta real, se
  puede volver a un número; hasta entonces, el sitio no promete nada que
  no se pueda sostener.
- **No hay testimonios.** La sección se retiró; el comentario en el HTML
  explica cómo restaurarla con reseñas reales.
- **Las fuentes vienen de Google Fonts** (Fraunces e Inter en la página,
  Source Serif 4 y Hanken Grotesk en header y footer). React y el runtime
  de Claude Design van empaquetados a propósito, para no depender de un
  CDN externo.
- **`.image-slots.state.json` da 404** en la consola. Es un sidecar de
  tiempo de diseño del runtime. Inofensivo.
- **El footer venía con un mailto equivocado.** Apuntaba a
  `contacto@integramedicalgroup.com` mientras el texto visible decía
  `.uy`. Corregido acá. Como el footer es un artboard compartido con los
  otros sitios del grupo, conviene revisar si el mismo error está en
  cmdtech.uy, coberturamedicad.com e integramedicalgroup.uy.
- El header y el footer usan el wordmark de Vivam. El diseño original
  traía el logo de Integra Medical Group, que no vino en el export.

## Pendientes legales anotados

**Transferencia internacional de datos de salud — a revisar con abogado.**
La política de privacidad declara que los datos del servicio (historia
clínica, reportes diarios, datos de cuidadores) se alojan en Neon, y más
adelante en Supabase, con servidores en Estados Unidos.

Uruguay no incluye a Estados Unidos en su lista de países con nivel de
protección adecuado, así que una transferencia internacional de datos
sensibles necesita una base legal específica. El artículo 23 de la Ley
18.331 prevé el consentimiento expreso del titular como una de las
excepciones, y es la que la política invoca hoy.

Estado real, verificado contra el repositorio del sistema de gestión:

- **No hay ningún análisis legal hecho.** Ni documento de evaluación de
  transferencia internacional, ni cláusulas contractuales tipo.
- **No hay evidencia de inscripción ante la URCDP.** Tratándose de datos
  de salud, es probable que corresponda.
- La base corre en **Neon Postgres provisionado por la integración de
  Vercel**, sin control directo sobre la región: por defecto queda en
  Estados Unidos.

Esto no se resuelve escribiendo código ni contenido. Antes de migrar a
Supabase o de escalar la recolección de datos de salud conviene una
consulta con abogado especializado en protección de datos, y evaluar con
esa persona si el consentimiento del artículo 23 alcanza o si hacen falta
cláusulas contractuales o una región de hosting en un país con nivel
adecuado. Al elegir región en Supabase, mirar qué opciones ofrece.

**Domicilio del responsable.** La política dice "Montevideo, Uruguay" sin
la dirección completa. La dirección fiscal se quitó a propósito: el
domicilio corresponde a un apartamento particular, y para ejercer los
derechos de la Ley 18.331 alcanza con el email y el teléfono, que sí
figuran. El dato completo está en el RUT y en el registro de la URCDP. Si
un abogado indica que el domicilio completo debe figurar en la página, se
restaura en el punto 1.

**Plazos y criterios vigentes.** Consultas que no avanzaron a servicio: 12
meses. Historia clínica: se archiva, no se destruye, con acceso permanente
del titular. Si eso cambia, hay que actualizar los puntos 8 y 9 de la
política, que están escritos uno en función del otro.

## Probarlo local

    python3 -m http.server 8000

Y abrir http://localhost:8000
