# Investigación de inhibidores de quinasas

Jupyter Book con una investigación computacional sobre inhibidores de quinasas: modelo
QSAR que predice la potencia de inhibición (**pIC50**) a partir de la estructura química.

## Contenido

```
intro.md                     portada y resumen del proyecto
capitulos/
  01_introduccion_quinasas   biología de quinasas y su papel en oncología
  02_planteamiento           qué se predice y qué no se puede concluir
  03_datos                   ChEMBL, curación y criterios de inclusión
  04_eda                     análisis exploratorio
  05_representacion_moleculas fingerprints y descriptores
  06_modelado_ml             baseline, Ridge, Random Forest, XGBoost, GNN
  07_evaluacion              scaffold split, métricas, dominio de aplicabilidad
  08_interpretabilidad       SHAP y mapeo de bits a fragmentos
  09_conclusiones            resultados, limitaciones, trabajo futuro
referencias.md / .bib        bibliografía
datos/                       crudos y procesados (no se versionan)
modelos/                     modelos entrenados (no se versionan)
```

## Puesta en marcha

```powershell
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

## Compilar el libro

```powershell
jupyter-book build .
```

Abrir `_build/html/index.html`.

Los notebooks no se ejecutan durante la compilación (`execute_notebooks: "off"` en
`_config.yml`) porque dependen de datos que aún no están descargados. Al tener
`datos/procesados/` poblado, cambiar a `auto` para que el libro se construya con las
figuras reales.

## Alcance

El modelo predice una propiedad de **moléculas** (su potencia frente a una quinasa en un
ensayo bioquímico), no una propiedad de pacientes. No es una herramienta de diagnóstico.
El capítulo 2 desarrolla esta distinción y el planteamiento que se sigue.
