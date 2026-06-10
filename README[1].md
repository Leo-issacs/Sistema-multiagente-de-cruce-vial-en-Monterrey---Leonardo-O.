# Coordinación inteligente de cruces semafóricos — Corredor Libertad (Monterrey, N.L.)

**TC2032 — Agentes Inteligentes · Proyecto Final Integrador**

Simulación multiagente de un corredor vial real de Monterrey (calle **Libertad**, Col. Nuevo
Repueblo) con sus tres intersecciones semaforizadas: **Sonora, Chiapas y Tepic**. El proyecto
compara la operación de los semáforos **sin coordinación** contra una coordinación por
**onda verde** (offsets calculados como distancia ÷ velocidad), y cuantifica el impacto sobre la
movilidad del corredor.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Leo-issacs/Sistema-multiagente-de-cruce-vial-en-Monterrey---Leonardo-O./blob/main/corredor_libertad_semaforos.ipynb)

## Autores

- Leonardo Orozco Guerrero — A00843030
- Adrian David Paez Parra — A00842372

## Resultados principales

Misma demanda, mismo ciclo y misma semilla aleatoria (42) en ambos escenarios; la **única
variable manipulada es la coordinación** (300 pasos de simulación).

| Indicador | Sin coordinación | Onda verde | Cambio |
|---|---:|---:|---:|
| Tiempo medio de viaje (pasos) | 67.4 | 52.1 | **−22.7%** |
| Tiempo medio de espera (pasos) | 22.4 | 7.1 | **−68.3%** |
| Paradas promedio por vehículo | 2.61 | 1.00 | **−61.7%** |
| Vehículos atendidos | 198 | 205 | **+3.5%** |
| Espera en calles transversales (pasos) | 5.2 | 4.9 | −5.8% |

El resultado es robusto: se verificó con 4 semillas distintas (1, 7, 42, 100) con reducciones
consistentes. Las colisiones son **cero por construcción** en todas las corridas.

### Diagrama espacio-tiempo

Las franjas muestran la fase (verde/rojo) de cada semáforo y las líneas diagonales son las
trayectorias reales de los vehículos. Sin coordinación, las trayectorias se quiebran contra el
rojo en cada cruce; con onda verde, atraviesan los tres cruces sin detenerse.

![Diagrama espacio-tiempo](figs/st_diagram.png)

### Comparación de indicadores

![Comparación de indicadores](figs/check_bar.png)

## Modelo

- **Entorno:** red discreta de 46×13 celdas; Libertad horizontal (un sentido O→E, como en el
  tramo real) y tres transversales verticales de un sentido, separadas 14 celdas.
- **Agentes vehículo:** tres reglas locales — si la celda siguiente está ocupada, detenerse; si
  el semáforo de su aproximación está en rojo, detenerse; en otro caso, avanzar una celda. La
  ocupación se actualiza al instante, lo que garantiza cero colisiones.
- **Agentes semáforo:** dos fases con ciclo fijo C = 24 pasos y verde principal G = 14 (reparto
  proporcional a la demanda). Decisión: verde a Libertad si `(t + offset) mod C < G`.
- **Coordinación (heurística de onda verde):** `offset_i = distancia_i ÷ velocidad`, lo que da
  offsets `[0, 14, 4]` para los tres cruces.

## Estructura del repositorio

```
├── corredor_libertad_semaforos.ipynb   # Notebook principal del proyecto (Colab-ready)
├── docs/
│   └── Reporte_Corredor_Libertad.docx  # Reporte técnico (estructura completa del curso)
├── figs/                               # Figuras generadas por la simulación
├── practicas/                          # Entregas previas (cruce en + y cruce en T, sin semáforos)
├── requirements.txt
└── README.md
```

## Cómo ejecutar

**Opción 1 — Google Colab (recomendada):** clic en el badge "Open in Colab" de arriba y luego
`Entorno de ejecución → Ejecutar todo`. No requiere instalación.

**Opción 2 — Local:**

```bash
pip install -r requirements.txt
jupyter notebook corredor_libertad_semaforos.ipynb
```

El notebook es reproducible (`seed = 42`). Los parámetros de demanda y de ciclo se editan en la
celda 5 (sección "Demanda vehicular").

## Supuestos y limitaciones

Velocidad uniforme (1 celda/paso), sin giros en las intersecciones, sin peatones ni fase ámbar,
y demanda estacionaria. Por ello los resultados deben leerse como **cota optimista** del
beneficio alcanzable en campo. Los resultados provienen de simulación y no representan datos de
campo verificados.

## Trabajo futuro

- Integrar datos de demanda reales con variación horaria.
- Modelar giros en intersecciones y fase ámbar.
- Comparar la onda verde contra control adaptativo (p. ej., Q-Learning con estados de cola y
  recompensa por menor espera).
- Extender a más intersecciones, doble sentido y comunicación entre agentes.
