# Preguntas para el abogado — Abiertas — BASTIUM Cálculos

## Instrucciones de uso

**Para Mí como desarrollador JoseMsD (quien maneja este documento):**

1. Copia la sección que te interese y pégala en un Word, o envía el enlace directo a esta sección.
2. Envíalo al abogado o despacho correspondiente. Cada pregunta tiene un espacio en blanco
   ("**Respuesta del despacho:**") para que ellos escriban la respuesta directamente ahí.
3. Cuando llegue una respuesta que quede totalmente clara y sin conflicto con el código, muévela a
   [`Preguntas-Para-Abogado-Respondidas.md`](Preguntas-Para-Abogado-Respondidas.md) (dile a Claude Code
   "mueve la respuesta del Sprint N a Respondidas" y él se encarga de dejarlo consistente en ambos
   documentos y en `Pendientes.md`).
4. Este documento es un documento **vivo**: cada vez que un sprint nuevo tenga una decisión legal sin
   confirmar, una fuente que falte, o una respuesta que haya quedado en conflicto con lo ya construido, se
   le agrega una sección nueva siguiendo la plantilla del final.

**Para el abogado / despacho que responde:**

Este documento acompaña el desarrollo de BASTIUM, un software de liquidación de procesos judiciales
(cálculo de capital, intereses, indexación, prescripción, etc.) para uso interno de un despacho. Cada
sección de abajo es una pregunta que **sigue abierta** — o porque nunca se respondió, o porque la
respuesta que llegó no se pudo aplicar tal cual (falta un dato, o entra en conflicto con algo que el
software ya tenía construido) y necesita una aclaración adicional. No hace falta leer código ni tener
conocimientos técnicos.

Las preguntas ya resueltas (sin necesidad de volver a preguntarlas) están archivadas aparte, en
[`Preguntas-Para-Abogado-Respondidas.md`](Preguntas-Para-Abogado-Respondidas.md).

---

## Índice

