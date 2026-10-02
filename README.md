# Analisis-Riesgo-Credito
Medición de riesgo de crédito en cuatro carteras de consumo: Pérdida Esperada, PNEp, CaR al 99% y Capital Económico en Excel (dic-22 vs dic-23).

# Riesgo de crédito en carteras de consumo: Pérdida Esperada, PNEp, CaR y Capital Económico

Estimación de pérdidas asociadas al riesgo de crédito de cuatro carteras de consumo (cnr nomina, cnr personales, cnr auto y cnr abcd) con datos por acreditado a diciembre de 2022 y diciembre de 2023, bajo dos supuestos de correlación entre incumplimientos (ρ = 0.1 y ρ = 0.3).

## Herramientas

  - Microsoft Excel

## Datos

Base **Carteras 202312**, con 1,400 créditos por cartera en cada fecha (cve_periodo 202212 y 202312). Las variables que se usan de cada crédito son:

| Variable | Uso en el modelo |
|---|---|
| `SdoCred` | EAD, exposición al incumplimiento |
| `SP_Acreditado` | LGD, severidad de la pérdida |
| `PI_Acreditado` | PD, probabilidad de incumplimiento |
| `cve_periodo`, `cve_institucion`, `FolioCred`, `Rvas` | Identificación y referencia |


## Metodología

**1. Por acreditado**

- Pérdida esperada: `PEi = SdoCred · SP_Acreditado · PI_Acreditado`
- Pérdida no esperada: `PNEi = SdoCred · SP_Acreditado · √(PI_Acreditado · (1 − PI_Acreditado))`
- `PNEi^2`, que se usa en la agregación

**2. Por cartera y fecha**

- Pérdida Esperada: `Σ PEi`
- PNEp, con correlación uniforme ρ:

  `PNEp = √( Σ PNEi² + ρ · [ (Σ PNEi)² − Σ PNEi² ] )`

- CaR al 99%: `CaR = Pérdida Esperada + β · PNEp`, con β = 2.326
- Capital Económico: `CaR − PNEp`

Cada medida se calcula para ρ = 0.1 y ρ = 0.3.


## Estructura del archivo `riesgo_credito_PE_PNE_CaR.xlsx`

El archivo tiene dos hojas: **Cálculos**, con los datos por acreditado y las medidas de riesgo, y **Resultados**, con la tabla resumen y las gráficas.

### Hoja `Cálculos`

Cada cartera ocupa un bloque de columnas con los datos por acreditado y los cálculos individuales (`PEi`, `PNEi`, `PNEi^2`):

| Rango | Contenido |
|---|---|
| A:J | cnr nomina |
| N:W | cnr personales |
| AA:AJ | cnr auto |
| AN:AW | cnr abcd |

Dentro de cada bloque, las filas 3–1402 son los créditos de dic-22 y las filas 1405–2804 los de dic-23.

Junto a cada bloque hay dos columnas auxiliares sin encabezado, `PEi·(1+θ)` y `PEi + α·PNEi`, que no entran en el resumen.

Los parámetros del modelo están en BA:BB:

| Celda | Parámetro |
|---|---|
| BB2 | ρ = 0.1 |
| BB3 | ρ = 0.3 |
| BB6 | β = 2.326 |
| BB9 | α = 2 |
| BB10 | θ = 0.05 |

El resumen por cartera está en BD:BM. Ahí se calculan Pérdida Esperada, Suma PNEi, Suma PNEi^2, PNEp, CaR y Capital Económico para ambas ρ:

| Cartera | dic-22 | dic-23 |
|---|---|---|
| cnr nomina | fila 3 | fila 4 |
| cnr personales | fila 8 | fila 9 |
| cnr auto | fila 13 | fila 14 |
| cnr abcd | fila 18 | fila 19 |

### Hoja `Resultados`

**Tabla resumen (A2:K10).** Contiene Pérdida Esperada, PNEp, CaR y Capital Económico por cartera y fecha, copiados del resumen de `Cálculos`. Las columnas J y K son auxiliares para las gráficas de composición:

- `β·PNEp ρ = 0.1` = CaR ρ = 0.1 − Pérdida Esperada
- `β·PNEp ρ = 0.3` = CaR ρ = 0.3 − Pérdida Esperada

**Gráficas:**

| Gráfica | Contenido |
|---|---|
| Gráfica 1 | CaR con ρ = 0.1 por cartera, dic-22 vs dic-23 |
| Gráfica 2 | CaR con ρ = 0.3 por cartera, dic-22 vs dic-23 |
| Gráfica 3 | Pérdida Esperada vs componente no esperado con ρ = 0.1, por cartera y fecha |
| Gráfica 4 | Pérdida Esperada vs componente no esperado con ρ = 0.3, por cartera y fecha |


## Resultados


