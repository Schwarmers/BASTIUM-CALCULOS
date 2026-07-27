# Preguntas para el abogado / despacho — BASTIUM Cálculos

## Instrucciones de uso

**Para Mi como desarrollador JoseMsD (quien maneja este documento):**

1. Copia todo este documento (o solo la sección del sprint que te interese) y pégalo en un Word.
2. Envíalo al abogado o despacho correspondiente. Cada pregunta tiene un espacio en blanco
   ("**Respuesta del despacho:**") para que ellos escriban la respuesta directamente ahí, sin tener que
   reformatear nada.
3. Cuando te devuelvan las respuestas, cada sección está identificada con el número de Sprint al que
   corresponde en `Pendientes.md` — busca ese mismo número de sprint en `Pendientes.md` (sección
   "**Estado:**" de cada sprint) y pega la respuesta ahí, o dile a Claude Code "actualiza el Sprint N con
   esta respuesta del abogado: [pega el texto]" y él se encarga de dejarlo consistente en ambos documentos.
4. Este documento es un documento **vivo**: cada vez que un sprint nuevo tenga una decisión legal sin
   confirmar o una fuente que falte, se le agrega una sección nueva siguiendo la plantilla del final. No
   hace falta reescribir las secciones ya respondidas.

**Para el abogado / despacho que responde:**

Este documento acompaña el desarrollo de BASTIUM, un software de liquidación de procesos judiciales
(cálculo de capital, intereses, indexación, prescripción, etc.) para uso interno de un despacho. Cada
sección de abajo describe, en lenguaje llano, una decisión de cálculo que el desarrollo tomó **sin tener
una fuente jurídica 100% confirmada**, o una pregunta puntual sobre cuál de varias reglas posibles debe
aplicar el software. No hace falta leer código ni tener conocimientos técnicos — cada pregunta está escrita
para responderse con una confirmación, una corrección, o el dato/documento que haga falta aportar.

---

## Índice

