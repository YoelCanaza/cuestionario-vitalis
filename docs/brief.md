# Brief para Claude Design — Cuestionario de evaluación de Vitalis

## 1. Contexto

Necesito una **página web de una sola ruta** que aloje dos cuestionarios estandarizados de evaluación de usabilidad y experiencia de usuario: el **UEQ** (User Experience Questionnaire, 26 ítems) y el **SUS** (System Usability Scale, 10 ítems).

La página la van a responder **entre 3 y 5 evaluadores, de forma remota y sin supervisión**, inmediatamente después de haber probado una app móvil llamada **Vitalis** en su propio teléfono. Nadie estará ahí para explicarles nada: la página tiene que sostenerse sola.

**Sobre Vitalis (el producto evaluado, NO el producto que vas a diseñar):**
- App móvil de bienestar que reúne los hábitos, comidas, ejercicio y sueño del día y los sintetiza en un único puntaje entendible y accionable.
- Versión 1.0.0, prototipo funcional de alta fidelidad (React Native + Expo).
- Público objetivo: adultos jóvenes que quieren orden sin culpa.
- Se prueba desde Expo Go escaneando un QR.

**Destino de los datos:** al enviar, la página hace un POST a un Google Apps Script que escribe una fila en una hoja de cálculo. No hay backend propio, no hay base de datos, no hay login.

---

## 2. Objetivo del diseño

Que una persona sola, en su celular, sin nadie que la acompañe, **complete 36 ítems seguidos sin abandonar a la mitad y sin equivocarse de casilla**.

Ese es el problema real. 26 pares de adjetivos opuestos con 7 opciones cada uno es un formato denso y poco familiar; en pantalla de 380 px es donde este tipo de cuestionarios se rompe. Todo lo demás del diseño está al servicio de eso.

Criterios de éxito:
1. Cero scroll horizontal en cualquier ancho desde 320 px.
2. Se entiende sin instrucciones adicionales cuál adjetivo corresponde a cada extremo.
3. Nunca se pierde el avance si la persona cierra la pestaña.
4. La persona siempre sabe cuánto le falta.

---

## 3. Entregable

**Un único archivo HTML autocontenido**, con CSS y JS en línea, sin dependencias externas ni build step. Se publicará en GitHub Pages como archivo estático.

Este archivo pasará después a Claude Code para conectar el envío de datos, así que el marcado debe ser limpio y con nombres de campo predecibles.

---

## 4. Estructura: 6 pasos

Un flujo lineal por pasos, no un muro de 36 preguntas. Barra de progreso siempre visible.

```
1. Portada y consentimiento
2. Ficha del evaluador
3. Confirmación de tareas realizadas
4. UEQ — 26 pares
5. SUS — 10 ítems
6. Cierre
```

No se puede avanzar con ítems sin responder. Al intentarlo, la página debe hacer scroll hasta el primer ítem incompleto y marcarlo, no mostrar un alert genérico.

### Paso 1 — Portada y consentimiento

Debe comunicar, en este orden:
- Qué es esto: una evaluación de la app Vitalis, versión 1.0.0.
- Cuánto toma: unos 8 minutos.
- Que no hay respuestas correctas ni incorrectas, y que la crítica honesta es exactamente lo que se busca.
- Que su nombre no aparecerá en el trabajo: en el informe cada persona figura solo con un código. Es más creíble y más tranquilizador que un "es anónimo" genérico.
- Casilla obligatoria de consentimiento.

Un detalle de contenido importante: hay que pedirles explícitamente que **respondan pensando en la app Vitalis que acaban de probar, no en esta página**. Es una confusión frecuente cuando el cuestionario es digital.

### Paso 2 — Ficha del evaluador

Siete campos. Nada más.

| Campo | Tipo |
|---|---|
| Tu nombre o apodo | Texto libre corto |
| Dispositivo usado para probar Vitalis | Texto libre corto (marca y modelo) |
| Edad | Número |
| Ocupación o carrera | Texto libre corto |
| Tu perfil como evaluador | Selección única (ver opciones abajo) |
| ¿Con qué frecuencia usas apps de bienestar, hábitos o salud? | Selección única: Nunca / A veces / Con frecuencia |
| ¿Habías usado Vitalis antes de hoy? | Sí / No |

Opciones de "Tu perfil como evaluador":
- Pertenezco al público objetivo de la app
- Tengo formación o experiencia en diseño o desarrollo de software
- Ambos

La fecha y hora no se preguntan: se registran solas. El código de evaluador (E-01 a E-05) tampoco: lo asigna el servidor según el orden de llegada.

### Paso 3 — Confirmación de tareas

Se listan las cinco tareas que debían realizar, cada una con una casilla **"Sí, la realicé"**. No es un examen y no da puntaje: sirve para saber qué alcanzó a probar cada persona. Las casillas son opcionales — si alguien no hizo una tarea, debe poder seguir.

