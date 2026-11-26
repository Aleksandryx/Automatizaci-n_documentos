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

1. **Las partidas sin pareja son el foco del análisis.** Tienen dos lecturas posibles:
   - **Existe en KZ pero no en SB:** el pago fue registrado, pero el banco no lo ha ejecutado (documento no pagado).
   - **Existe en SB pero no en KZ:** el banco ya pagó, pero Tesorería no generó el documento.

## 4. Beneficios

El principal beneficio de conciliar las partidas por NIT, fecha y valor es que el sistema empareja automáticamente cada movimiento que el banco reporta (documento SB) con el pago que Tesorería ordenó (documento KZ), algo que antes obligaba a revisar manualmente, partida por partida, y a corregir el dígito de verificación del NIT para que ambos documentos coincidieran. Esto permite detectar diferencias el mismo día en que ocurren, y no al cierre del mes, lo que acelera el cierre contable, da mayor certeza sobre cuánto dinero salió realmente de las cuentas y reduce el riesgo de pagos duplicados, por valores incorrectos o a terceros equivocados. Además, al tratarse de pagos de proveedores, impuestos, anticipos y transportistas, contar con esta claridad ayuda a cumplir las obligaciones a tiempo, responder con rapidez ante reclamaciones y dejar un soporte verificable de cada pago para auditoría.

## 5. Resumen

| Aspecto | Antes (cruce manual) | Después (cruce por NIT + fecha + valor) |
|---------|----------------------|------------------------------------------|
| Comparación de partidas | Manual, una por una | Automática |
| Tratamiento del NIT | Corrección manual del dígito de verificación | Homologación automática |
| Partidas revisadas | Todas | Solo las diferencias |
| Detección de pagos pendientes o sin documento | Tardía | Inmediata |
| Cierre contable | Lento, con acumulación de pendientes | Ágil, con conciliación diaria |
| Trazabilidad | Limitada | Completa entre SB y KZ |
