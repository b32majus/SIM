# SIM HIS Inmuno — Cierre de flecos funcionales v0.2

**Fecha:** 15 julio 2026  
**Estado:** decisiones funcionales cerradas antes de iniciar integración en `index.html`.

Este documento complementa:

- `docs/SIM_decisiones_plan_maestro_mayo26.md`
- `docs/SIM_especificacion_funcional_v2_mayo26.md`

---

## 1. HS-Efficiency / HISTRACK

Decisión cerrada:

```text
HISTRACK HS-Efficiency = Incremento pacientes tratados/candidatos HS Hurley II-III
```

La narrativa del proyecto debe hablar de generación de capacidad asistencial, pero el número registrable en HISTRACK debe expresarse como pacientes tratados/candidatos en subpoblación HS Hurley II-III.

---

## 2. EspANDE / Cosentyx

Decisión cerrada:

```text
EspANDE HISTRACK visible = impacto nuevos pacientes diagnosticados EspANDE
```

No se mostrará caída visible a Cosentyx en EspANDE en esta fase, porque no existen datos intermedios suficientemente sólidos para justificar una asociación indirecta diagnóstico → oportunidad Cosentyx.

EspANDE debe tratarse como proyecto de expansión diagnóstica, no como proyecto de switch.

---

## 3. Shares nacionales default

Decisión cerrada:

| Área / proyectos | Share Cosentyx default | Uso |
|---|---:|---|
| HS | 37% | Default nacional editable mediante override local |
| Reuma | 20% | Default nacional editable mediante override local |

Estos valores representan el share nacional de Cosentyx en HS y Reuma. En `index.html` se usarán como default. El ISP podrá introducir un share local por hospital/proyecto cuando conozca el dato específico.

---

## 4. Prevalencias Reuma y fuente metodológica

Decisión cerrada:

| Patología / proyecto | Prevalencia operacional SIM |
|---|---:|
| APs / PsA | 0,0058 |
| EspAax / axSpA | 0,0026 |
| EspANDE combinado | 0,0084 |

La metodología de SIM citará estas prevalencias como parámetros operacionales basados en EPISER/SER y adaptados al modelo SIM. No deben presentarse como una transcripción literal aislada sin contexto, sino como valores de trabajo metodológico para el simulador.

---

## 5. Narrativa dinámica

Decisión cerrada:

- El PowerPoint de narrativa se usará como semilla.
- No se reutilizará texto con typos ni placeholders `XXX`.
- La narrativa final se reescribirá limpia y se hardcodeará con placeholders controlados.
- Habrá narrativa por proyecto, por hospital y por territorio/consolidado.

---

## 6. Input de share local

Decisión cerrada:

```text
Input de share local = inline dentro del detalle proyecto/hospital
```

No irá en la tabla principal para evitar saturación. En el detalle de cada proyecto/hospital se mostrará:

- share Cosentyx aplicado;
- si procede de valor nacional o local;
- opción para editar valor local;
- opción para volver al default nacional.

Debe guardarse y restaurarse en JSON.

---

## 7. Consecuencia para la Fase 3

Con estos puntos cerrados, puede iniciarse la preparación de integración en `index.html` con estas prioridades:

1. no tocar aún la lógica visual principal sin copia/backup;
2. introducir outputs HISTRACK explícitos;
3. añadir outputs Cosentyx donde aplique;
4. excluir Cosentyx visible en EspANDE;
5. añadir share local por hospital/proyecto;
6. conservar compatibilidad JSON;
7. añadir metodología hardcodeada y narrativa dinámica después de estabilizar cálculos.
