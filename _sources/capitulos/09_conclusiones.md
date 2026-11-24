# Conclusiones

```{note}
Capítulo para rellenar al cerrar el trabajo. Abajo queda la estructura y las preguntas
que debe responder, para que no se escriba en abstracto.
```

## Qué se construyó

Un modelo QSAR de regresión que predice la potencia de inhibición (pIC50) de moléculas
pequeñas frente a *[diana]*, entrenado con *[n]* compuestos curados de ChEMBL *[versión]*.

| Elemento | Decisión tomada |
|---|---|
| Diana | |
| Compuestos tras curación | |
| Representación | |
| Modelo final | |
| Partición | scaffold split de Bemis–Murcko (70/15/15) |

## Resultados

| Métrica (test, scaffold split) | Valor | Umbral fijado en cap. 2 |
|---|---|---|
| RMSE | | < 0,8 |
| $R^2$ | | > 0,5 |
| Spearman ρ | | > 0,7 |

Comparación con partición aleatoria: *[diferencia]*. La brecha cuantifica el optimismo de
la validación aleatoria.

## Qué se aprendió

Puntos a desarrollar con lo observado:

- **Sobre el problema** — cuánta señal hay realmente en la estructura 2D para predecir
  potencia, y dónde está el techo impuesto por el ruido experimental.
- **Sobre la representación** — si los fingerprints bastaron o si los descriptores
  fisicoquímicos aportaron, y en qué medida.
- **Sobre el modelo** — si la complejidad extra (boosting, GNN) se pagó en rendimiento
  real o solo en la partición aleatoria.
- **Sobre la química** — qué fragmentos destacó SHAP y si coinciden con farmacóforos
  conocidos de inhibidores de quinasas.

## Limitaciones

Conviene ser explícito, porque es lo que distingue un trabajo serio:

1. **Dominio de aplicabilidad estrecho.** El modelo solo es fiable para moléculas con
   Tanimoto > *[umbral]* al conjunto de entrenamiento. Fuera de ahí el RMSE sube a
   *[valor]*.
2. **Una sola diana.** No dice nada sobre selectividad ni sobre el perfil del compuesto
   contra el resto del quinoma.
3. **Ruido de ensayo.** Los datos mezclan laboratorios y condiciones; parte del error es
   irreducible.
4. **Sesgo de publicación.** ChEMBL sobre-representa compuestos activos; el espacio de
   inactivos está mal cubierto.
5. **Regresión a la media.** El modelo comprime el rango y tiende a subestimar los
   compuestos más potentes, que son justamente los de interés.
6. **Solo estructura 2D.** No hay conformación, no hay información del sitio de unión, no
   hay dinámica.

## Lo que el modelo no hace

```{warning}
Este modelo predice una propiedad de **moléculas**, no de **personas**. No es, ni puede
convertirse en, una herramienta de diagnóstico, cribado o pronóstico de cáncer. No hay
pacientes en los datos de entrenamiento, así que no hay nada que pueda haber aprendido
sobre pacientes. Cualquier uso clínico requeriría un planteamiento distinto, datos
distintos y validación regulatoria.
```

Dónde sí encaja: en la fase temprana de descubrimiento de fármacos, para **priorizar qué
compuestos sintetizar y ensayar** antes de gastar tiempo de laboratorio. Ese es un uso
real y valioso.

## Trabajo futuro

Por orden de coste/beneficio:

1. **Ampliar a varias dianas** y derivar un índice de selectividad. Es la extensión más
   interesante científicamente y aprovecha todo lo ya construido.
2. **Clasificación activo/inactivo** con umbral justificado, para cribado virtual de
   librerías grandes (ZINC, Enamine REAL).
3. **Modelos de incertidumbre** — conformal prediction o ensembles profundos, para
   acompañar cada predicción de un intervalo en vez de un número suelto.
4. **Incorporar estructura 3D** — docking, descriptores del complejo, o modelos
   estructura-dependientes si hay cristales en PDB para la diana.
5. **Aprendizaje multitarea** — entrenar simultáneamente sobre varias quinasas suele
   mejorar las dianas con pocos datos.
6. **Validación prospectiva** — predecir compuestos de una publicación posterior a la
   versión de ChEMBL usada. Es la prueba más honesta que existe y es gratis.

## Reproducibilidad

- Código y notebooks: este repositorio.
- Versión de ChEMBL: *[anotar]*.
- Dependencias: `requirements.txt`.
- Semillas: `RANDOM_STATE = 42` en todos los notebooks.
- Para reconstruir todo: `jupyter-book build .` con `execute_notebooks: auto`.
