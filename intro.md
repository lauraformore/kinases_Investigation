# Investigación de inhibidores de quinasas

Este libro documenta un proyecto de investigación computacional sobre **inhibidores de quinasas**: desde la revisión del contexto biológico hasta la construcción y evaluación de un modelo de aprendizaje automático.

## Qué se pretende

El objetivo central es construir un modelo **QSAR** (Quantitative Structure–Activity Relationship) que, a partir de la estructura química de una molécula, prediga su **potencia de inhibición** sobre una quinasa concreta, expresada como valor **pK** (pIC50 o pKi).

```{note}
**pK** es el logaritmo negativo de la concentración que produce el efecto:
`pIC50 = -log10(IC50 en molar)`. Un pIC50 de 9 equivale a IC50 = 1 nM (muy potente);
un pIC50 de 5 equivale a 10 µM (débil). Se trabaja en escala logarítmica porque
las concentraciones abarcan varios órdenes de magnitud.
```

## Un ajuste importante en el planteamiento

La idea inicial del proyecto era predecir el valor de pK «para saber la probabilidad de que una persona tenga cáncer o no». Conviene separar dos preguntas que son distintas:

| Pregunta | Qué se mide | Tipo de dato necesario |
|---|---|---|
| ¿Cuánto inhibe esta molécula a esta quinasa? | Potencia del compuesto (pIC50/pKi) | Ensayos bioquímicos de compuestos (ChEMBL) |
| ¿Tiene esta persona cáncer? | Estado clínico de un paciente | Datos clínicos, histología, imagen, ómicas de pacientes |

El valor de pK describe una propiedad de **la molécula**, no del paciente. Un pIC50 alto significa que el compuesto bloquea bien la enzima en el tubo de ensayo; no dice nada sobre el diagnóstico de nadie. Son dos cadenas causales muy separadas: potencia in vitro → actividad celular → eficacia en modelo animal → eficacia clínica → beneficio en un paciente concreto.

El [capítulo 2](capitulos/02_planteamiento) desarrolla esto y propone el planteamiento que sí se sostiene con datos públicos: **regresión de pIC50 sobre inhibidores de quinasas**, con validación honesta y modelo interpretable. Es un proyecto de machine learning completo y defendible, y encaja con el interés original en quinasas y cáncer, porque las quinasas son dianas oncológicas de primera línea.

## Estructura del libro

1. **Introducción a las quinasas** — biología, familias, papel en oncología.
2. **Planteamiento** — qué se predice, por qué, y qué no se puede concluir.
3. **Datos** — origen (ChEMBL), curación, criterios de inclusión.
4. **Análisis exploratorio** — distribuciones, espacio químico, sesgos.
5. **Representación molecular** — fingerprints, descriptores.
6. **Modelado** — baseline, Random Forest, gradient boosting, redes de grafos.
7. **Evaluación** — scaffold split, métricas, aplicabilidad del modelo.
8. **Interpretabilidad** — SHAP, qué fragmentos empujan la predicción.
9. **Conclusiones** — qué se aprendió y qué quedaría por hacer.

## Cómo compilar

```powershell
jupyter-book build .
```

El resultado queda en `_build/html/index.html`.
