---
tipo: anexo
proyecto: SECOP II Boyacá — Entrega final
creado: 2026-11-16
estado: en-revision
---

# Indicadores de riesgo OCP aplicables a SECOP II

**Fuente:** `OCP_2024_Red_Flags_in_Public_Procurement.pdf` (en esta carpeta), descargado el 16-nov-2026 de <https://open-contracting.org/wp-content/uploads/2024/12/OCP2024-RedFlagProcurement-1.pdf>.

**Referencia (APA):** Salazar, C. (2024). *Red flags in public procurement: A guide to using data to detect and mitigate risks*. Open Contracting Partnership. https://www.open-contracting.org/resources/red-flags-in-public-procurement-a-guide-to-using-data-to-detect-and-mitigate-risks/

100 páginas, licencia CC BY-NC-SA 4.0. La guía lista **73 indicadores** (R001–R073) en cinco tipos: baja transparencia, baja competencia, fraude, riesgo de colusión y manipulación del proceso (*bid-rigging*). La lista completa está en la Tabla 3 (pp. 19–21) y cada indicador tiene su ficha en el anexo (definición, por qué es bandera, datos necesarios, método y fuente).

> La guía advierte que las banderas son indicadores de riesgo elevado y que por sí solas no establecen irregularidad.

## Hallazgo que ordena el proyecto

Las modificaciones del contrato **son en sí mismas banderas OCP** de la etapa de ejecución:

- **R064 — Contract has modifications** (p. 87): el contrato tiene modificaciones.
- **R069 — Contract amendments to increase price** (p. 92): modificaciones posteriores a la firma que aumentan el precio.

Es decir, lo que ya modelamos con supervivencia (tiempo hasta la primera modificación / adición en valor) es una bandera OCP de ejecución. El proyecto queda así: **¿las banderas que se ven al momento de la firma anticipan las banderas de ejecución?**

## Las tres que pidió el profesor

| Pedido del profesor | Indicador OCP | Definición de la guía | Método de la guía |
|---|---|---|---|
| Proveedor único recurrente | **R040 — High share of buyer's contracts** (p. 63) | La participación del proveedor en los contratos de un comprador es atípicamente alta | Participación = valor adjudicado al proveedor por el comprador en el periodo ÷ valor total del comprador en el periodo. Bandera si supera un umbral (ej. 40 %) o la cerca superior Q3 + 1,5·IQR |
| Contratos justo bajo umbrales | **R049 — Direct awards below threshold** (p. 72) | El proveedor recibe más de una adjudicación directa del mismo comprador en el periodo, justo por debajo del umbral competitivo | Más de 1 adjudicación directa, del mismo comprador, justo bajo el umbral. El periodo y la distancia al umbral (ej. 1 %, 2 %) se definen según el contexto |
| Fraccionamiento | **R011 — Splitting purchases to avoid procurement thresholds** (p. 34) | Lo que debía ser un solo contrato se parte en dos o más, cada uno bajo el umbral | Dos o más procesos del mismo comprador, de la **misma categoría de ítem**, en un periodo corto (ej. tres meses), y justo bajo el umbral |

Relacionados: **R002** (manipulación de umbrales, a nivel de comprador, p. 25) y **R055** (varias adjudicaciones directas al mismo proveedor cuya **suma** supera el umbral, p. 78; está implementado en Cardinal).

### Cómo quedaron medidas (notebook, sección 13.4)

Cada indicador se calcula con contratos firmados **antes** del contrato evaluado, de dos formas: la regla de la guía y la regla aplicada en el modelo. La columna de adición es el porcentaje de contratos con adición en valor dentro de los 365 días siguientes a la firma, con y sin la bandera (contratos firmados hasta el 31-oct-2025).

