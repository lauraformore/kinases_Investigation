# Planteamiento del problema

Este capítulo fija qué se predice, con qué datos, y qué conclusiones se pueden y no se pueden extraer. Es el capítulo que sostiene la validez científica de todo lo demás.

## La pregunta original y su ajuste

El punto de partida fue: *predecir el número de pK en las quinasas para saber la probabilidad de que una persona tenga cáncer o no*.

Hay dos preguntas distintas mezcladas ahí, y conviene separarlas porque necesitan datos diferentes:

**Pregunta A — ¿Cuánto inhibe esta molécula a esta quinasa?**
La respuesta es un valor de pK (pIC50, pKi, pKd). Es una propiedad **de la molécula** medida en un ensayo bioquímico. Los datos existen, son públicos y abundantes (ChEMBL, BindingDB, PKIS). Es un problema de **regresión** bien definido.

**Pregunta B — ¿Tiene esta persona cáncer?**
La respuesta es un diagnóstico. Es una propiedad **del paciente**. Necesitaría datos clínicos: histopatología, imagen, biomarcadores en sangre, perfiles de expresión génica tumoral. Sería un problema de **clasificación** sobre pacientes, no sobre moléculas.

El pK de un compuesto no informa sobre el diagnóstico de nadie. Un pIC50 de 9 significa que la molécula bloquea bien la enzima en un tubo de ensayo. Entre eso y el estado clínico de una persona hay una cadena larga:

```
potencia in vitro (pIC50)
   → actividad en célula tumoral (GI50)
      → farmacocinética y exposición en tejido
         → eficacia en modelo animal
            → eficacia en ensayo clínico
               → beneficio en un paciente concreto
```

Cada flecha es un salto con su propia tasa de fallo. Alrededor del 90 % de los candidatos oncológicos que entran en fase I no llegan a aprobación. Un modelo de pIC50 vive en el primer eslabón.

```{warning}
Un modelo entrenado con pIC50 de compuestos **no puede** usarse para diagnóstico.
No tiene pacientes en los datos de entrenamiento, así que no hay nada en él que
pueda aprender sobre pacientes. Afirmar lo contrario sería un error de validez
de constructo, no un problema de precisión del modelo.
```

## La pregunta que sí se va a responder

> **Dada la estructura química de una molécula, ¿cuál es su pIC50 frente a una quinasa diana concreta?**

Es un proyecto de machine learning completo y riguroso, conecta directamente con oncología (las quinasas elegidas son dianas oncológicas reales), y es exactamente el tipo de modelo que se usa en descubrimiento de fármacos para priorizar qué compuestos sintetizar.

Formalmente:

- **Entrada** $x$: estructura molecular (SMILES), transformada en fingerprints y descriptores.
- **Salida** $y$: pIC50 (escala continua, típicamente entre 4 y 11).
- **Tarea**: regresión supervisada.
- **Objetivo real**: que el modelo generalice a **scaffolds químicos no vistos**, no solo a análogos cercanos de lo que ya conoce.

## Extensiones posibles

Si el proyecto crece, hay dos variantes naturales que mantienen el rigor:

1. **Clasificación activo/inactivo** con un umbral justificado (p. ej. pIC50 ≥ 7, es decir IC50 ≤ 100 nM). Útil cuando interesa cribado virtual más que el valor exacto.
2. **Modelo multi-diana (selectividad)** — predecir el perfil de un compuesto contra varias quinasas a la vez, y derivar un índice de selectividad. Es más ambicioso y científicamente más interesante.

Lo que **no** se va a hacer es saltar a predicción clínica, porque los datos del proyecto no lo permiten.

## Criterios de éxito

El modelo se considerará bueno si, **en el conjunto de test separado por scaffold**:

| Métrica | Umbral razonable | Comentario |
|---|---|---|
| RMSE | < 0,8 unidades log | El error experimental entre laboratorios ya es ~0,3–0,5 |
| $R^2$ | > 0,5 | En scaffold split, valores de 0,5–0,7 son buenos |
| Spearman ρ | > 0,7 | Importa el orden: priorizar compuestos |

```{important}
Un $R^2$ de 0,9 en una partición aleatoria suele ser una ilusión: los análogos
del mismo scaffold caen a ambos lados de la partición y el modelo solo tiene que
interpolar. La partición por scaffold es la que mide si el modelo sirve para algo.
```

## Riesgos del planteamiento

- **Sesgo de publicación** — ChEMBL contiene sobre todo compuestos activos; los inactivos se publican menos. El modelo ve un espacio químico desequilibrado.
- **Heterogeneidad de ensayos** — un IC50 depende de la concentración de ATP, del formato del ensayo y del laboratorio. Mezclar fuentes añade ruido irreducible.
- **Series congenéricas** — muchos datos vienen de series de análogos de un mismo artículo. Esto infla las métricas si no se controla con scaffold split.
- **Dominio de aplicabilidad** — el modelo solo es fiable para moléculas parecidas a las de entrenamiento. Hay que declararlo explícitamente, no esconderlo.

## Decisión de diana

Se recomienda empezar con **una sola quinasa bien poblada en ChEMBL**, para evitar mezclar ensayos incomparables. Candidatas:

| Diana | ChEMBL ID | Compuestos con IC50 (orden) | Relevancia |
|---|---|---|---|
| EGFR | CHEMBL203 | ~10 000 | Pulmón, colorrectal |
| ABL1 | CHEMBL1862 | ~3 000 | Leucemia mieloide crónica |
| CDK2 | CHEMBL301 | ~5 000 | Ciclo celular |
| BRAF | CHEMBL5145 | ~3 000 | Melanoma |
| VEGFR2 (KDR) | CHEMBL279 | ~8 000 | Angiogénesis |

La elección se cierra en el [capítulo 3](03_datos).
