# Cuestionario Vitalis — SUS + UEQ

Instrumento de evaluación de usabilidad y experiencia de uso de la app **Vitalis 1.0.0**:
6 pasos, 26 pares del UEQ y 10 ítems del SUS, para responderse en el celular sin supervisión.

Implementación de `Cuestionario Vitalis.dc.html`, importado del proyecto de Claude Design
`ee0a8789-7075-49e9-974d-768e7b8ae6fb`, según `docs/brief.md`.

## Archivos

| Archivo | Qué es |
| --- | --- |
| `index.html` | **El entregable.** Un solo archivo con el CSS y el JS en línea; es lo único que se publica. |
| `docs/brief.md` | El encargo original. |
| `Cuestionario Vitalis.dc.html`, `support.js`, `_ds/` | El archivo de diseño tal como está en Claude Design, con el runtime y el sistema Broadsheet, para poder volver a sincronizar. No hacen falta para publicar. |

La única dependencia externa de `index.html` es la fuente Source Serif 4 de Google Fonts;
sin conexión cae a la serif del sistema y todo lo demás sigue funcionando.

## Publicar

Es un archivo estático: subir `index.html` a GitHub Pages (o a cualquier hosting) y compartir el enlace.

Para probarlo en local basta con abrirlo, aunque conviene servirlo, porque `localStorage`
—y por lo tanto el guardado del avance— no está disponible en `file://`:

```bash
python3 -m http.server 8000   # http://localhost:8000/index.html
```

Para revisar un paso sin llenar los anteriores: `?paso=4` (de 2 a 6).

## Configurar el envío

Al inicio del `<script>` de `index.html`:

```js
const CONFIG = {
  endpoint: 'https://script.google.com/macros/s/REEMPLAZAR/exec',
  restoreProgress: true,
  startStep: 1
};
```

Mientras la URL contenga `REEMPLAZAR`, el botón **simula** el envío: sirve para revisar los estados
«enviando / enviada / error» sin backend. Al reemplazarla por la URL del Web App de Apps Script el
envío es real: un `POST` con `Content-Type: text/plain` —así se evita el *preflight* de CORS, que
Apps Script no responde— con el JSON de una respuesta en el cuerpo.

Script mínimo del lado de la hoja de cálculo:

```js
function doPost(e) {
  const r = JSON.parse(e.postData.contents);
  const hoja = SpreadsheetApp.getActiveSheet();
  if (hoja.getLastRow() === 0) hoja.appendRow(Object.keys(r));
  hoja.appendRow(Object.keys(r).map(k => r[k]));
  return ContentService.createTextOutput('ok');
}
```

Publicarlo como *Web App*, ejecutándose como tú y con acceso para «cualquier persona».

### Campos que llegan

`fecha_hora` (ISO), `nombre`, `dispositivo`, `edad` (número),
`ocupacion`, `perfil_evaluador` (`publico_objetivo`/`formacion_tecnica`/`ambos`),
`frecuencia_apps` (`nunca`/`a_veces`/`con_frecuencia`), `uso_previo` (booleano),
`tarea_1`…`tarea_5` (booleanos), `ueq_01`…`ueq_26` (1–7) y `sus_01`…`sus_10` (1–5).

Valores crudos: la página no invierte, no resta y no promedia nada. Los 26 pares del UEQ están en el
orden y la polaridad oficiales, así que las columnas `ueq_01`…`ueq_26` se pegan directamente en el
*UEQ Data Analysis Tool*. El SUS se puntúa aparte (impares −1, pares 5−valor, suma × 2.5).

## Cómo se comporta

- El avance se guarda en `localStorage` (`vitalis_eval_v1`) y se borra al enviar.
- Pasos 1, 2, 4 y 5 obligatorios; el paso 3 (tareas realizadas) es opcional a propósito: dejar una
  tarea sin marcar es un dato.
- Al faltar respuestas no hay alerta: se resalta el primer pendiente y la página se desplaza hasta él.
- Ninguna opción viene preseleccionada y nunca se muestra puntaje.
- Radios y checkboxes reales con `<label>`: navegable por teclado y con lector de pantalla.
- Sin scroll horizontal de 320 px en adelante; cada opción de la escala mide 44×44 px o más
  (por debajo de 355 px la fila de círculos sangra hasta el borde para conservarlos).
- Respeta `prefers-reduced-motion` (sin desplazamientos suaves).

## Diferencias respecto al diseño y al brief

- **Se retoma el paso guardado.** En el archivo de diseño, el *prop* `startStep` (por defecto 1)
  ganaba siempre sobre el paso guardado, contra lo que promete el texto de la propia página.
  Aquí `CONFIG.startStep` (o `?paso=N`) sólo manda si es mayor que 1.
- **Concordancia en los avisos:** «Falta 1 campo» / «Faltan 3 campos», en vez de «Faltan 1 campo».
- **Sin `sistema_operativo`.** El archivo de diseño había agregado ese campo (Android/iOS) a la ficha;
  se quitó para dejar los siete campos del brief y las claves de su §9.

## Pendiente

- Reemplazar `endpoint` por la URL real del Apps Script.