<img width="1107" height="350" alt="image" src="https://github.com/user-attachments/assets/d7d0a00d-8336-4985-a84b-86036433d865" />

<img width="1160" height="409" alt="image" src="https://github.com/user-attachments/assets/ad88b930-a725-46bd-8422-07e143c0c6b9" />

## Interpretación de resultados

### Efecto de la diversificación

Con 1,400 créditos por cartera, la PNEp queda muy por debajo de la suma simple de las PNEi. En cnr nomina a dic-22, Σ PNEi es 13.81 millones, pero la PNEp con ρ = 0.1 es 4.39 millones, alrededor de 68% menos. Esa diferencia es el beneficio de diversificación: no todos los acreditados incumplen al mismo tiempo.

Con tantos créditos, la parte idiosincrática (Σ PNEi²) pesa poco en la fórmula y la PNEp se aproxima a √ρ · Σ PNEi. Por eso la PNEp crece casi en proporción a √ρ: pasar de ρ = 0.1 a ρ = 0.3 la multiplica por cerca de √3 ≈ 1.73 en todas las carteras. En otras palabras, en carteras grandes y granulares el riesgo que queda es casi todo sistémico, y su tamaño lo determina el supuesto de correlación.

### Sensibilidad a la correlación

El CaR sube entre 55% y 67% al pasar de ρ = 0.1 a ρ = 0.3. El aumento es menor que el de la PNEp porque la Pérdida Esperada no depende de ρ. cnr auto es la más sensible (alrededor de 67%), porque casi todo su CaR viene de la PNEp. Como ρ no se estimó con datos sino que se supuso, conviene leer los resultados como un rango y no como una cifra puntual.

### Pérdida Esperada vs pérdida no esperada

En cnr nomina, cnr personales y cnr abcd, la Pérdida Esperada representa entre 17% y 24% del CaR con ρ = 0.1. En cnr auto es apenas 8%. La razón está en su PD: con PI_Acreditado cercana a 0.42%, el factor √(PD·(1−PD)) es unas 15 veces mayor que PD. Es una cartera con incumplimientos poco frecuentes, pero con saldos grandes. Casi no genera pérdida esperada, y aun así requiere un colchón de capital considerable por pérdidas no esperadas.

### Evolución de dic-22 a dic-23

- **cnr nomina:** la Pérdida Esperada, la PNEp y el CaR crecen alrededor de 20%, a la par. Esto apunta a un aumento del volumen o del riesgo de la cartera, más que a un cambio en su estructura.
- **cnr personales:** la Pérdida Esperada se mantiene en 1.90 millones, pero la PNEp baja cerca de 11%. El CaR cae porque la cartera se volvió menos volátil, no porque se espere perder menos.
- **cnr auto:** la Pérdida Esperada cae 24% y la PNEp 19%, es decir, una reducción general del riesgo.
- **cnr abcd:** la Pérdida Esperada casi se reduce a la mitad (−47%) y el CaR cae 26%. En monto sigue siendo marginal.

### Concentración del riesgo

La suma del CaR con ρ = 0.1 de las cuatro carteras prácticamente no cambia entre fechas: 32.29 millones en dic-22 y 32.32 millones en dic-23. Lo que cambia es la composición. cnr nomina pasa de 41% a 50% del total, así que el riesgo total se mantuvo, pero quedó más concentrado en una sola cartera. Esta suma no considera la correlación entre carteras, por lo que es una cota conservadora del riesgo conjunto.

### Capital Económico

El Capital Económico conserva el mismo orden que el CaR: cnr nomina > cnr auto > cnr personales > cnr abcd en dic-22, y cnr nomina > cnr personales > cnr auto > cnr abcd en dic-23. En dic-23, cnr auto pasa al tercer lugar por la caída de su PNEp. Con ρ = 0.3, el Capital Económico de cnr nomina llega a 15.84 millones, el mayor requerimiento del análisis.

## Hallazgos

- cnr nomina concentra el mayor riesgo y es la única cartera cuyo CaR aumenta de dic-22 a dic-23 (de 13.32 a 16.05 con ρ = 0.1).
- cnr personales, cnr auto y cnr abcd reducen su CaR entre ambas fechas.
- Pasar de ρ = 0.1 a ρ = 0.3 eleva el CaR entre 55% y 70%, así que la correlación es el supuesto con mayor impacto.
- En cnr auto el riesgo es casi totalmente no esperado: la Pérdida Esperada (0.79) es menos del 10% del CaR (9.75).

## Limitaciones

- Correlación uniforme entre todos los acreditados de una cartera.
- LGD constante por cartera.
- Aproximación normal para el CaR (β = 2.326).
- Análisis estático en dos fechas de corte.

## Autoría

Proyecto desarrollado por Celeste Núñez López, Kevin Rodríguez Pérez, Francisco Lince Domínguez y Alejandro Dorantes Quiroz.