| Indicador | Columnas de SECOP II | Regla | Contratos marcados (toda la base) | Adición con / sin |
|---|---|---|---|---|
| R040 guía | entidad, documento del proveedor, presupuesto, fecha de firma | Proveedor con más del 40 % del valor de la entidad en los 12 meses previos | 790 | 21,1 % / 11,6 % |
| R040 aplicada | igual | Participación continua (`share_prov`); en la tabla, corte de 10 % | 2.311 con seguimiento | 22,0 % / 11,4 % |
| R049 guía | modalidad, presupuesto, salario mínimo del año, entidad, proveedor | Mínima cuantía entre 95 % y 100 % del tope, repetida con el mismo proveedor en 12 meses | 1.288 | 22,7 % / 13,6 % |
| R049 aplicada | modalidad, presupuesto, salario mínimo del año | Mínima cuantía entre 95 % y 100 % del tope | 3.672 | 18,3 % / 13,1 % |
| R011 guía | entidad, proveedor, código UNSPSC (familia, 4 dígitos), presupuesto, fecha | Otra mínima cuantía de la misma familia al mismo proveedor en 90 días; cada una bajo el tope y la suma por encima | 980 | 19,2 % / 13,9 % |
| R011 aplicada | igual, sin código UNSPSC | Igual, sin exigir la categoría | 3.378 | 14,8 % / 14,1 % (p = 0,38) |

- La regla estricta de la guía **aumenta la asociación** en los tres casos. En fraccionamiento cambia el resultado: sin exigir categoría no había diferencia.
- **Ninguna versión mejora la predicción fuera de tiempo:** AUC 0,658 con las banderas de la guía, 0,659 con las aplicadas y 0,665 con la base (sección 14.1).
- Cerca Q3 + 1,5·IQR de la participación del proveedor: 1,1 %. Es demasiado baja para usarla como alerta, por eso R040 entra como variable continua.

## Otros indicadores construibles con la base actual

| Indicador | Qué mide | Columnas de SECOP II | Observación |
|---|---|---|---|
| **R013 — High use of non-competitive methods** (p. 36) | Proporción de procesos no competitivos del comprador, atípicamente alta | `modalidad_de_contratacion`, `nit_entidad` | Es por entidad. Umbral fijo (ej. 30 %) o Q3 + 1,5·IQR entre entidades. En Boyacá la directa es ~69 %, así que el umbral fijo no sirve: usar el atípico |
| **R010 — Unjustified use of non competitive procedure** (p. 33) | El uso del método no competitivo no está bien justificado | `justificacion_modalidad_de` | La columna existe y no tiene vacíos (23 categorías). Solo se puede marcar la justificación genérica (ej. "Regla aplicable"); verificar legalidad caso a caso no es posible con datos |
| **R048 — Heterogeneous supplier** (p. 71) | Proveedor que vende categorías muy distintas | `codigo_de_categoria_principal` (UNSPSC), `documento_proveedor` | Número de segmentos UNSPSC distintos (primeros 2 dígitos) por proveedor; bandera sobre Q3 + 1,5·IQR. Está en Cardinal |
| **R052 — Small initial purchase followed by much larger purchases** (p. 75) | Primer contrato pequeño y segundo mucho mayor, mismo comprador | `valor`, `fecha_firma`, proveedor, entidad | (valor₂ − valor₁) ÷ valor₁ sobre un umbral (ej. 30 %) o el atípico |
| **R054 — Direct award followed by change orders that exceed the competitive threshold** (p. 77) | Directa cuyas adiciones la llevan sobre el umbral | Requiere el **monto** de la adición | El dataset de Adiciones no trae montos. Solo aproximable comparando `valor_del_contrato` entre la descarga de la Entrega 1 y la actual |

## No construibles con la base de contratos

- **Necesitan datos de ofertas** (dataset de procesos `p6dx-8zbt`, no descargado): R018 oferta única, R019 pocos oferentes, R022–R028 precios de ofertas, R035–R038 descalificaciones.
- **Necesitan datos de planeación o de plazos del proceso:** R001, R003, R012, R014, R015, R060–R062.
- **Necesitan fuentes externas:** R045–R047 (registros mercantiles, sancionados), R032–R033 (beneficiarios finales).
- **R059** (diferencia entre adjudicación y contrato): la base solo trae el valor del contrato.

## Decisiones por tomar antes de programar

