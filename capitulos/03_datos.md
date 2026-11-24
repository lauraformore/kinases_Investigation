# Datos

## Fuente

Los datos de actividad vienen de **ChEMBL** (versión 35 o posterior), base de datos de bioactividad curada manualmente a partir de literatura primaria por el EMBL-EBI. Es la fuente estándar para QSAR y es de acceso libre.

Alternativas y complementos:

- **BindingDB** — afinidades de unión, solapamiento parcial con ChEMBL.
- **PKIS / PKIS2** (GSK) — paneles de quinasas medidos en condiciones homogéneas; mucho menos ruido entre ensayos.
- **Davis et al. (2011)** y **Metz et al. (2011)** — matrices compuesto × quinasa, útiles para modelos multi-diana.
- **DrugBank / FDA** — anotación de fármacos aprobados, para contextualizar.

## Acceso

```python
from chembl_webresource_client.new_client import new_client

target = new_client.target
activity = new_client.activity

# Buscar la diana
res = target.filter(pref_name__icontains="Epidermal growth factor receptor")

# Descargar actividades
acts = activity.filter(
    target_chembl_id="CHEMBL203",
    standard_type="IC50",
).only([
    "molecule_chembl_id", "canonical_smiles",
    "standard_value", "standard_units", "standard_relation",
    "assay_chembl_id", "assay_type", "document_chembl_id",
    "pchembl_value", "data_validity_comment",
])
```

El resultado se guarda crudo en `datos/crudos/` y **no se modifica nunca**. Toda transformación produce ficheros nuevos en `datos/procesados/`.

## Curación

El filtrado es la parte del proyecto que más determina la calidad del modelo. Pasos, en orden:

### 1. Filtrado de registros

| Criterio | Acción | Motivo |
|---|---|---|
| `standard_units != 'nM'` | descartar | unidades no comparables |
| `standard_relation != '='` | descartar | `>` o `<` son censurados; se pierden pero no se pueden usar en regresión directa |
| `data_validity_comment` no nulo | descartar | ChEMBL marca valores sospechosos |
| `assay_type != 'B'` | descartar | se quieren ensayos bioquímicos, no celulares ni funcionales |
| `standard_value` nulo o ≤ 0 | descartar | imposible tomar logaritmo |
| `pchembl_value` fuera de [3, 12] | revisar | valores extremos suelen ser errores de transcripción |

### 2. Normalización de estructuras

Con RDKit:

- Parsear SMILES; descartar los que no parsean.
- **Desalar**: quedarse con el fragmento mayor (`SaltRemover` o selección del fragmento con más átomos pesados).
- **Neutralizar** cargas formales donde sea posible.
- **Estandarizar** tautómeros y grupos funcionales (`rdMolStandardize`).
- Generar **SMILES canónico** para detectar duplicados.
- Descartar moléculas con elementos fuera de `{C,N,O,S,P,F,Cl,Br,I,H}` (compuestos organometálicos no se modelan bien con fingerprints estándar).
- Descartar fuera del rango de peso molecular 150–900 Da.

### 3. Agregación de duplicados

Un mismo compuesto aparece a menudo medido varias veces. Regla:

- Convertir cada medida a pIC50.
- Agrupar por SMILES canónico.
- Si el rango intra-compuesto es **≤ 1 unidad log**, tomar la **mediana**.
- Si el rango es **> 1 unidad log**, descartar el compuesto: las medidas son incompatibles y no se sabe cuál creer.

```{note}
Conviene registrar cuántos compuestos se descartan por esta regla. Si es un
porcentaje alto, indica que se están mezclando ensayos muy distintos y habría
que restringir por `assay_chembl_id`.
```

### 4. Conversión a pIC50

$$\text{pIC50} = 9 - \log_{10}(\text{IC50 en nM})$$

(equivalente a $-\log_{10}$ de la concentración en molar)

## Tabla final

El dataset de modelado tiene una fila por compuesto:

| Columna | Tipo | Descripción |
|---|---|---|
| `chembl_id` | str | identificador del compuesto |
| `smiles` | str | SMILES canónico, estandarizado |
| `pic50` | float | variable objetivo |
| `n_medidas` | int | cuántas medidas se agregaron |
| `rango_log` | float | dispersión intra-compuesto |
| `scaffold` | str | esqueleto de Bemis–Murcko, para la partición |
| `mw`, `logp`, `tpsa`, `hbd`, `hba`, `rotb` | float | descriptores fisicoquímicos básicos |

Se guarda en `datos/procesados/dataset_modelado.parquet`.

## Trazabilidad

Cada paso del pipeline deja constancia del número de filas antes y después. Al final del capítulo se incluye una tabla de atrición:

```
registros descargados       38 412
tras filtro de unidades     31 207
tras filtro de relación     27 845
tras filtro de validez      27 611
tras assay_type = B         19 433
SMILES parseables           19 390
tras estandarización        18 902
compuestos únicos            8 744
tras agregación coherente    8 311   ← dataset final
```

Sin esta tabla no es posible auditar el estudio ni reproducirlo.

## Reproducibilidad

- La versión de ChEMBL se fija y se anota (los datos cambian entre releases).
- Los ficheros crudos se versionan por fecha de descarga.
- Las semillas aleatorias se fijan en todas las particiones y modelos.
- Las dependencias se congelan en `requirements.txt` o `environment.yml`.