Texto introductorio sugerido: *"Antes de evaluar, confirma qué pudiste hacer. Si algo no funcionó o no lo encontraste, déjalo sin marcar — es información útil."*

Las cinco tareas:

1. **Crear tu cuenta.** Abrir Vitalis por primera vez, crear una cuenta, indicar qué te gustaría cuidar y dejarla lista para usar.
2. **Registrar tu día y entender el puntaje.** Marcar el avance de algunos hábitos y averiguar cómo se compone tu puntaje del día.
3. **Crear un hábito con recordatorio.** Crear un hábito nuevo, elegir en qué días lo harás, activar un recordatorio y confirmar que quedó guardado en tu lista.
4. **Registrar comidas o sueño.** Anotar lo que comiste hoy o cuánto dormiste anoche, y comprobar que tu día lo refleja.
5. **Ajustar metas y revisar tendencias.** Cambiar lo que quieres cuidar y luego revisar qué patrones dice la app haber notado sobre ti; abrir uno para ver el detalle.

### Paso 4 — UEQ

Va **antes** que el SUS. El UEQ pide impresión espontánea y funciona mejor mientras la sensación está fresca.

Encabezando el paso, este texto de instrucciones (es del cuestionario oficial, **no se reescribe**):

> Por favor, rellene el siguiente cuestionario con el fin de evaluar el producto. Se compone de pares de propiedades opuestas que el producto puede tener. Las gradaciones entre los opuestos están representadas por círculos. Usted puede expresar su conformidad con una propiedad marcando uno de estos círculos que mejor refleje su impresión.
>
> Por favor, decida espontáneamente. No piense demasiado su opinión y asegúrese que expresa su sensación inicial.
>
> Por favor, marque un círculo para cada par de propiedades, aunque piense que no son aplicables o que hay propiedades parecidas o prácticamente iguales.
>
> Su opinión personal cuenta. Recuerde: ¡no hay respuesta correcta o incorrecta!

Los 26 pares, en este orden exacto y con esta orientación exacta:

| # | Extremo izquierdo | Extremo derecho |
|---|---|---|
| 1 | desagradable | agradable |
| 2 | no entendible | entendible |
| 3 | creativo | sin imaginación |
| 4 | fácil de aprender | difícil de aprender |
| 5 | valioso | de poco valor |
| 6 | aburrido | emocionante |
| 7 | no interesante | interesante |
| 8 | impredecible | predecible |
| 9 | rápido | lento |
| 10 | original | convencional |
| 11 | obstructivo | impulsor de apoyo |
| 12 | bueno | malo |
| 13 | complicado | fácil |
| 14 | repeler | atraer |
| 15 | convencional | novedoso |
| 16 | incómodo | cómodo |
| 17 | seguro | inseguro |
| 18 | activante | adormecedor |
| 19 | cubre expectativas | no cubre expectativas |
| 20 | ineficiente | eficiente |
| 21 | claro | confuso |
| 22 | no pragmático | pragmático |
| 23 | ordenado | sobrecargado |
| 24 | atractivo | feo |
| 25 | simpático | antipático |
| 26 | conservador | innovador |

Cada ítem: 7 posiciones, valor guardado de 1 a 7 de izquierda a derecha. Se numeran visiblemente del 1 al 26.

### Paso 5 — SUS

Escala de 5 puntos. El extremo izquierdo se rotula **"Totalmente en desacuerdo"** y el derecho **"Totalmente de acuerdo"**. Los 10 enunciados, en este orden:

1. Creo que me gustaría usar Vitalis con frecuencia.
2. Considero que Vitalis es innecesariamente complejo.
3. Creo que Vitalis fue fácil de usar.
4. Creo que necesitaría el apoyo de una persona técnica para poder usar Vitalis.
5. Considero que las diversas funciones de Vitalis están bien integradas.
6. Creo que hay demasiadas inconsistencias en Vitalis.
7. Imagino que la mayoría de las personas aprenderían a usar Vitalis muy rápidamente.
8. Encuentro que Vitalis es muy engorroso de usar.
9. Me siento muy seguro usando Vitalis.
10. Necesité aprender muchas cosas antes de poder comenzar a usar Vitalis.

*(Nota: se sustituyó "este sistema" por "Vitalis". Es una adaptación aceptada del instrumento y se declarará en el informe.)*

### Paso 6 — Cierre

Agradecimiento breve y confirmación explícita de que la respuesta se envió. Tres estados posibles, los tres diseñados:

- **Enviando** — indicador de carga.
- **Enviada** — confirmación clara e inequívoca.
- **Error** — mensaje que diga qué hacer, no solo que algo falló.

Después de enviar, el botón se desactiva para que nadie mande dos veces la misma respuesta.

No se muestra ningún puntaje ni resultado al evaluador. No hay sección de resultados en esta página.

---

## 5. Reglas no negociables