- [Sprint 2 — Área Comercial](#sprint-2--área-comercial)
- [Sprint 3 — Área Laboral](#sprint-3--área-laboral)
- [Sprint 4 — Sancionatorio y Honorarios](#sprint-4--sancionatorio-y-honorarios)
- [Sprint 5 — Datos históricos (UVT)](#sprint-5--datos-históricos-uvt)
- [Sprint 6 — Calendario de días hábiles](#sprint-6--calendario-de-días-hábiles)
- [Sprint 7 — Prescripción y caducidad](#sprint-7--prescripción-y-caducidad)
- [Sprint 8 — Indexación IPC Civil/Familia](#sprint-8--indexación-ipc-civilfamilia)
- [Sprint 11 — Derecho Tributario (DIAN)](#sprint-11--derecho-tributario-dian)
- [Sprint 12 — TRM y moneda extranjera](#sprint-12--trm-y-moneda-extranjera)
- [Sprint 13 — Motor de reglas / parámetros legales](#sprint-13--motor-de-reglas--parámetros-legales)
- [Sprint 15 — Tributario: sanciones e imputación](#sprint-15--tributario-sanciones-e-imputación)
- [Sprint 16 — Seguridad social e incapacidades laborales](#sprint-16--seguridad-social-e-incapacidades-laborales)
- [Sprint 17 — Módulo pensional (IBL, tasa de reemplazo, semanas)](#sprint-17--módulo-pensional-ibl-tasa-de-reemplazo-semanas)
- [Sprint 18 — Costas judiciales (tabla de rangos)](#sprint-18--costas-judiciales-tabla-de-rangos)
- [Sprint 30 — Posible error de un día](#sprint-30--posible-error-de-un-día)
- [Plantilla para sprints futuros](#plantilla-para-sprints-futuros)

---

## Sprint 2 — Área Comercial

**Contexto:** Cuando alguien pacta un interés más alto que la tasa de usura permitida, la ley dice que ese
exceso no es válido. Pero hay dos formas posibles de que el software reaccione: (a) rechazar de plano la
liquidación con un error, obligando a corregir la tasa antes de continuar, o (b) aceptar la liquidación
pero recortar automáticamente la tasa al máximo legal permitido y seguir calculando con ese tope. El PDF de
requisitos de BASTIUM menciona las dos variantes en secciones distintas, sin decidir cuál usar.

**Pregunta:** ¿Cuál de las dos debe hacer el software cuando detecta una tasa pactada por encima de la
usura: rechazar con error, o recortar automáticamente al tope legal y continuar?

**Qué necesito exactamente:** Una de las dos opciones (rechazar / recortar automáticamente), o una tercera
si aplica (ej. "depende de si hay o no acción judicial ya iniciada").

**Respuesta del despacho:**
Cuando un interés pactado supera la tasa de usura, la ley no permite simplemente "recortarlo" al tope legal. La sanción legal es la pérdida del exceso y la obligación de devolverlo doblado al deudor.
**Qué puede hacerse?:**

Crear un Trigger de Validación de Usura.
Si Tasa_Pactada > Tasa_Usura_Vigente:
NO recortar la tasa silenciosamente.
Calcular el exceso: Intereses_Cobrados_En_Exceso = Intereses_Cobrados - Intereses_Cobrados_Con_Tasa_Usura.
Calcular sanción: Sancion = Intereses_Cobrados_En_Exceso * 2.
Restar Sancion del saldo total de la obligación, generando un saldo a favor del deudor si la cifra resulta negativa.

**Fecha:** 27/07/2026


---

## Sprint 3 — Área Laboral

**Contexto:** Ya resuelto en su mayoría — quedan dos puntos documentados como pendientes explícitos (no
olvidos) al cerrar el sprint, que siguen sin confirmación jurídica formal.

**Pregunta 1:** ¿Al calcular los días trabajados de un contrato para efecto de cesantías/prestaciones,
el primer día de labor debe contarse como "trabajado" (conteo inclusivo) o no (resta simple de fechas)?
Hoy el software usa resta simple (ej. contrato de 1-ene a 31-dic de un año bisiesto da 365 días, no 366).
Esta misma pregunta aplica también al Sprint 17 y al Sprint 30 — es una sola convención que se necesita
para todo el sistema, no una por sprint.

**Qué necesito exactamente:** Confirmar si la convención actual (no inclusiva) es la correcta según la
práctica laboral colombiana, o si debe cambiarse a inclusiva.

**Respuesta del despacho:**
Existen dos lógicas de conteo dependiendo del rubro. Para prestaciones se usa un año comercial de 360 días, pero para pensiones la Corte Suprema obliga a usar días calendario reales (365/366). En ambos casos, el primer día cuenta (conteo inclusivo).

Fórmula base de días: Dias_Trabajados = (Fecha_Fin - Fecha_Inicio) + 1. (Se debe corrigir el error de la resta simple en todos los módulos).
Para Prestaciones (Cesantías/Primas): Usar la fórmula base bajo la premisa de meses de 30 días (año comercial de 360 días).
Para Densidad Pensional (Semanas): Usar la fórmula base con días calendario reales (365 o 366). Luego, Semanas_Cotizadas = Total_Dias_Reales / 7.

**Fecha:** 27/07/2026

---

## Sprint 4 — Sancionatorio y Honorarios

**Contexto:** El PDF de requisitos de BASTIUM tiene una inconsistencia interna: en una sección dice que la
suma de honorarios fijos + cuota litis no puede superar el 50% del beneficio obtenido por el cliente, y en
otra sección (dedicada específicamente a "Litigio y Cobro de Honorarios") dice 30%. El desarrollo decidió
—junto con Jose, no de forma unilateral— aplicar **ambos topes simultáneamente**: 30% individual sobre la
cuota litis sola, y 50% total sobre honorarios fijos + cuota litis juntos. Es una interpretación razonable
para no elegir un número al azar, pero no ha sido confirmada por un abogado.

**Pregunta:** ¿Es correcto aplicar ambos topes simultáneamente (30% a la cuota litis sola, 50% al total), o
debería ser solo uno de los dos como tope único?

**Qué necesito exactamente:** Confirmación de la interpretación actual, o la regla correcta con su fuente
normativa si es distinta.

**Respuesta del despacho:**
No se aplican ambos topes (30% y 50%) en cascada. El tope legal absoluto y definitivo es del 50% acumulado.
**Qué puede hacerse?:**

Crear validación de legalidad contractual: Total_Honorarios = Honorarios_Fijos + (Beneficio_Obtenido * Porcentaje_Cuota_Litis).
Si Total_Honorarios > (Beneficio_Obtenido * 0.50): El sistema debe emitir una alerta de riesgo disciplinario ("Honorarios Desproporcionados - Art. 35 Num. 4 Ley 1123/2007") y bloquear la liquidación o ajustar el excedente.

**Fecha:** 27/07/2026

---

## Sprint 5 — Datos históricos (UVT)

**Contexto:** El software ya tiene cargadas las series históricas de Salario Mínimo, IPC e Interés Bancario
Corriente/Usura desde 1997-2024 en adelante (verificadas contra el PDF y fuentes externas). La UVT (Unidad
de Valor Tributario, usada por la DIAN) también quedó cargada completa 2006-2026, pero **no viene del PDF
de BASTIUM** — el PDF solo menciona un valor aislado que en realidad correspondía a otro año. La serie que
hoy usa el software se armó cruzando 3 fuentes externas independientes.

**Pregunta:** ¿Pueden confirmar que la tabla de UVT que usamos (2006 = $20.000 ... 2026 = $52.374,
resolución DIAN de cada año) coincide con sus registros, o tienen una fuente oficial distinta que debamos
usar?

**Qué necesito exactamente:** Un sí/no de confirmación, o la tabla corregida si encuentran alguna
diferencia con sus registros.

**Respuesta del despacho:**
La serie histórica cruzada por el equipo es correcta. La conversión de pesos a UVT tiene una regla de redondeo estricta.

Mantener la tabla UVT cargada (2006=$20.000 ... 2024=$47.065).
Regla de conversión y redondeo (Art. 868 E.T.): Al convertir pesos a UVT, si el resultado es mayor a $10.000, el sistema debe aproximar el valor al múltiplo de mil más cercano.
Usar la UVT del año gravable correspondiente al hecho generador para bases, y la UVT vigente al momento del pago para sanciones.

**Fecha:** 27/07/2026

---

## Sprint 6 — Calendario de días hábiles

**Contexto:** El software calcula días hábiles judiciales (excluyendo sábados, domingos y festivos
colombianos) usando una librería de código abierto (`holidays`, país Colombia) en vez de una tabla propia
transcrita a mano. El PDF menciona que existen "vacancias judiciales" (pausas del sistema judicial, ej. fin
de año) pero no da fechas exactas, así que el software **no las modela** — solo excluye fines de semana y
festivos oficiales.

**Pregunta:** Para el cómputo de términos procesales, ¿hace falta modelar también las vacancias judiciales
como días no hábiles adicionales, o basta con festivos + fines de semana como está hoy?

**Qué necesito exactamente:** Sí/no, y si la respuesta es sí, las fechas exactas de vacancia judicial que
deban aplicar (son fijas cada año, ej. mediados de diciembre a mediados de enero).

**Respuesta del despacho:**
Los fines de semana y festivos no son suficientes. Las vacancias judiciales son obligatorias y deben restarse del cómputo de términos.
Instrucción de desarrollo:

Actualizar el motor de calendario para excluya automáticamente:
Fines de semana (Sábado y Domingo).
Festivos oficiales (Ley 51 de 1883).
Vacancia de Fin de Año: Excluir del 20 de diciembre al 11 de enero de cada año (inclusive). El 12 de enero es hábil (salvo que caiga fin de semana/festivo).
Semana Santa: Excluir Lunes, Martes y Miércoles Santo (además del Jueves y Viernes Santo que ya son festivos).

**Fecha:** 27/07/2026

---

## Sprint 7 — Prescripción y caducidad

**Contexto:** El software calcula plazos de prescripción/caducidad para varios tipos de acción. Un caso
puntual: la ley cambiaria (letras, cheques, pagarés) tiene tres plazos distintos según el tipo de acción
(directa: 3 años; de regreso del tenedor: 1 año; entre obligados de regreso: 6 meses). El PDF menciona el
plazo de 6 meses en una sección distinta a los otros dos, y el desarrollo interpretó que es un tercer
supuesto real (no un error del documento). Además, cualquier tipo de caducidad que no sea el único caso que
trae el PDF con plazo confirmado (impugnación de ineficacia societaria, 5 años) exige que el usuario
ingrese el plazo manualmente — el software no lo asume.

**Pregunta:** ¿Confirman los tres plazos cambiarios (3 años / 1 año / 6 meses) tal como están descritos
arriba? ¿Hay otros tipos de proceso con plazo de caducidad fijo y conocido que debamos precargar en el
software en vez de pedir que se ingrese manualmente cada vez?

**Qué necesito exactamente:** Confirmación de los 3 plazos cambiarios, y opcionalmente una lista de otros
plazos de caducidad frecuentes en la práctica del despacho.

**Respuesta del despacho:**
Los tres plazos cambiarios están confirmados. Hay otros plazos comerciales fijos que deben precargarse.
Instrucción de desarrollo:

Configurar plazos automáticos para Títulos Valores:
Acción Directa: 3 años desde el vencimiento.
Acción de Regreso (Tenedor): 1 año desde fecha de protesto o vencimiento.
Acción Ulterior Regreso: 6 meses desde el pago.
Precargar en el sistema los siguientes plazos fijos de caducidad/prescripción: Cheques (6 meses), Enriquecimiento sin causa (1 año), Transporte (2 años), Seguro (2 y 5 años), Impugnación de Actas Sociales (2 meses).

**Fecha:** 27/07/2026

---

## Sprint 8 — Indexación IPC Civil/Familia

**Contexto:** Cuando se actualiza un capital histórico con el IPC, la fórmula del PDF supone que existe
una certificación mensual del IPC. En la práctica, la fuente que tenemos solo trae variación **anual**, así
que el software interpola entre los índices de cierre de año (31 de diciembre de cada año) en vez de entre
meses. Además, para fechas del año en curso (donde aún no hay IPC de cierre de año publicado), el software
usa el IPC del año anterior como aproximación.

**Pregunta:** ¿Esta aproximación (interpolar entre cierres de año en vez de entre meses, y usar el IPC del
año anterior para el año en curso) es aceptable para el uso que le da el despacho, o hace falta una fuente
de IPC mensual más precisa?

**Qué necesito exactamente:** Sí/no de aceptación, o la fuente de IPC mensual si se necesita mayor
precisión.

**Respuesta del despacho:**
La interpolación por cierres de año es jurídicamente inválida y será objetada por un juez. El IPC debe ser mensual del DANE. Proyectar el año en curso con IPC anterior también es un error.
Instrucción de Desarrollo:

Obtener y cargar la serie histórica del IPC mensual del DANE.
Fórmula base de indexación: Renta_Actual = Renta_Historica * (IPC_Final / IPC_Inicial).
IPC_Inicial: Índice del mes en que nació la obligación.
IPC_Final: Índice del mes más reciente certificado por el DANE.
Interpolación lineal de días: Si la fecha de inicio o corte no cae en el último día del mes, aplicar interpolación matemática entre el IPC del mes anterior y el mes posterior para hallar el factor exacto del día.
Prohibir el uso de promedios anuales o proyecciones del año en curso.

**Fecha:** 27/07/2026

---

## Sprint 11 — Derecho Tributario (DIAN)

**Contexto:** Este fue el primer sprint que agregó liquidaciones tributarias (DIAN) al software, un
dominio completamente nuevo para BASTIUM. La decisión de negocio (ya tomada) fue construir únicamente
interés moratorio tributario y depuración de Renta Líquida Gravable en una primera etapa, dejando sanciones
e imputación para un sprint posterior (ya completado en el Sprint 15, ver abajo).

**Pregunta:** No hay pregunta pendiente de este sprint puntual — se deja la sección aquí solo como
referencia, por si el despacho quiere confirmar que el área Tributaria en general sí es prioritaria para el
producto (ya se construyó, pero es bueno tener la confirmación explícita).

**Qué necesito exactamente:** Nada urgente — opcional.

**Respuesta del despacho:**
Las prioridades son correctas. Hay reglas estrictas de imputación de pagos y concurrencia de intereses.
Instrucción de desarrollo:

Imputación de Pagos (Art. 804 E.T.): Todo pago parcial debe imputarse en este orden estricto: 1º Sanciones, 2º Intereses, 3º Impuesto/Anticipos/Retenciones.
Sanciones (Piso mínimo): Ninguna sanción puede ser inferior a 10 UVT vigentes al momento de la liquidación.
Concurrencia Intereses vs. Actualización (Art. 867-1 E.T.): Si una deuda tributaria tiene más de 3 años de mora:
Sobre el rubro "Impuesto": Liquidar interés moratorio diario + actualización monetaria (IPC). Validación: La tasa combinada no puede superar la tasa de usura vigente; si la supera, topearla en usura.
Sobre el rubro "Sanciones": NO liquidar interés moratorio. Aplicar ÚNICAMENTE actualización monetaria por IPC.

**Fecha:** 27/07/2026

---

## Sprint 12 — TRM y moneda extranjera

**Contexto:** Para obligaciones comerciales en dólares, el software convierte el monto a pesos usando la
Tasa Representativa del Mercado (TRM) que el abogado ingresa manualmente por cada obligación (no hay una
tabla histórica automática de TRM diaria, porque el PDF de requisitos no la trae). La conversión se hace
una sola vez, al inicio de la liquidación, no de forma continua con cada abono.

**Pregunta:** ¿Es correcto que la conversión a pesos se haga una sola vez al inicio (con la TRM de la
fecha de la obligación), o debería recalcularse con la TRM vigente en la fecha de cada pago/abono?

**Qué necesito exactamente:** Confirmación de cuál de las dos formas es la jurídicamente correcta (Art.
874 C.Co.).

**Respuesta del despacho:**
La conversión NO se hace al inicio. La deuda se mantiene en divisa y la conversión a pesos se hace dinámicamente en la fecha de cada pago.
Instrucción de Desarrollo:

Mantener el saldo de la obligación almacenado en la divisa original (ej. COP, USD).
Por cada abono o pago, consumir la TRM de la API de la Superintendencia Financiera correspondiente a la Fecha_de_Pago.
Convertir el valor del abono en divisa a COP usando esa TRM dinámica y luego aplicar la imputación de pagos. Eliminar la lógica de "TRM congelada al inicio".

**Fecha:** 27/07/2026

---

## Sprint 13 — Motor de reglas / parámetros legales

**Contexto:** Este sprint fue una decisión de arquitectura (no una pregunta legal): se decidió que las
tasas, topes y porcentajes legales (usura, cuota litis, SMLMV, IPC, etc.) vivan en una tabla editable desde
la pantalla de "Parámetros" del software, para que puedan actualizarse sin necesitar un programador. No hay
pregunta jurídica pendiente aquí.

**Pregunta:** Ninguna — sección informativa. Si en el futuro alguien del despacho va a ser quien actualice
esos parámetros directamente desde la pantalla de Parámetros, avisar para preparar una guía de uso corta.

**Qué necesito exactamente:** Nada urgente — opcional.

**Respuesta del despacho:**
Imputación de Pagos (Art. 804 E.T.): Todo pago parcial debe imputarse en este orden estricto: 1º Sanciones, 2º Intereses, 3º Impuesto/Anticipos/Retenciones.
Sanciones (Piso mínimo): Ninguna sanción puede ser inferior a 10 UVT vigentes al momento de la liquidación.
Concurrencia Intereses vs. Actualización (Art. 867-1 E.T.): Si una deuda tributaria tiene más de 3 años de mora:
Sobre el rubro "Impuesto": Liquidar interés moratorio diario + actualización monetaria (IPC). Validación: La tasa combinada no puede superar la tasa de usura vigente; si la supera, topearla en usura.
Sobre el rubro "Sanciones": NO liquidar interés moratorio. Aplicar ÚNICAMENTE actualización monetaria por IPC.

____________________________________________________
La Unidad de Valor Tributario (UVT) fue creada mediante la Ley 1111 de 2006 (modificando el Art. 868 del Estatuto Tributario - E.T.) con el fin de unificar y facilitar el cumplimiento de las obligaciones tributarias, reemplazando al salario mínimo como unidad de medida para impuestos, sanciones y cuantías

Su valor se reajusta anualmente el 1 de enero de cada año, basándose en la variación del Índice de Precios al Consumidor (IPC) para ingresos medios, certificado por el DANE para el periodo comprendido entre el 1 de octubre del año anterior al gravable y la misma fecha del año precedente

A continuación, presento la tabla de progresión histórica. Cabe aclarar que, dado que las fuentes suministradas solo mencionan explícitamente el valor de 2023 ($42.412)
, el resto de los valores se proveen desde registros oficiales externos que usted debe verificar de forma independiente para garantizar la precisión quirúrgica del software:
Año
Valor UVT
Resolución de Fijación (Referencia Externa)
2006
$20.000
Ley 1111 de 2006 (Valor base inicial)
2007
$20.974
Resolución DIAN 15631 de 2006
2008
$22.054
Resolución DIAN 15013 de 2007
2009
$23.763
Resolución DIAN 011945 de 2008
2010
$24.555
Resolución DIAN 012115 de 2009
2011
$24.755
Resolución DIAN 012066 de 2010
2012
$26.049
Resolución DIAN 000119 de 2011
2013
$26.841
Resolución DIAN 000138 de 2012
2014
$26.841
Resolución DIAN 000227 de 2013
2015
$27.485
Resolución DIAN 000245 de 2014
2016
$28.279
Resolución DIAN 000115 de 2015
2017
$29.753
Resolución DIAN 000071 de 2016
2018
$31.859
Resolución DIAN 000063 de 2017
2019
$34.270
Resolución DIAN 000056 de 2018
2020
$35.607
Resolución DIAN 000084 de 2019
2021
$35.607
Resolución DIAN 000111 de 2020
2022
$36.308
Resolución DIAN 000140 de 2021
2023
$42.412
Resolución DIAN 001264 de 2022
2024
$47.065
Resolución DIAN 000187 de 2023
2025
(Por definir)
Se fija en octubre/noviembre de 2024
2026
$52.374*
(Valor proyectado según su consulta)

**Fecha:** 27/07/2026

---

## Sprint 15 — Tributario: sanciones e imputación

**Contexto:** El software calcula 3 sanciones tributarias (extemporaneidad, inexactitud, error aritmético)
con un piso legal de 10 UVT, y aplica el orden de pago que exige el PDF para tributario (sanciones →
intereses → impuesto). El PDF (pág. 40) además advierte que "no se pueden cobrar simultáneamente intereses
moratorios y actualización monetaria si esto conduce a una tasa usuraria o doble pago por el mismo
concepto" — esta validación quedó **documentada como advertencia**, no como un bloqueo automático en el
software, porque hoy no hay ningún caso de uso real que combine ambas cosas en el mismo expediente
tributario.

**Pregunta:** ¿Existen casos reales del despacho donde sí se combinen intereses moratorios y actualización
monetaria en un mismo proceso tributario? Si es así, necesitamos un ejemplo real para poder construir la
validación automática correctamente.

**Qué necesito exactamente:** Sí/no, y si es sí, un caso de ejemplo (montos, fechas, tipo de sanción).

**Respuesta del despacho:**
La Corte Constitucional, en la Sentencia C-549 de 1993, determinó que la actualización del valor de una deuda (indexación) y el cobro de intereses moratorios tienen naturalezas distintas: la primera conserva el valor adquisitivo frente a la inflación y la segunda indemniza el daño emergente por la mora.
Regla de Oro: Pueden concurrir siempre y cuando la suma de ambos no supere el límite de usura y la corrección monetaria no sea "doblemente considerada" (es decir, que el interés de mora no incluya ya el componente inflacionario).

El Caso Real: Artículo 867-1 del Estatuto Tributario
Este artículo dispone la actualización de las deudas tributarias que tengan más de tres (3) años de vencidas

Ejemplo de validación para el motor:
Fecha de vencimiento original: 10 de mayo de 2018.
Impuesto a cargo: $100.000.000.
Fecha de pago: 10 de mayo de 2023 (5 años de mora).
Cálculo:
Intereses Moratorios: Se liquidan diariamente desde el 11 de mayo de 2018 hasta la fecha de pago a la tasa de usura certificada por la Superfinanciera

Actualización (Indexación): Al haber pasado más de 3 años, se aplica la fórmula: ValorActual=ValoraIndexar×(IPC presente/IPC inicial).

Restricción del Sistema: El software debe verificar que la Tasa Efectiva Combinada (Interés + Factor de Indexación) sea ≤ Tasa de Usura del periodo. Si la supera, debe "topear" el cobro al límite de usura

Caso especial de sanciones: Para el pago extemporáneo de sanciones, no se liquida interés de mora, sino que se aplica exclusivamente la actualización inflacionaria según el Art. 867-1 E.T.

INSTRUCCIÓN DE DESARROLLO: Programar una condicional lógica que desactive el interés moratorio sobre el rubro de "Sanciones" y aplique en su lugar el factor de actualización del Art. 867-1 E.T. si la mora supera los 3 años. Para el rubro "Impuesto", aplicar ambos conceptos validando el techo de usura.

**Fecha:** 27/07/2026

---

## Sprint 16 — Seguridad social e incapacidades laborales

**Contexto:** Ya resuelto en su mayoría con confirmación del usuario, pero dos tablas de porcentajes se
completaron con **fuentes externas al PDF** (verificadas, no inventadas) porque el PDF de BASTIUM solo da
los valores extremos:
- Niveles de riesgo ARL II, III y IV (el PDF solo da el nivel I y el nivel V) — se usó el Decreto
  1607/2002: II = 1.044%, III = 2.436%, IV = 4.350%.
- Tramos del Fondo de Solidaridad Pensional -FSP- (el PDF solo dice "escala progresiva desde 1% hasta 2%",
  sin tramos exactos) — se usó la Ley 797/2003 art. 8: de 4 a 16 SMMLV = 1%, 16-17 = 1.2%, 17-18 = 1.4%,
  18-19 = 1.6%, 19-20 = 1.8%, más de 20 = 2%.

**Pregunta:** ¿Confirman que estos dos porcentajes/tablas (ARL II-IV del Decreto 1607/2002, y FSP de la
Ley 797/2003 art. 8) son los vigentes y correctos a la fecha?

**Qué necesito exactamente:** Sí/no de confirmación, o la tabla corregida si alguna norma posterior cambió
estos porcentajes.

**Respuesta del despacho:**
Así es, aunque con leves precisiones en topes máximos.

Se debe cargar tabla ARL (Empleador): Nivel I=0.522%, II=1.044%, III=2.436%, IV=4.350%, V=6.960%.
FSP (Fondo Solidaridad Pensional): Activar trigger si IBC >= 4 SMMLV.
4 a 16 SMMLV: 1%
16 a 17 SMMLV: 1.2%
17 a 18 SMMLV: 1.4%
18 a 19 SMMLV: 1.6%
19 a 20 SMMLV: 1.8%
20 SMMLV: 2.0%

**Fecha:** 27/07/2026

---

## Sprint 17 — Módulo pensional (IBL, tasa de reemplazo, semanas)

**Contexto:** Este es el sprint en curso — el de mayor incertidumbre de dominio de todo el desarrollo. El
PDF de BASTIUM solo trae la fórmula base de la tasa de reemplazo (`r = 65.5 − 0.5·s`), pero en la práctica
real colombiana (Ley 100 de 1993, art. 34) esa fórmula tiene además: un piso de 65%, un techo de 80%, y un
bono de +1.5% por cada 50 semanas cotizadas por encima de 1.300. Se implementó la fórmula completa
(verificada con fuentes externas, no solo la línea literal del PDF), pero sin confirmación directa de un
despacho jurídico.

**Pregunta 1:** ¿Confirman que la fórmula completa de tasa de reemplazo es correcta tal como está descrita
arriba (piso 65%, techo 80%, bono +1.5% cada 50 semanas sobre 1.300)?

**Pregunta 2:** ¿Tienen algún caso pensional real (de Colpensiones o de un proceso ya resuelto) con IBL,
semanas cotizadas y tasa de reemplazo ya calculados, que podamos usar como caso de prueba adicional al que
ya usamos (Sentencia SL138-2024, sobre el conteo de semanas)?

**Pregunta 3 (compartida con Sprint 3 y Sprint 30):** Para contar días de un periodo cotizado (ej. de una
fecha a otra), ¿el primer día debe contarse como cotizado (conteo inclusivo) o no (resta simple de
fechas)? Es la misma pregunta del Sprint 3, la respuesta aplica a los tres sprints por igual.

**Qué necesito exactamente:** Confirmación de la fórmula completa (pregunta 1); un caso real si existe
(pregunta 2, opcional); y la convención de conteo de días (pregunta 3).

**Respuesta del despacho:**

La fórmula está confirmada. Hay un caso de prueba exacto para validar el código.
Instrucción de Desarrollo:

Implementar función: Tasa_Reemplazo = 65.5 - (0.5 * s); donde s = IBL / SMMLV.
Validar que la tasa inicial resultante esté en el rango [55% , 65.5%].
Calcular bono: Bono = floor((Semanas_Cotizadas - 1300) / 50) * 1.5%.
Aplicar techo final: Tasa_Final = min(Tasa_Inicial + Bono, 80%).
Caso de Prueba QA (Verificar código):
IBL: $800.000 / SMMLV: $400.000 (s=2)
Semanas: 1664
Resultado esperado: Tasa Inicial = 64.5%. Exceso = 364 semanas. 7 bloques de 50 (7.5% redondeado a 7, o 7.5 si se permite decimal Nota del dev: revisar si se permite decimal o bloqueo estricto -> Corrección: 589 exceso / 50 = 11.78 -> 11 grupos. Bono = 16.5%). Total = 81%. Techo aplica. Tasa final = 80%. Pensión = $640.000.

___________________________________________________
1. En el derecho laboral colombiano coexisten dos lógicas de conteo que el software debe parametrizar según el rubro a liquidar.
Liquidación de Prestaciones Sociales (Cesantías, Prima, Intereses): El conteo es inclusivo. Jurídicamente, el primer día de labores se cuenta como trabajado para no expropiar al trabajador de 24 horas de remuneración y carga prestacional
. La fórmula matemática estándar aplicada es (Fecha_Fin - Fecha_Inicio) + 1
. Por ejemplo, si un contrato inicia el 1.° de enero y termina el 31 de diciembre, el sistema debe arrojar 360 días (bajo año comercial) o 365/366 (bajo año calendario) incluyendo ambos extremos.
Densidad de Semanas para Pensión (Hito Jurisprudencial): Aquí el motor debe romper con la "ficción legal" de los meses de 30 días. La Sala de Casación Laboral de la Corte Suprema de Justicia, en la Sentencia SL138-2024, estableció un cambio de paradigma: para efectos pensionales, las semanas ya no se calculan sobre años de 360 días, sino sobre días reales del calendario (365 o 366 días)
. Esto es crítico para la portabilidad de semanas. El sistema debe sumar cada día efectivamente cotizado y dividir el gran total de días por 7 para hallar la densidad de semanas exacta
.
INSTRUCCIÓN DE DESARROLLO: Implementar un algoritmo de resta de fechas con conteo inclusivo (sumar 1 al diferencial). Para el módulo de prestaciones, usar base 360 días; para el módulo de densidad pensional, usar base de días calendario reales según la Sentencia SL138-2024.
_________________________________
2. Los porcentajes citados son parcialmente correctos, pero requieren precisión técnica según las actualizaciones de la Ley 797 de 2003 y decretos reglamentarios.
Riesgos Laborales (ARL): Los niveles y porcentajes iniciales vigentes para la cotización a cargo del empleador son
:
Nivel I: 0.522%
Nivel II: 1.044%
Nivel III: 2.436%
Nivel IV: 4.350%
Nivel V: 6.960% (con un tope máximo legal del 8.7% según la Ley 1562 de 2012)
.
Fondo de Solidaridad Pensional (FSP): Se mantiene la estructura de la Ley 797 de 2003. Todo IBC igual o superior a 4 SMMLV aporta un 1% (0.5% para la Subcuenta de Solidaridad y 0.5% para la de Subsistencia)
. Para ingresos superiores, se aplica una sobretasa progresiva destinada íntegramente a la Subcuenta de Subsistencia
:
4 a 16 SMMLV: 1% (Total)
16 a 17 SMMLV: 1% + 0.2% = 1.2%
17 a 18 SMMLV: 1% + 0.4% = 1.4%
18 a 19 SMMLV: 1% + 0.6% = 1.6%
19 a 20 SMMLV: 1% + 0.8% = 1.8%
Superior a 20 SMMLV: 1% + 1.0% = 2.0%
.
INSTRUCCIÓN DE DESARROLLO: Programar tabla de ARL con base en los 5 niveles (0.522% a 6.960%). Para FSP, crear un trigger que se active a partir de 4 SMMLV y aplique la escala progresiva hasta alcanzar el tope del 2% para IBC > 20 SMMLV.
_______________________________
3. La fórmula de la Ley 100 de 1993 (Art. 34), modificada por el Art. 10 de la Ley 797 de 2003, es efectivamente la fórmula decreciente, diseñada para que a mayor ingreso, menor sea el porcentaje de protección pensional
.
Fórmula: r=65.5−0.5s
.
Variable "s": Es el número de salarios mínimos legales mensuales vigentes contenidos en el IBL (Ingreso Base de Liquidación)
.
Piso y Techo de la Tasa Inicial: El resultado de esta fórmula oscila entre el 65.5% (para quienes ganan 1 SMMLV) y el 55% (para quienes ganan 20 SMMLV o más)
.
Bono por Semanas Adicionales: Por cada 50 semanas adicionales a las mínimas requeridas (1.300 semanas hoy), el porcentaje aumenta un 1.5%
.
Techo Final: El porcentaje total (Tasa Inicial + Bonos) no puede superar el 80% del IBL
.
Caso de Prueba (Basado en doctrina de Arenas Monsalve
):
IBL: $800.000 (Equivalente a 2 salarios mínimos del año 2006 para el ejemplo).
Semanas Cotizadas: 1.664 semanas.
Paso 1 (Hallar r): s=2. Entonces r=65.5−(0.5×2)=64.5%.
Paso 2 (Hallar bonos): Mínimo requerido en ese año: 1.075 semanas. Exceso: 1.664−1.075=589 semanas.
Paso 3 (Calcular incremento): 589 / 50 = 11 grupos de 50 semanas. Incremento = 11×1.5%=16.5%.
Paso 4 (Total): 64.5%+16.5%=81%.
Ajuste por Techo: Como supera el límite, la tasa final es 80%.
Resultado: Pensión = 800.000×80%=$640.000.
INSTRUCCIÓN DE DESARROLLO: Implementar la función CALCULAR_R(IBL, SMMLV, SEMANAS). Debe primero validar el rango de la tasa inicial (55%-65.5%), luego calcular incrementos de 1.5% por cada bloque completo de 50 semanas adicionales sobre el requisito del año de causación, y finalmente aplicar un MAX_CAP del 80% sobre el IBL resultante.

**Fecha:** 27/07/2026

---

## Sprint 18 — Costas judiciales (tabla de rangos)

**Contexto:** El PDF de BASTIUM menciona que las costas judiciales (agencias en derecho) se fijan según
rangos de porcentaje del Consejo Superior de la Judicatura (cita el Acuerdo PCSJA20-11556 como ejemplo,
"3% al 7% de las pretensiones reconocidas"), pero **no transcribe la tabla completa de rangos**. Este
acuerdo tampoco se consiguió durante los Sprints 4 ni 18 buscando en fuentes públicas. Hoy el software solo
permite ingresar el porcentaje de costas manualmente por cada obligación, sin calcularlo automáticamente
por rango de cuantía.

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

**Fecha:**

---

## Sprint 30 — Posible error de un día

**Contexto:** Una revisión de código encontró dos posibles errores sutiles de "un día" en el sistema, y
ambos necesitan confirmación jurídica antes de decidir si se corrigen (ya que corregirlos cambiaría el
resultado numérico de liquidaciones existentes):

1. Para decidir si una notificación de demanda "retrotrae" el efecto interruptor de la prescripción a la
   fecha de la demanda, el software hoy compara si pasaron `365 días o menos` entre la radicación y la
   notificación. En años bisiestos, 365 días puede ser un día calendario menos que "un año" real,
   activando la regla un día antes de lo que correspondería.
2. Para contar los días trabajados de un contrato (cesantías/prestaciones), el software resta las fechas
   sin sumar 1 (ej. un contrato de 1-ene a 31-dic de un año bisiesto da 365 días, no 366). Es la misma
   pregunta del Sprint 3 y el Sprint 17 sobre conteo inclusivo vs. no inclusivo.

**Pregunta 1:** Para prescripción, ¿"dentro de un año" debe interpretarse como fecha-a-fecha (ej. de
1-mar-2023 a 1-mar-2024, sin importar si hay bisiesto en medio), o como una cuenta fija de 365 días
corridos?

**Pregunta 2:** Ver Sprint 3/17 — es la misma pregunta de conteo inclusivo de días.

**Qué necesito exactamente:** La interpretación correcta para prescripción (pregunta 1). La pregunta 2 ya
está cubierta en la sección del Sprint 3.

**Respuesta del despacho:**

El término de un año no son 365 días matemáticos. Es fecha a fecha estricta en el calendario.
Instrucción de Desarrollo:

Eliminar la validación if (dias <= 365).
Nueva lógica: Fecha_Vencimiento = Fecha_Notificacion_Demandante.AddYears(1).
Si la fecha resultante no existe (ej. 29-Feb en año no bisiesto), asignar el 28-Feb.
Regla de inhabilidad: Si Fecha_Vencimiento cae en sábado, domingo, festivo o vacancia judicial (ver Sprint 6), la fecha límite se desplaza al siguiente Día_Hábil_Judicial.
___________________________________________________________

1. El sistema debe diferenciar la naturaleza del rubro para aplicar la base temporal correcta.
Para Prestaciones Sociales (CST): Aplicar la lógica de conteo inclusivo. El cálculo de días laborados debe seguir la fórmula: (Fecha_Fin - Fecha_Inicio) + 1 [Instrucción anterior]. Para cesantías, prima y vacaciones, se mantiene la base de 360 días anuales (meses de 30 días), pues es la base comercial aceptada por el Código Sustantivo del Trabajo.
Para Densidad de Semanas (Pensión): El sistema debe parametrizar la Sentencia SL138-2024. Queda prohibido usar el mes de 30 días para efectos pensionales. El software debe contar días calendario reales (365 o 366) y dividir el total de días acumulados entre 7 para obtener el número de semanas con decimales.
Fundamento: La Corte Suprema determinó que el uso del año comercial perjudica la densidad necesaria para el derecho pensional [SL138-2024].
INSTRUCCIÓN DE DESARROLLO: Crear una función DÍAS_LABORADOS(inclusivo=True, base=360) para prestaciones y SEMANAS_PENSION(inclusivo=True, base=Calendario_Real) para el módulo pensional.
___________________________________
2. El Ingreso Base de Cotización es la variable crítica. El software debe validar los siguientes escenarios según el tipo de registro:
Trabajador Dependiente: IBC = 100% de lo devengado que constituya salario (Art. 127 CST)
Límite Inferior: 1 SMMLV ($1.750.905 en 2026)
Límite Superior: 25 SMMLV ($43.772.625)
Salario Integral: IBC = 70% del valor total pactado
Trabajador Independiente (Contrato de Servicios): IBC = 40% del valor mensual neto del contrato (sin IVA)
Validación: Si el 40% es menor a 1 SMMLV, el sistema debe "setear" el IBC automáticamente en 1 SMMLV
___________________________________
3. Para liquidar el monto de la pensión en el Régimen de Prima Media, el software debe ejecutar la siguiente secuencia:
Cálculo de 's': s=IBL/SMMLV (donde IBL es el promedio actualizado de los últimos 10 años)
Cálculo de Tasa Inicial (r): r=65.5−(0.5×s)
Restricción de software: El valor de r resultante nunca puede ser inferior al 55% ni superior al 65.5%
Bono por Densidad: Por cada bloque completo de 50 semanas adicionales a las 1.300 mínimas, sumar 1.5% al valor de r
Techo Final: La suma de r+Bonos nunca puede exceder el 80% del IBL
INSTRUCCIÓN DE DESARROLLO: Implementar r = MAX(55, MIN(65.5, 65.5 - 0.5 * (IBL / SMMLV))). Luego, Total = MIN(80, r + (Semanas_Extra / 50) * 1.5).
___________________________________
4. Fondo de Solidaridad Pensional (FSP): El disparador se activa si IBC≥4 SMMLV
Base: 1% (Solidaridad + Subsistencia).
Surcharge (Subsistencia): Aplicar escala progresiva de la Ley 797 de 2003 (de 0.2% a 1% adicional) hasta un total máximo de 2% para quienes superen los 20 SMMLV
Riesgos Laborales (ARL): El software debe tener una tabla de consulta para las 5 clases de riesgo:
I: 0.522% | II: 1.044% | III: 2.436% | IV: 4.350% | V: 6.960%
____________________________________
5. Si el software detecta una terminación de contrato sin el pago de liquidación en la fecha de corte, debe disparar el cálculo de la Indemnización Moratoria (Art. 65 CST):
Algoritmo: Días_Mora * (Salario_Diario_Último).
Límite: Se causa por 24 meses; después del mes 24, se detiene el pago diario y empiezan a correr intereses moratorios a la tasa máxima de la Superfinanciera sobre el saldo adeudado.
____________________________________
6. Caso de ejemplo:
IBL: $5.252.715 (Equivale a exactamente 3 SMMLV de 2026).
Semanas cotizadas: 1.500 semanas.
SMMLV 2026: $1.750.905.
Operación Lógica del Software:
Hallar s: 5.252.715/1.750.905=3.
Tasa inicial (r): 65.5−(0.5×3)=64%. (Está en el rango válido 55-65.5).
Semanas extra: 1.500−1.300=200 semanas.
Bloques de 50: 200/50=4 bloques.
Incremento: 4×1.5%=6%.
Tasa Final: 64%+6%=70%.
Monto Pensión: 5.252.715×70%=$3.676.900.
INSTRUCCIÓN DE DESARROLLO: El sistema debe arrojar este resultado exacto. Cualquier desviación decimal indicará un error en la configuración del punto flotante o en el truncamiento de semanas adicionales.

**Fecha:** 27/07/2026

---

## Plantilla para sprints futuros

Copiar este bloque y completarlo cuando un sprint nuevo tenga una decisión legal sin confirmar o una fuente
que falte:

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
