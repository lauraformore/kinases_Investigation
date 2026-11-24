# Introducción a las quinasas

## Qué son

Las **proteínas quinasas** son enzimas que transfieren un grupo fosfato desde el ATP a un residuo de otra proteína (serina, treonina o tirosina). Esa fosforilación actúa como un interruptor: cambia la conformación, la actividad o la localización de la proteína diana. Es el mecanismo básico de transmisión de señales dentro de la célula.

El genoma humano codifica unas **518 quinasas**, el llamado *quinoma*. Se agrupan en familias según la similitud de su dominio catalítico:

| Grupo | Ejemplos | Función predominante |
|---|---|---|
| TK (tirosina quinasas) | EGFR, ABL, VEGFR, ALK | Receptores de factores de crecimiento |
| TKL | BRAF, RAF1, ALK5 | Cascada MAPK, TGF-β |
| STE | MEK1/2, PAK | Módulos de señalización MAPK |
| CMGC | CDK1-9, GSK3, MAPK/ERK | Ciclo celular, respuesta a estrés |
| AGC | AKT, PKA, PKC, S6K | Metabolismo, supervivencia |
| CAMK | CAMK2, AMPK | Señalización por calcio y energía |
| CK1 | CK1α/δ/ε | Ritmo circadiano, Wnt |

## Por qué importan en cáncer

En una célula sana, las vías de señalización mediadas por quinasas están reguladas: una señal externa activa un receptor, el receptor activa una cascada, y la cascada se apaga. En muchos tumores ese control se rompe por:

- **Mutaciones activadoras** — p. ej. `BRAF V600E` en melanoma, que mantiene la quinasa activa sin señal previa.
- **Fusiones génicas** — `BCR-ABL` en leucemia mieloide crónica, `EML4-ALK` en cáncer de pulmón.
- **Amplificación o sobreexpresión** — `HER2` (ERBB2) en subtipos de cáncer de mama.
- **Pérdida de reguladores negativos** — p. ej. pérdida de PTEN, que desinhibe la vía PI3K/AKT.

El resultado es una señal de proliferación y supervivencia permanentemente encendida. Esto convierte a la quinasa alterada en una **diana terapéutica**: si se bloquea su actividad catalítica, la señal se apaga.

## Inhibidores de quinasas

Un inhibidor de quinasa es una molécula pequeña que se une a la enzima y le impide fosforilar su sustrato. Se clasifican por el sitio y el modo de unión:

- **Tipo I** — compiten con el ATP en el bolsillo catalítico, con la quinasa en conformación activa (DFG-in). Ejemplo: erlotinib.
- **Tipo II** — se unen con la quinasa en conformación inactiva (DFG-out), ocupando además un bolsillo hidrofóbico adyacente. Ejemplo: imatinib.
- **Tipo III / IV** — alostéricos; se unen fuera del sitio del ATP. Ejemplo: trametinib (MEK).
- **Covalentes** — forman un enlace irreversible con un residuo nucleofílico, típicamente una cisteína. Ejemplo: osimertinib (EGFR C797).

Desde la aprobación de imatinib en 2001, la FDA ha autorizado más de 80 inhibidores de quinasas, mayoritariamente en oncología. Es la clase de fármaco dirigido más numerosa.

## El problema de la selectividad

El bolsillo del ATP está muy conservado entre las 518 quinasas humanas. Un inhibidor diseñado para una diana casi siempre se une también a otras: es la **poli-farmacología**. Esto tiene dos caras:

- Puede ser indeseable — los efectos adversos de muchos inhibidores vienen de dianas secundarias.
- Puede ser deseable — algunos fármacos eficaces lo son precisamente por inhibir varias quinasas de una misma vía.

Para el modelado esto es relevante: la actividad de un compuesto siempre se mide **contra una diana concreta en un ensayo concreto**. Mezclar medidas de dianas distintas en un mismo modelo sin tenerlo en cuenta produce resultados sin sentido.

## Cómo se cuantifica la potencia

Los ensayos bioquímicos devuelven una concentración:

- **IC50** — concentración que inhibe el 50 % de la actividad enzimática.
- **Ki** — constante de disociación del inhibidor, independiente de la concentración de sustrato.
- **Kd** — constante de disociación en ensayos de unión.

Todas se convierten a escala logarítmica para el modelado:

$$\text{pIC50} = -\log_{10}(\text{IC50 en mol/L})$$

Así, 1 nM → pIC50 = 9; 1 µM → pIC50 = 6. La escala logarítmica es la adecuada porque la energía libre de unión es proporcional al logaritmo de la constante de afinidad, y porque los errores experimentales son multiplicativos, no aditivos.

Esta variable, **pK**, es la que el modelo del proyecto va a predecir.

## Lecturas de partida

- Manning et al. (2002), *The protein kinase complement of the human genome*, Science.
- Roskoski (2024), revisiones anuales de inhibidores de quinasas aprobados.
- Cohen, Cross & Jänne (2021), *Kinase drug discovery 20 years after imatinib*, Nat Rev Drug Discov.