Estas son las que rompen el instrumento si se tocan. Todo lo demás es libre.

1. **El orden y la polaridad de los 26 pares del UEQ son intocables.** Fíjate que el ítem 1 va *desagradable → agradable* (lo positivo a la derecha) pero el 3 va *creativo → sin imaginación* (lo positivo a la izquierda). Esa alternancia es deliberada, existe para que nadie marque una columna entera en automático, y la herramienta oficial de análisis la tiene codificada por posición. Reordenar o "acomodar" los pares para que lo positivo quede siempre del mismo lado produce resultados incorrectos sin ningún aviso.
2. **Lo mismo con la alternancia positivo/negativo del SUS.**
3. **La redacción de los ítems no se modifica.**
4. **Ninguna opción viene preseleccionada.** Sin valor por defecto, sin marca en el centro.
5. **No existe "no aplica" ni "prefiero no responder".** Todos los ítems del UEQ y del SUS son obligatorios.
6. **La escala es visualmente simétrica y neutra.** Los 7 controles tienen el mismo tamaño, la misma forma y el mismo color. Nada de degradados de rojo a verde, ni caritas, ni emojis, ni de un extremo más grande que el otro: eso sugiere qué respuesta es "buena" y sesga el resultado.
7. **El puntaje nunca se calcula ni se muestra en la página.**
8. **El texto de instrucciones del UEQ se reproduce como está.**

---

## 6. Responsive y accesibilidad

**El problema del UEQ en móvil:** los dos adjetivos no caben a los lados de 7 círculos en 380 px. La solución que funciona es una tarjeta por ítem, con el adjetivo izquierdo arriba a la izquierda, el derecho arriba a la derecha, y los 7 controles debajo repartidos en todo el ancho. Si tienes una idea mejor, adelante — el requisito es que en ningún momento haya que hacer scroll horizontal ni adivinar a qué extremo corresponde cada adjetivo.

- Área táctil mínima de 44×44 px por opción.
- Funciona desde 320 px de ancho.
- Radios reales (`<input type="radio">`) con `<label>` asociada, no divs con listeners. Debe ser navegable por teclado y utilizable con lector de pantalla.
- Contraste AA como mínimo.
- Respeta `prefers-reduced-motion`.
- Guardado automático del avance en `localStorage`, restaurado al volver a abrir.
- Indicador de progreso con número: "12 de 26", no solo una barra.

---

## 7. Dirección visual

Aquí tienes libertad. Solo tres orientaciones:

**Esta página no es Vitalis.** No la disfraces de la app: si se parece demasiado, el evaluador confunde el instrumento con el producto y termina calificando el formulario. Un parentesco lejano está bien —que se note que pertenecen al mismo proyecto— pero la página debe leerse como un instrumento, no como una pantalla más del producto.

**El tono correcto es sereno y honesto.** La persona está a punto de criticar el trabajo de alguien que conoce. El diseño debería darle permiso para hacerlo: nada de celebraciones, confeti, gamificación ni copy entusiasta. Vitalis se define por "orden sin culpa"; ese mismo espíritu aplica bien aquí.

**Un solo elemento memorable.** La densidad del contenido pide contención en todo lo demás. Elige dónde gastar la audacia —la escala de 7 puntos es la candidata natural, es lo que la persona va a mirar 26 veces— y mantén el resto disciplinado.

Sobre lo que conviene evitar por gastado: fondo crema con serif de alto contraste y acento terracota; negro casi puro con un único acento verde ácido; retícula tipo periódico con filetes finos y esquinas a cero. Son direcciones legítimas, pero aparecen por defecto en cualquier brief. Este merece una decisión propia.

---

## 8. Créditos

Al pie, discreto pero presente:

- UEQ: Laugwitz, Held & Schrepp (2008) — ueq-online.org
- SUS: John Brooke (1986). Plantilla en español de Interaction Design Foundation, licencia CC BY-NC-SA 4.0.
- Evaluación realizada para el curso [nombre del curso], [tu nombre], 2026.

---

## 9. Nombres de campo para el envío

El JSON que se enviará debe tener esta forma plana. Respeta los nombres al construir los inputs:

```
nombre                 "Sofía"
dispositivo            "Samsung A54"
edad                   27
ocupacion              "Diseñadora gráfica"
perfil_evaluador       "publico_objetivo" | "formacion_tecnica" | "ambos"
frecuencia_apps        "nunca" | "a_veces" | "con_frecuencia"
uso_previo             true | false
tarea_1 ... tarea_5    true | false
ueq_01 ... ueq_26      1–7   (valor crudo, sin transformar)
sus_01 ... sus_10      1–5   (valor crudo, sin transformar)
```

Crudo significa crudo: la página no invierte ítems, no resta, no promedia. Toda la transformación ocurre después, en el Excel oficial de análisis del UEQ y en la hoja del SUS.