1. **El umbral.** Todos los indicadores de umbral piden "los umbrales competitivos según la regulación local". En Colombia la mínima cuantía es el 10 % de la menor cuantía, y la menor cuantía depende del presupuesto anual de cada entidad en salarios mínimos (Ley 1150 de 2007, art. 2). El presupuesto no está en la base. Opciones:
	- (a) Aproximar el tope por entidad y año con el valor máximo observado en contratos de modalidad "Mínima cuantía".
	- (b) Usar la justificación: `justificacion_modalidad_de` trae "Presupuesto inferior al 10% de la menor cuantía", que identifica los contratos que la propia entidad declaró bajo el tope.
	- (c) Combinar: (b) para identificar y (a) para medir la distancia al tope.
2. **Proveedor por documento, no por nombre.** `documento_proveedor` no tiene vacíos, pero trae "No Definido" y la mayoría son cédulas (personas naturales en prestación de servicios), no NIT. Decidir si R040 se calcula para todos o solo para personas jurídicas.
3. **Ventana de tiempo.** La guía sugiere tres meses para R011. Para R040 propongo los 12 meses anteriores a la firma.
4. **Sin fuga.** La guía calcula por periodo completo; para usarlos como predictores hay que calcularlos solo con contratos firmados **antes** del contrato evaluado. Es una adaptación nuestra y hay que decirlo en el escrito.
5. **Categoría de ítem para R011.** `codigo_de_categoria_principal` (UNSPSC): decidir el nivel (clase, 6 dígitos, o familia, 4 dígitos).

> [!warning] Actualización del 16-nov (tarde)
> Las cifras de la sección siguiente se calcularon con `valor_del_contrato`, que resultó ser el valor **vigente** (incluye adiciones). Las definitivas, con el presupuesto del proceso, están en la sección 13 del notebook:
> - Justo bajo el tope: 3.672 contratos (20,3 % de la mínima cuantía), no 6,9 %.
> - Tramo 26–28 SMMLV: 3.237 contratos; 28–30: 146.
> - Topes inferidos: 179 entidades en 28 SMMLV, 4 en 45, 3 en 65, 1 en 85 y 9 en 100.
> - Fraccionamiento: 3.378 contratos (18,7 % de la mínima cuantía).
> - **El pico de 41–42 SMMLV quedó explicado:** 42 = 28 × 1,5, contratos en el tope con la adición máxima (50 %). El 23,0 % de los presupuestados en el tope lo supera tras la adición (indicador R054).
> - Se agregó R018 (oferta única), que sí se puede construir con el dataset de procesos.
> - Conjunto final de predictores y su aporte: secciones 14 y 18 del notebook.

## Resultados de la revisión del 16-nov (puntos 1 y 2)

Exploración sobre `Datos/base_analitica_boyaca.csv.gz` (141.742 contratos). Todavía no está en el notebook; va como sección 11.

### Punto 1 — El mapeo necesita una adaptación a Colombia

R049 y R011 hablan de **adjudicaciones directas** bajo el umbral. En Colombia la contratación directa no depende del valor sino de una causal (servicios profesionales, interadministrativos, etc.), así que no tiene tope que esquivar. La modalidad que sí depende del valor es la **mínima cuantía**: quedar bajo su tope evita la selección abreviada o la licitación. Por eso R049 y R011 se aplican sobre contratos de **mínima cuantía**, no sobre los de directa. Es una adaptación nuestra y hay que declararla.

La base lo confirma: los 18.444 contratos con justificación "Presupuesto inferior al 10% de la menor cuantía" son exactamente los de modalidad "Mínima cuantía" (salvo 1). La justificación no añade información; basta la modalidad.

| Indicador | Adaptación | Prevalencia encontrada |
|---|---|---|
| **R040** proveedor recurrente | Participación del proveedor en el valor contratado por la entidad | Mediana 0,07 %; percentil 99 = 8,4 %; solo 49 pares proveedor–entidad–año sobre el 40 % que sugiere la guía y 755 sobre 10 %. La cerca Q3 + 1,5·IQR da 0,67 %, demasiado baja para servir. **Usarla como variable continua**, no como bandera con corte |
| **R049** justo bajo el tope | Mínima cuantía con valor entre 95 % y 100 % del tope de la entidad | 1.279 contratos, **6,9 %** de la mínima cuantía |
| **R011 / R055** fraccionamiento | Mínima cuantía con otro contrato de mínima cuantía de la misma entidad y proveedor en los 90 días anteriores, cada uno bajo el tope y cuya suma lo supera | 4.699 contratos (25,5 %) tienen otro en la ventana; en **2.604 (14,2 %)** la suma supera el tope. Aún sin exigir misma categoría UNSPSC, que pide R011 |