- [Sprint 8 (seguimiento) — Fuente del IPC mensual del DANE](#sprint-8-seguimiento--fuente-del-ipc-mensual-del-dane)
- [Sprint 13 — Motor de reglas / parámetros legales](#sprint-13--motor-de-reglas--parámetros-legales)
- [Sprint 18 — Costas judiciales (tabla de rangos)](#sprint-18--costas-judiciales-tabla-de-rangos)
- [Sprint 18 (seguimiento) — ¿La tabla simple reemplaza el Acuerdo PSAA16-10554?](#sprint-18-seguimiento--la-tabla-simple-de-rangos-reemplaza-el-acuerdo-psaa16-10554)
- [Sprint 33 — Tipo de acción procesal para las alertas de prescripción del Dashboard](#sprint-33--tipo-de-acción-procesal-para-las-alertas-de-prescripción-del-dashboard)
- [Plantilla para sprints futuros](#plantilla-para-sprints-futuros)

---

## Sprint 8 (seguimiento) — Fuente del IPC mensual del DANE

**Contexto:** El despacho ya confirmó (ver Sprint 8 en el archivo de Respondidas) que la interpolación
entre cierres de año es jurídicamente inválida y que se necesita el índice IPC **mensual** real del DANE,
con interpolación lineal de días entre meses. El desarrollo ya construyó y probó la función que hace esa
interpolación (`get_ipc_interpolado_mensual_for_date` en `app/engine/indexation/historical_index.py`),
pero le falta el insumo: la tabla real de índices mensuales del DANE. La fuente que ya tenía el software
(transcrita del PDF de requisitos) solo trae variación **anual**, no mensual. Una búsqueda de fuentes
públicas en internet no encontró una serie mensual completa y verificable en un formato transcribible con
confianza (solo variaciones porcentuales desde 2011, no el índice completo desde 1967).

**Pregunta:** ¿El despacho tiene acceso a la serie histórica mensual de IPC del DANE (índice, no solo
variación porcentual), por ejemplo a través de un servicio como Legis, Actualícese Premium, o la
suscripción de datos que use el despacho? Si es así, ¿pueden aportar esa tabla (Excel, CSV, o el enlace de
descarga)?

**Qué necesito exactamente:** La tabla completa de índice IPC mensual (no variación porcentual) que cubra
desde el año más antiguo que el despacho necesite liquidar hasta el mes más reciente certificado por el
DANE, idealmente con la base y el período de referencia indicados (ej. "base diciembre 2018 = 100"). Si no
se consigue la serie completa desde 1967, sirve también acotar desde qué año en adelante hace falta —
misma lógica que se usó con la UVT en el Sprint 14.

**Respuesta del despacho:**


**Fecha:**

---

## Sprint 13 — Motor de reglas / parámetros legales

**Contexto:** Este sprint fue una decisión de arquitectura (no una pregunta legal): se decidió que las
tasas, topes y porcentajes legales (usura, cuota litis, SMLMV, IPC, etc.) vivan en una tabla editable desde
la pantalla de "Parámetros" del software, para que puedan actualizarse sin necesitar un programador.

Nota de JoseMsD (2026-08-01): en una ronda anterior de respuestas, este bloque quedó duplicado por error —
lo que había ahí era una copia exacta de la respuesta del Sprint 11 (imputación de pagos, piso de
sanciones, concurrencia intereses/actualización), que ya está archivada en su sección correcta en
`Preguntas-Para-Abogado-Respondidas.md`. Junto con esa copia venía además una tabla histórica de UVT
2006-2026, que en realidad responde al Sprint 5 y ya se movió a esa sección. **Sigue sin existir una
respuesta real a la pregunta de este Sprint 13.**

**Pregunta:** Si en el futuro alguien del despacho va a ser quien actualice los parámetros legales
(tasas, topes, plazos) directamente desde la pantalla de "⚙ Parámetros" del software, ¿hace falta preparar
una guía de uso corta para esa persona?

**Qué necesito exactamente:** Sí/no, y si es sí, quién sería esa persona (para adaptar el lenguaje de la
guía a su nivel técnico).

**Respuesta del despacho:**


**Fecha:**

---

## Sprint 18 — Costas judiciales (tabla de rangos)

**Contexto:** El PDF de BASTIUM menciona que las costas judiciales (agencias en derecho) se fijan según
rangos de porcentaje del Consejo Superior de la Judicatura (cita el Acuerdo PCSJA20-11556 como ejemplo,
"3% al 7% de las pretensiones reconocidas"), pero **no transcribe la tabla completa de rangos**. Este
acuerdo tampoco se consiguió buscando en fuentes públicas.

**Pregunta:** ¿Pueden aportar el texto completo (o al menos la tabla de rangos de cuantía y porcentaje) del
Acuerdo del Consejo Superior de la Judicatura que esté vigente hoy para costas judiciales/agencias en
derecho?

**Qué necesito exactamente:** El documento o la tabla completa (rango de cuantía desde/hasta → porcentaje
aplicable), o el nombre/número exacto del acuerdo vigente si no es el PCSJA20-11556.

**Respuesta del despacho:**
Existe una tabla de rangos de cuantía estricta que limita lo que el juez puede fijar.
Instrucción de Desarrollo:

Implementar tabla de validación cruzada basada en las pretensiones del proceso:
Mínima Cuantía (Hasta 40 SMMLV): Rango permitido 0% al 10%.
Menor Cuantía (>40 hasta 150 SMMLV): Rango permitido 3% al 7%.
Mayor Cuantía (>150 SMMLV): Rango permitido 1% al 5%.
El sistema debe restringir el input del usuario: si el proceso es de Mayor Cuantía, el usuario no podrá ingresar un 8% de agencias en derecho (el sistema debe lanzar un error de validación).

**Fecha:** _(pendiente — no se especificó al copiar la respuesta; confirmar con el despacho)_

**Por qué sigue abierta (verificado leyendo el código, 2026-08-01):** esta tabla simple de 3 rangos **no
coincide numéricamente** con la tabla granular que el desarrollo ya había construido en el cierre original
del Sprint 18 (18 tipos de proceso × instancia, cada uno con su propio rango, transcrita directamente del
Acuerdo PSAA16-10554 del 5 de agosto de 2016 del Consejo Superior de la Judicatura, verificado contra la
fuente oficial en ramajudicial.gov.co — ej. la tabla granular da 5%-15% para mínima cuantía en varios tipos
de proceso, no 0%-10%). Ver la pregunta de seguimiento abajo, que es la que de verdad necesita respuesta
para poder cerrar este punto.

---

## Sprint 18 (seguimiento) — ¿La tabla simple de rangos reemplaza el Acuerdo PSAA16-10554?

**Contexto:** La respuesta del despacho arriba trajo una tabla simple de 3 rangos por cuantía (Mínima
0%-10%, Menor 3%-7%, Mayor 1%-5%). El desarrollo ya tenía construida, desde el cierre original del Sprint
18, una tabla mucho más granular (18 tipos de proceso × instancia, cada uno con su propio rango) transcrita
directamente del Acuerdo PSAA16-10554 — esa tabla granular NO coincide numéricamente con la tabla simple.

**Qué se hizo mientras tanto (2026-08-01):** para no dejar sin implementar la instrucción explícita del
despacho ("el sistema debe restringir el input del usuario... lanzar un error de validación"), se usó la
tabla simple **únicamente para validar/rechazar el porcentaje manual** (`costas_pct_manual`) — la tabla
granular sigue intacta y sin tocar para el cálculo automático por tipo de proceso
(`costas_tipo_proceso`/`costas_instancia`). Es una decisión técnica tomada con criterio propio, no
confirmada todavía por el despacho.

**Pregunta:** ¿La tabla simple de 3 rangos que enviaron es (a) una síntesis/resumen aceptable que reemplaza
por completo la tabla granular del Acuerdo PSAA16-10554 (en cuyo caso habría que eliminar la tabla granular
y quedarnos solo con los 3 rangos), o (b) un tope general que solo aplica cuando se usa el porcentaje
manual, y la tabla granular sigue siendo la fuente correcta para el cálculo automático por tipo de proceso?

**Qué necesito exactamente:** Una de las dos opciones (a/b), o la aclaración que corresponda si ninguna es
exacta.

**Respuesta del despacho:**


**Fecha:**

---

## Sprint 33 — Tipo de acción procesal para las alertas de prescripción del Dashboard

**Contexto:** El Dashboard nuevo de BASTIUM (pantalla de inicio) avisa cuando una obligación está por
prescribir, para que no se pase la fecha límite sin darse cuenta. Para calcular esa fecha límite, el
software necesita saber qué "tipo de acción" judicial aplica (por ejemplo, ejecutiva, ordinaria,
cambiaria), porque cada tipo tiene un plazo de prescripción distinto. Hoy el software **no guarda ese dato
en ningún expediente ni obligación** — no existe un campo para eso — así que, por ahora, se está usando
"acción ejecutiva" para calcular la alerta en **todas** las áreas del derecho por igual (Civil/Familia,
Comercial, Sancionatorio, Honorarios, Laboral, Tributario). Esto es una simplificación técnica temporal,
no una regla legal confirmada por el despacho.

**Pregunta:** ¿La acción ejecutiva es el tipo correcto para calcular la prescripción en las 6 áreas que
maneja el software, o cada área debería usar un tipo de acción distinto (por ejemplo, ordinaria para
algunos casos de Familia, cambiaria para pagarés/letras en Comercial, etc.), con plazos diferentes?

**Qué necesito exactamente:** Si la respuesta es "cada área es distinta", una tabla simple de
Área del derecho → Tipo de acción → Plazo de prescripción (en años o meses), con la norma que lo respalda
si es posible. Si "ejecutiva para todo" es una aproximación razonable mientras tanto, basta la confirmación
de que sirve como estimado provisional (sabiendo que puede no ser exacto para casos puntuales).

**Respuesta del despacho:**


**Fecha:**

---

## Plantilla para sprints futuros

Copiar este bloque y completarlo cuando un sprint nuevo tenga una decisión legal sin confirmar, una fuente
que falte, o una respuesta que haya quedado en conflicto con el código ya construido:

## CAMBIOS QUE DEBEN HACERSE POR ERRORES DETECTADOS


IPC no aparecen los datos de cada año, ver documento "REGLAS DE CÀLCULO BASTIUM" Pagina 62.

El IPC es muy importante porque es el que define la actualización de la cuota anualmente en casos donde las actas de conciliación dicen que no son por aumento salarial sino por IPC. Eso afecta el càlculo de los intereses, indexaciones, etc. No solo en derecho de familia sino en varios cálculos de áreas jurídicas diferentes, es esencial que el sistema pueda saber què IPC aplica por cada año.

El sistema debe saber què ley aplica )la formula y cifras de cada ley) dependiendo del año del caso. Por ejemplo, si alguien se pensionò en 1997 le aplican las reglas de la Ley 100 de 1993, si se pensionò en 2024 le aplican las reglas de la Ley 797 de 2003; y si se pensionò en mayo de 2026 le aplican las reglas de la ley 2381 de 2024 que entró en vigencia recientemente.

Debe saberse cuando entró en vigencia cada ley para saber cuándo se aplica una ley y sus reglas y cifras. Sobre todo en materia de Derecho Laboral y Seguridad Social donde se ha modificado el Código Sustantivo del Trabajo y el Código de Procedimiento del Trabajo.

_________________
En la parte de Agregar una obligación, la casilla de "aplica indexación IPC (corrección monetaria) No se ve la casilla para marcarla porque se confunde con el resto del fondo color crema.

La casilla de aplicar indexación de IPC debe aparecer cuando ya estè hecha la liquidación, pues automaticamente se debe indexar. Sòlo cuando estè lista la liquidación por pura pedagogía se le puede presentar una tabla al juez donde no se actualiza con indexación. Pero deberìa agregarse como opción cuando ya estè proyectada la liquidación para elegir si ponerle o quitarle indexación.

La segunda opción de interés sobre capital indexado puede llegar a estar prohibida por el ordenamiento, debe revisarse. Pero en caso que sea legal, también deberìa aparecer al final cuando ya estè lista la liquidación para saber si se agrega con ese valor o no.

Estos son detalles que pueden considerarse, no es grave.

La ventana de agregar obligación es muy grande y no tiene opción para disminuir tamaño, es algo a mejorar porque no aparece la opción de "guardar" a simple vista.
________________
En la opción de agregar obligación se puede lograr que al ampliar la ventana no queden barras largas de los campos, sino que sea armonico y la parte de tasa e intereses pase a la derecha de datos básicos, que sea fácil de usar y muy intuitivo, para que funcione en cualquier pantalla sin errores de diseño que afecten la experiencia.
________________
Para gastos de vestuario no se repite mes a mes, sino en fechas únicas en el año. Por ejemplo, debe pagar las mudas de ropa en junio, diciembre y el cumpleaños del niño, entonces solo se ponen esas fechas en el calendario y ya el 

___________
Dado que no solo los niños reciben alimentos, debe al principio decirse si es un niño, si tiene discapacidad, si son alimentos para el cónyuge o para los padres, o para otras personas como donantes.
En principio todas las obligaciones son perdurables excepto la de los niños, ya que solo se paga hasta que cumplan 25 años o hasta que superen su discapacidad, si su discapacidad es permanente, el alimentante debe pagar eternamente hasta que muera. Lo mismo con los demás alimentos, se pagan por siempre hasta la muerte del beneficiario o del deudor, o hasta que el alimentado supere su condición de vulnerabilidad. Deben haber casillas dependiendo del caso, que surjan dependiendo de cada opción como arboles de decisión.
______________________
Al inicio de los casos de Civil/Familia se debe saber la edad del demandante, todos los datos necesarios solo para liquidar. Por ejemplo, ingresar la fecha exacta de nacimiento para que el sistema calcule automaticamente la edad del demandante )o del beneficiario, que es muy importante). Debe preguntarse si hay beneficiario o no, pues el beneficiario es el legitimo acreedor de las deudar por las cuales està luchando el demandado. Si hay beneficiario se debe saber el nombre y fecha de nacimiento para calcular la edad automaticamente. Eso es relevante cuando son cuotas alimentarias para niños sin discapacidad, pues la obligación para a los 18 años si el niño no està estudiando una carrera profesional, técnica o tecnológica. Si està estudiando, la obligación alimentaria continúa solo hasta los 25 años. En cambio, se puede deber alimentos al cónyuge y su fecha de caducidad se determina en la fecha donde supere su condición de vulnerabilidad, por ejemplo si consigue trabajo, si gana la loterìa, etc. Y con los padres es diferente porque se debe hasta la muerte de ellos o la muerte del alimentante. En casos puntuales se pueden deber alimentos a otros como abuelos, donantes, etc.

________________
En la parte del demandante debe aparecer si es el mismo beneficiario acreedor. Si no es el beneficiario debe preguntar nombre, fecha de nacimiento del beneficiario

_____________
En el apartado de agregar bono, PARA TODAS LAS CATEGORÌAS DE TODAS LAS AREAS DEL DERECHO DEBE DETECTARSE QUE ES UNA OBLIGACIÒN RECURRENTE Y SACAR EL LISTADO DE OBLIGACIONES GENERADAS MENSUALMENTE A PARTIR DE ESA FECHA. Con ello se podrá seleccionar por rangos o manualmente si se pagaron esas obligaciones enteramente o si se hicieron abonos y contar el pago desde la fecha de pago hacia delante. Por ejemplo, si el acta dice que se deben 150.000 los 5 de cada mes desde el 1 de abril de 2022, debe desplegarse una lista grande de cada obligación de cuota mensual con sus obligaciones derivadas (subobligaciones) de intereses simples. Y si por ejemplo el deudor pagò desde mayo de 2023 hasta noviembre de 2023, se entiende que todas las obligaciones de ese rango ya fueron pagadas. O si por ejemplo sòlo hizo un abono el 1 de abril de 2024 de 500.000 pesos, se entiende que paga la cuota del mes que hizo el abono y las obligaciones inmediatamente anteriores. Es decir, que con ese abono pagò capital de cuota de abril, de marzo con sus intereses, y de febrero pero con solo una parte de sus intereses. El resto de los intereses los sigue debiendo pero como ya pagò el capital de ese mes, entonces no genera màs intereses. O puede pasar que pagò parcialmente capital de una cuota atrasada y no le alcanzó para màs con el abono, entonces se entiende que los intereses que se habían generado hasta esa fecha de pago siguen pero ahora se cuentan los intereses sobre el nuevo capital que es el insoluto que no alcanzó a pagar con el abono que hizo.  
_____________
Agregar boton de editar y eliminar obligaciones y abonos.
```
## Sprint N — [Nombre del sprint]

**Contexto:** [Explicación en lenguaje llano de qué decisión se tomó o qué falta, y por qué importa.]

**Pregunta:** [Pregunta puntual, lo más cerrada posible — idealmente respondible con un sí/no o un dato
concreto.]

**Qué necesito exactamente:** [Formato exacto de la respuesta esperada: confirmación, corrección, tabla,
documento, ejemplo numérico, etc.]

**Respuesta del despacho:**


**Fecha:**
```
