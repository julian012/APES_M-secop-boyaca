# Veeduría de datos: banderas de riesgo y adiciones en la contratación pública de Boyacá (SECOP II)

Proyecto final del curso **Aprendizaje Estadístico (ISIS_APES_M)**, Maestría en Informática, Escuela Colombiana de Ingeniería Julio Garavito, 2026-2.

**Autor:** Julián Serna · julian.serna-v@mail.escuelaing.edu.co
**Profesor:** Jairo Castrellón

## Pregunta

Boyacá tiene más de 140.000 contratos en SECOP II y ninguna veeduría puede revisarlos todos. El proyecto busca saber si lo que se conoce **al firmar un contrato** permite anticipar cuáles terminarán con una **adición en valor** durante el año siguiente, para decidir qué revisar primero.

## Resultados principales

| | Resultado |
|---|---|
| Fuga de información | El valor del contrato publicado ya incluye las adiciones. Se reemplazó por el presupuesto del proceso. |
| Mínima cuantía | 3.237 presupuestos entre 26 y 28 salarios mínimos y solo 146 entre 28 y 30: se agrupan justo bajo el tope legal. |
| Mejor señal | La historia de adiciones de la entidad y del proveedor. Entidades con más historia: 25,6 % de contratos adicionados; con menos: 1,5 %. |
| Modelo final | Regresión logística de 15 variables, probada con contratos de 2024–2025: AUC 0,74. Revisando el 8,5 % con mayor puntaje, 3 de cada 10 tienen adición (1 de cada 10 al azar). |
| Supervivencia | Modelo de Cox con variables limpias: C-index 0,81 fuera de tiempo. |
| Ley de Garantías | En 2026 la contratación directa pasa de 86 % a 9 % durante la restricción, después de adelantarse a enero. |

**Límites.** El modelo prioriza la revisión, no prueba irregularidades. La Gobernación de Boyacá concentra la mitad de las adiciones del periodo de prueba; sin ella el desempeño cambia (ver sección 14 del notebook). Las probabilidades están sobreestimadas porque la tasa de adición bajó entre entrenamiento y prueba.

## Contenido

| Archivo | Qué es |
|---|---|
| [`Serna_Julian_Entrega2.ipynb`](Serna_Julian_Entrega2.ipynb) | Notebook con todo el análisis y sus salidas |
| [`Serna_Julian_Trabajo_Escrito_Ent.pdf`](Serna_Julian_Trabajo_Escrito_Ent.pdf) | Trabajo escrito (APA 7) |
| [`Serna_Julian_Poster.pdf`](Serna_Julian_Poster.pdf) | Póster (A1) |
| [`Figuras/`](Figuras/) | Figuras generadas por el notebook |
| [`Anexos/Indicadores_OCP_aplicables.md`](Anexos/Indicadores_OCP_aplicables.md) | Indicadores de riesgo de la guía de Open Contracting Partnership que se pueden calcular con SECOP II, y cómo quedaron medidos |
| [`Anexos/OCP_2024_Red_Flags_in_Public_Procurement.pdf`](Anexos/OCP_2024_Red_Flags_in_Public_Procurement.pdf) | Guía de Open Contracting Partnership (2024) usada como marco |
| [`Anexos/Leyes/`](Anexos/Leyes/) | Leyes 80 de 1993, 996 de 2005 y 1150 de 2007 (Gestor Normativo de Función Pública) |
| `Datos/` | Datos descargados de la fuente (ver abajo) |

### Secciones del notebook

1–10 descarga, calidad de datos, descripción y primeros modelos · 11 prueba de fuga del valor · 12 presupuesto inicial desde procesos · 13 indicadores de riesgo OCP (13.4: los tres indicadores de la retroalimentación) · 14 regresión logística validada fuera de tiempo · 15 modelo de Cox (15.1: cuándo llegan las adiciones) · 16 regresión bayesiana y residuo como alerta · 17 Ley de Garantías · 18 conclusiones.

## Datos

Fuente: [datos.gov.co](https://www.datos.gov.co), departamento de Boyacá.

| Conjunto | Identificador | Carpeta |
|---|---|---|
| SECOP II – Contratos electrónicos | `jbjy-vk9h` | `Datos/raw_por_anio/` |
| SECOP II – Procesos de contratación | `p6dx-8zbt` | `Datos/procesos_boyaca.csv.gz` |
| SECOP II – Adiciones | `cb9c-h8sn` | `Datos/eventos/` |
| SECOP II – Suspensiones | `u99c-7mfm` | `Datos/eventos/` |
| Muestra de la primera entrega (20-ago-2026) | `jbjy-vk9h` | `Datos/entrega1/` |
| Base analítica (contratos limpios, derivada de los anteriores) | — | `Datos/base_analitica_boyaca.csv.gz` |

Los archivos incluidos son la descarga con la que se obtuvieron los resultados (corte de octubre de 2026). La fuente se actualiza a diario: si se borran, el notebook los vuelve a descargar, pero las cifras pueden cambiar.

## Cómo ejecutarlo

```bash
python3.9 -m venv .venv
source .venv/bin/activate          # en Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter notebook Serna_Julian_Entrega2.ipynb
```

En VS Code, después de crear el entorno, se elige `.venv` como kernel del notebook.

Ejecutar desde la carpeta del repositorio. Se probó con Python 3.9. Algunas celdas consultan la API para contar registros, así que se necesita conexión a internet.

## Referencia principal

Salazar, C. (2024). *Red flags in public procurement: A guide to using data to detect and mitigate risks*. Open Contracting Partnership. https://www.open-contracting.org/resources/red-flags-in-public-procurement-a-guide-to-using-data-to-detect-and-mitigate-risks/

Las demás referencias están en el trabajo escrito.

## Licencia

- **Código** (notebook): [MIT](LICENSE).
- **Trabajo escrito, póster y figuras**: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/deed.es). Se pueden reutilizar citando al autor.
- **Datos** (`Datos/`): pertenecen a Colombia Compra Eficiente y se publican en datos.gov.co. Conservan los términos de uso de esa fuente; este repositorio no les asigna licencia propia.
- **Guía de Open Contracting Partnership** (`Anexos/OCP_2024_Red_Flags_in_Public_Procurement.pdf`): © Open Contracting Partnership, licencia [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es). Se incluye sin modificaciones y con fines académicos.
- **Leyes** (`Anexos/Leyes/`): textos oficiales descargados del Gestor Normativo de Función Pública.