El 73,9 % de los proveedores se identifica con cédula y el 25,5 % con NIT.

### Punto 2 — El umbral sí se puede reconstruir

La Ley 1150 de 2007 (art. 2) fija la menor cuantía en cinco niveles según el presupuesto de la entidad: 280, 450, 650, 850 o 1.000 salarios mínimos. La mínima cuantía es el 10 %: **28, 45, 65, 85 o 100 SMMLV**. Solo hay cinco topes posibles por año, así que no hace falta el presupuesto: se infiere el nivel de cada entidad.

**Evidencia de agrupamiento bajo el tope** (mínima cuantía, valor en SMMLV del año de firma):

| Tramo (SMMLV) | Contratos |
|---|---|
| 24–26 | 779 |
| **26–28** | **2.319** |
| 28–30 | 232 |
| 96–98 | 44 |
| **98–100** | **198** |
| 100–102 | 5 |

Hay tres veces más contratos justo debajo de 28 SMMLV que en el tramo anterior, y diez veces más que en el siguiente. El patrón se repite todos los años desde 2022 y los valores más frecuentes coinciden con 28 SMMLV exactos: $36,4 millones en 2024, $39,9 en 2025 y $49,0 en 2026. Eso también valida la tabla de salarios mínimos usada.

**Regla propuesta (opción c, refinada):** tope de la entidad = el menor de los cinco niveles legales que cubre el percentil 95 de sus contratos de mínima cuantía. Resultado sobre 197 entidades: 124 en 28 SMMLV, 56 en 45, 6 en 65, 1 en 85 y 10 en 100. Solo el 1,9 % de los contratos queda por encima del tope inferido.

**Cabos sueltos**
- Hay un pico secundario en **41–42 SMMLV** (460 contratos) que no corresponde a ningún nivel de la ley. Las entidades que lo tienen quedan clasificadas en el nivel 45. Falta explicarlo antes de citarlo.
- 161 de las 358 entidades no tienen contratos de mínima cuantía: para ellas no hay tope inferido y las banderas de umbral valen cero.
- Las entidades de régimen especial no se rigen por estos topes.
- El nivel se infiere una vez por entidad; en rigor puede cambiar de un año a otro con el presupuesto.
- En selección abreviada de menor cuantía (2.610 contratos) también hay agrupamiento bajo 280 SMMLV (108 en 270–280 frente a 22 en 280–285), pero son pocos; queda como extensión.
- Salarios mínimos usados: 2017 $737.717 · 2018 $781.242 · 2019 $828.116 · 2020 $877.803 · 2021 $908.526 · 2022 $1.000.000 · 2023 $1.160.000 · 2024 $1.300.000 · 2025 $1.423.500 · 2026 $1.750.905. Citar los decretos en el escrito.

## Propuesta de conjunto final

Predictores (al momento de la firma): **R040, R049, R011, R013, R048, R052**, más R010 si la justificación genérica resulta informativa.
Resultado (ejecución): **R064 / R069**, ya construido en las secciones 7–10 del notebook.

## Pendiente de revisar juntos
- [x] Validar el mapeo de las tres banderas del profesor (R040, R049, R011) → se sostiene, aplicando R049 y R011 sobre mínima cuantía
- [x] Escoger la opción de umbral → cinco niveles legales en SMMLV, con el nivel inferido por entidad
- [x] Explicar el pico de 41–42 SMMLV → 42 = 28 × 1,5 (adición máxima sobre el tope)
- [x] Pasar esta exploración al notebook y cruzarla con el evento de adición en valor → secciones 13 y 14
- [x] Confirmar el conjunto final de predictores → selección por AIC, sección 14.1
- [ ] Leer las secciones 1–3 de la guía (definición, datos, implementación paso a paso; pp. 3–18) para la metodología del escrito
