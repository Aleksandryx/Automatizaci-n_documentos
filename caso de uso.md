# Conciliación de partidas bancarias: documentos SB y KZ

## Descripción del proceso

En el presente documento se desarrolla un ejercicio de conciliación bancaria a partir de dos tipos de documentos contables.

El primero corresponde a las partidas generadas por el banco durante el día, las cuales se cargan al sistema mediante un archivo Multicash y se registran con un documento de clase **SB**.

El segundo corresponde a un documento de clase **KZ**, cargado por el área de Tesorería, que representa las salidas de dinero ordenadas por la compañía, tales como:

- Pagos a proveedores
- Pagos de impuestos
- Anticipos
- Pagos a transportistas

Ambos documentos deben cruzarse entre sí para poder compensarse. Cuando se presentan diferencias entre ellos, estas pueden indicar que el documento no ha sido pagado por el banco o que Tesorería aún no ha generado el documento correspondiente.

## Criterios de cruce

El documento SB registra el NIT **sin** dígito de verificación, mientras que el documento KZ lo registra **con** dígito de verificación. Por esta razón, el proceso de conciliación homologa el NIT entre ambos documentos y busca igualar las partidas a partir de tres criterios:

| Criterio | Descripción |
|----------|-------------|
| NIT | Identificador del tercero, homologado entre SB y KZ |
| Fecha | Debe ser la misma en ambos documentos |
| Valor | Debe ser el mismo en ambos documentos |

Las partidas que no coinciden bajo estos criterios se separan para ser analizadas en detalle.

---

## Explicación del caso de uso

El proceso consiste en conciliar **lo que el banco informa que pagó (SB)** con **lo que la empresa ordenó pagar (KZ)**.

1. **SB: lado del banco.** Cada día el banco reporta los movimientos reales de su cuenta. Esa información ingresa al sistema mediante el archivo Multicash.

2. **KZ: lado de la empresa.** Tesorería registra los pagos que debe realizar o ya realizó.

3. **Objetivo: compensar.** Si el banco debitó un valor *X* a un tercero y Tesorería registró un pago de *X* a ese mismo tercero el mismo día, las dos partidas se anulan entre sí y quedan conciliadas.

4. **Las partidas sin pareja son el foco del análisis.** Tienen dos lecturas posibles:
   - **Existe en KZ pero no en SB:** el pago fue registrado, pero el banco no lo ha ejecutado (documento no pagado).
   - **Existe en SB pero no en KZ:** el banco ya pagó, pero Tesorería no generó el documento.

5. **Problema técnico: el NIT.** El SB trae el NIT sin dígito de verificación (ej. `900123456`) y el KZ con él (ej. `900123456-7`). Un cruce directo fallaría aunque se trate del mismo tercero. La solución es homologar el NIT, comparando solo la parte base, y luego cruzar por **NIT + fecha + valor**.

6. **Resultado.** Las partidas que cruzan quedan conciliadas; las que no, conforman un listado de diferencias que se investiga una por una.
