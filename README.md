# Generador de Guías de Tramitación (v3.1)

Herramienta web de un solo archivo que convierte la **definición de un procedimiento administrativo exportada de Model@** (XML) en una **guía de tramitación en formato ODT** (LibreOffice / OpenOffice / Word), lista para revisar y entregar.

Todo ocurre en el navegador: no hay servidor, no hay instalación y el XML no sale del equipo.

```text
XML de Model@  ──►  Generador de Guías  ──►  Guia_Tramitacion_<procedimiento>_v<versión>.odt
(procedimiento)     (index.html en el        (portada, ficha, fases y módulos
                     navegador)               de tramitación editables)
```

---

## ¿Para qué sirve?

Escribir a mano la guía de tramitación de cada procedimiento es lento y repetitivo: casi todos tienen fases parecidas (presentación, análisis, subsanación, resolución, archivo…). Esta herramienta:

1. **Lee el XML de Model@** y extrae el procedimiento, sus metafases, fases, perfiles de usuario y transiciones.
2. **Detecta automáticamente qué módulos de tramitación** aplican según los nombres de las fases.
3. **Rellena** metadatos y descripción con los datos del XML.
4. Deja **editar todos los textos** (descripción, perfil, tareas, cómo finalizar, documentos) de cada módulo.
5. **Genera el ODT** con portada, control de versiones, ficha del procedimiento y una tabla por fase.

---

## Uso

1. Descarga [`index.html`](index.html) y ábrelo en un navegador actualizado (Chrome, Edge o Firefox).
2. **Importar XML:** arrastra el XML de Model@ a la zona de carga (o haz clic para elegirlo).
   El panel lateral muestra el nombre del procedimiento y el número de metafases y fases.
3. Revisa las pestañas:
   - **Metadatos**
     - *Datos del documento:* nombre del procedimiento, versión, fecha, autor/empresa y organismo (portada y control de versiones).
     - *Fases detectadas en el XML:* tabla de metafases y fases con su ID.
     - *Descripción del procedimiento:* descripción corta, descripción ampliada (objeto y alcance) y unidad responsable.
     - *Normativa de aplicación:* una norma por línea.
     - *Datos de solicitud y tramitación:* quién puede solicitar, plazo, cómo presentar, notificación de la resolución, recursos y protección de datos.
   - **Módulos / textos:** edita el texto base de cada módulo detectado. *↺ Restaurar texto base* deshace los cambios.
   - **Vista previa:** estructura del ODT que se va a generar.
4. En el panel **Módulos** activa o desactiva los módulos que quieras incluir.
5. Pulsa **Descargar ODT**. Se descarga `Guia_Tramitacion_<nombre>_v<versión>.odt`.

---

## Módulos de tramitación

Se marcan solos cuando alguna fase del XML contiene el texto indicado (sin distinguir mayúsculas):

| Código | Módulo | Se activa si una fase contiene… |
|---|---|---|
| `PRES` | Presentación presencial | “presencial” |
| `TELE` | Presentación telemática | “telem” |
| `ANAL` | Análisis de la documentación | “anal” |
| `REQSUB` | Requerimiento de subsanación | “requerimiento” |
| `SUB` | Subsanación del interesado | “subsana” (y no “requerimiento”) |
| `RECEP` | Recepción y comunicación de plazos | “recep” o “plazo” |
| `DESIST` | Desistimiento / renuncia | “desistimiento” |
| `RES` | Resolución | “resoluci” (y no “archivo” ni “desistimiento”) |
| `ARCH` | Resolución de archivo | “archivo” |
| `INCORP` | Incorporación de documentación | “incorpora” |
| `CIERRE` | Cierre del expediente | “cierre” o “fin del” |

Cada módulo trae un texto base con **Descripción**, **Perfil**, **Tramitación y tareas**, **Finalizar** y **Documentos**, que se vuelca tal cual en el ODT.

---

## Qué lee del XML de Model@

| Elemento XML | Uso |
|---|---|
| `DEF_PROCEDIMIENTO` (`id`, `nombre`, `descripcion`, `descrip_ampliada`, `sistema`, `desc_familia`, `version`) | Datos generales y descripciones |
| `PERFIL_USUARIO` (`id`, `nombre`, `descripcion`) | Perfiles que intervienen |
| `METAFASE` › `FASE` (`id`, `nombre`, `descripcion`, `texto_aux`) | Estructura de fases y detección de módulos |
| `TRANSICION` (`etiqueta`, `FASE_INI`, `FASES_FIN`, `PERFIL_TRANSICION`) | Flujo entre fases |

---

## Cómo se genera el ODT

No usa librerías externas. El propio `index.html`:

1. Construye el `content.xml` en formato OpenDocument (tablas *Portada*, *Ficha*, *Agrupación*, control de versiones y una tabla por fase).
2. Comprueba que el XML generado es válido con `DOMParser`.
3. Empaqueta `mimetype`, `META-INF/manifest.xml`, `content.xml`, `styles.xml` y `meta.xml` en un ZIP (con su propio CRC32 y compresión *deflate*) con la estructura de un `.odt`.
4. Ofrece la descarga.

---

## Estructura del repositorio

```text
.
└── index.html   ← Toda la aplicación: estilos, interfaz y lógica
```

## Limitaciones conocidas

- `index.html` es un **fragmento HTML**: empieza por `<style>` y no tiene `<html>`, `<head>` ni `<meta charset>`. Los navegadores lo abren igual, pero no declara la codificación.
- Los iconos usan clases de *Tabler Icons* (`ti ti-…`) sin cargar su hoja de estilos, así que no se ven.
- La detección de módulos va por palabras clave en los nombres de las fases: revisa siempre la selección antes de generar.
- Solo admite XML exportados de Model@ con la estructura descrita arriba.

---

**Autor:** Francisco Condado.
