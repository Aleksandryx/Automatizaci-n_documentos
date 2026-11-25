# Conciliación automática de partidas bancarias: documentos SB y KZ

## 1. Descripción del caso de uso

En el presente documento se desarrolla un ejercicio de conciliación bancaria a partir de dos tipos de documentos contables.

El primero corresponde a las partidas generadas por el banco durante el día, las cuales se cargan al sistema mediante un archivo Multicash y se registran con un documento de clase **SB**.

El segundo corresponde a un documento de clase **KZ**, cargado por el área de Tesorería, que representa las salidas de dinero ordenadas por la compañía, tales como:

- Pagos a proveedores
- Pagos de impuestos
- Anticipos
- Pagos a transportistas

Ambos documentos deben cruzarse entre sí para poder compensarse. Cuando se presentan diferencias entre ellos, estas pueden indicar que el documento no ha sido pagado por el banco o que Tesorería aún no ha generado el documento correspondiente.

## 2. Criterios de cruce

El documento SB registra el NIT **sin** dígito de verificación, mientras que el documento KZ lo registra **con** dígito de verificación. Por esta razón, el proceso de conciliación homologa el NIT entre ambos documentos y busca igualar las partidas a partir de tres criterios:

| Criterio | Descripción |
|----------|-------------|
| NIT | Identificador del tercero, homologado entre SB y KZ |
| Fecha | Debe ser la misma en ambos documentos |
| Valor | Debe ser el mismo en ambos documentos |

Las partidas que no coinciden bajo estos criterios se separan para ser analizadas en detalle.

## 3. Explicación del proceso

El proceso consiste en conciliar **lo que el banco informa que pagó (SB)** con **lo que la empresa ordenó pagar (KZ)**.

1. **SB: lado del banco.** Cada día el banco reporta los movimientos reales de su cuenta. Esa información ingresa al sistema mediante el archivo Multicash.
2. **KZ: lado de la empresa.** Tesorería registra los pagos que debe realizar o ya realizó.
3. **Objetivo: compensar.** Si el banco debitó un valor *X* a un tercero y Tesorería registró un pago de *X* a ese mismo tercero el mismo día, las dos partidas se anulan entre sí y quedan conciliadas.
4. **Las partidas sin pareja son el foco del análisis.** Tienen dos lecturas posibles:
   - **Existe en KZ pero no en SB:** el pago fue registrado, pero el banco no lo ha ejecutado (documento no pagado).
   - **Existe en SB pero no en KZ:** el banco ya pagó, pero Tesorería no generó el documento.
5. **Problema técnico: el NIT.** El SB trae el NIT sin dígito de verificación (ej. `900123456`) y el KZ con él (ej. `900123456-7`). Un cruce directo fallaría aunque se trate del mismo tercero. La solución es homologar el NIT, comparando solo la parte base, y luego cruzar por **NIT + fecha + valor**.
6. **Resultado.** Las partidas que cruzan quedan conciliadas; las que no, conforman un listado de diferencias que se investiga una por una.

## 4. Beneficios

El principal beneficio de conciliar las partidas por NIT, fecha y valor es que el sistema empareja automáticamente cada movimiento que el banco reporta (documento SB) con el pago que Tesorería ordenó (documento KZ), algo que antes obligaba a revisar manualmente, partida por partida, y a corregir el dígito de verificación del NIT para que ambos documentos coincidieran. Al automatizar este cruce, las partidas que coinciden se compensan solas y el equipo contable dedica su tiempo únicamente a las que no cruzan, que son justamente las que requieren análisis: un pago registrado por Tesorería que el banco aún no ha ejecutado, o un pago que el banco ya ejecutó sin que Tesorería haya generado su documento. Esto permite detectar diferencias el mismo día en que ocurren, y no al cierre del mes, lo que acelera el cierre contable, da mayor certeza sobre cuánto dinero salió realmente de las cuentas y reduce el riesgo de pagos duplicados, por valores incorrectos o a terceros equivocados. Además, al tratarse de pagos de proveedores, impuestos, anticipos y transportistas, contar con esta claridad ayuda a cumplir las obligaciones a tiempo, responder con rapidez ante reclamaciones y dejar un soporte verificable de cada pago para auditoría.

A continuación se detallan cada uno de estos beneficios.

### 4.1 Reducción del trabajo manual
Sin la homologación del NIT, el analista debe comparar visualmente cada partida del banco contra cada pago de Tesorería, corrigiendo manualmente el dígito de verificación. Al automatizar el cruce, el equipo deja de dedicar tiempo a las partidas que coinciden y se enfoca solo en las excepciones.

### 4.2 Mayor rapidez en el cierre contable
Al conciliar las partidas de forma diaria y automática, se evita la acumulación de pendientes al cierre del mes. Esto agiliza la compensación de las cuentas y la entrega de información contable confiable.

### 4.3 Identificación oportuna de diferencias
El proceso separa de inmediato las partidas que no cruzan, lo que permite saber cuáles corresponden a:

- **Pagos registrados que el banco aún no ejecutó:** se puede hacer seguimiento con el banco o con Tesorería para confirmar el estado del pago.
- **Pagos ejecutados por el banco sin documento de Tesorería:** se puede exigir el registro faltante y regularizar la contabilidad.

### 4.4 Mejor control del flujo de caja
Contar con partidas conciliadas permite conocer con mayor certeza cuánto dinero salió realmente del banco y cuánto está pendiente de salir. Esto mejora la visibilidad de la posición de caja y la toma de decisiones de Tesorería.

### 4.5 Reducción de errores y riesgos
La conciliación por NIT, fecha y valor ayuda a detectar situaciones como:

- Pagos duplicados
- Pagos por valores incorrectos
- Pagos a terceros equivocados
- Movimientos bancarios no autorizados o no reconocidos

### 4.6 Cumplimiento y confianza con terceros
Dado que las partidas incluyen pagos de impuestos, proveedores y transportistas, tener claridad sobre qué se pagó y qué no permite cumplir las obligaciones en las fechas previstas y responder con rapidez ante reclamaciones de proveedores o transportistas.

### 4.7 Trazabilidad y soporte para auditoría
Cada partida conciliada deja una relación clara entre el movimiento del banco (SB) y el documento de Tesorería (KZ). Esto facilita la revisión por parte de auditoría y brinda un soporte verificable de cada pago.

## 5. Resumen

| Aspecto | Antes (cruce manual) | Después (cruce por NIT + fecha + valor) |
|---------|----------------------|------------------------------------------|
| Comparación de partidas | Manual, una por una | Automática |
| Tratamiento del NIT | Corrección manual del dígito de verificación | Homologación automática |
| Partidas revisadas | Todas | Solo las diferencias |
| Detección de pagos pendientes o sin documento | Tardía | Inmediata |
| Cierre contable | Lento, con acumulación de pendientes | Ágil, con conciliación diaria |
| Trazabilidad | Limitada | Completa entre SB y KZ |
