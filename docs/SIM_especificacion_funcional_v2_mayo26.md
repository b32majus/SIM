# SIM HIS Inmuno — Especificación funcional SIM v2

**Fecha:** 14 julio 2026  
**Versión:** v0.1  
**Estado:** documento funcional previo a integración en `index.html`  
**Documento relacionado:** `docs/SIM_decisiones_plan_maestro_mayo26.md`

## 1. Objetivo

Definir qué debe hacer SIM v2 antes de tocar el código. La herramienta debe evolucionar desde cálculo básico de impacto hacia:

1. cálculo asistencial por proyecto;
2. output HISTRACK explícito;
3. estimación interna de oportunidad Cosentyx;
4. share Cosentyx editable por hospital/proyecto;
5. metodología/leyenda hardcodeada;
6. narrativa dinámica por proyecto, hospital y territorio;
7. export/import JSON preservando configuración local.

## 2. Principios

`index.html` debe ser autosuficiente y funcionar con la base de hospitales/poblaciones asistenciales. El Excel maestro queda como documento vivo de validación y actualización esporádica, no como dependencia diaria.

Regla crítica:

```text
HISTRACK = output operativo/asistencial del proyecto
Cosentyx = capa interna de oportunidad, no HISTRACK
```

Los IDs actuales deben mantenerse:

```text
HS-Connect-AP 
HS-Connect Intra
HS-Efficiency
HS-PROMueve-Derma
HS-PROMUEVE-FH
PROMUEVE EspA
PROMueve APs
EspANDE 
```

El importador futuro debe aceptar alias normalizados con `trim()` para no depender de espacios finales en nombres de hoja.

## 3. Modelo de datos propuesto

Cada hospital debe soportar configuración por proyecto:

```js
hospital.configProyecto = {
  [proyectoId]: {
    shareCosentyxOverride: null,
    usaShareLocal: false
  }
}
```

Regla de share:

```js
shareAplicado = overrideLocal ?? shareDefaultProyecto
```

Si hay override, la interfaz debe indicar `local`. Si no lo hay, debe indicar `nacional`.

## 4. Outputs canónicos

| Proyecto | Output HISTRACK | Outputs internos |
|---|---|---|
| HS-Connect-AP | Impacto nuevos pacientes diagnosticados | Hurley II/III, candidatos biológico, mercado dinámico, Cosentyx |
| HS-Connect Intra | Impacto nuevos pacientes diagnosticados intra | Hurley II/III, candidatos biológico, mercado dinámico, Cosentyx |
| HS-Efficiency | Incremento pacientes tratados/capacidad validado internamente | Mercado dinámico, Cosentyx |
| HS-PROMueve-Derma | Incremento pacientes switch biológico | Cosentyx |
| HS-PROMUEVE-FH | Incremento pacientes switch biológico | Cosentyx |
| PROMUEVE EspA | Incremento pacientes switch biológico EspAax | Cosentyx |
| PROMueve APs | Incremento pacientes switch biológico APs | Cosentyx |
| EspANDE | Impacto nuevos pacientes diagnosticados EspANDE | Cosentyx opcional/interno |

Pendiente: confirmar etiqueta exacta de HISTRACK en HS-Efficiency y si EspANDE muestra Cosentyx visible.

## 5. Lógica de cálculo por proyecto

### HS-Connect-AP

```text
población cubierta = población asistencial × cobertura AP
prevalentes HS = población cubierta × prevalencia HS
diagnosticados actuales = prevalentes HS × ratio diagnóstico basal
diagnosticados con proyecto = diagnosticados actuales × (1 + impacto proyecto)
impacto nuevos diagnosticados = diagnosticados con proyecto - diagnosticados actuales
Hurley II/III = impacto nuevos diagnosticados × % Hurley II/III
candidatos biológico = Hurley II/III × tasa candidato/tratamiento
mercado dinámico = candidatos biológico × % mercado dinámico
pacientes Cosentyx = mercado dinámico × share Cosentyx aplicado
```

HISTRACK: impacto nuevos pacientes diagnosticados. Cobertura AP editable en HTML. Impacto proyecto inicial: 20%, parametrizable.

### HS-Connect Intra

```text
prevalentes HS = población asistencial × prevalencia HS
diagnosticados actuales = prevalentes HS × ratio diagnóstico basal
diagnosticados intra actuales = diagnosticados actuales × % diagnóstico intrahospitalario
diagnosticados intra con proyecto = diagnosticados intra actuales × (1 + impacto proyecto)
impacto nuevos diagnosticados intra = diagnosticados intra con proyecto - diagnosticados intra actuales
```

HISTRACK: impacto nuevos pacientes diagnosticados intra. Valor base 20%, parametrizable.

### HS-Efficiency

```text
prevalentes HS = población asistencial × prevalencia HS
diagnosticados HS = prevalentes HS × ratio diagnóstico basal
Hurley II/III = diagnosticados HS × % Hurley II/III
incremento capacidad/tratados = subpoblación relevante × impacto capacidad
mercado dinámico = incremento capacidad/tratados × % mercado dinámico
pacientes Cosentyx = mercado dinámico × share Cosentyx aplicado
```

Lógica validada internamente. Pendiente etiqueta exacta HISTRACK.

### HS-PROMueve-Derma / HS-PROMUEVE-FH

```text
prevalentes HS = población asistencial × prevalencia HS
diagnosticados HS = prevalentes HS × ratio diagnóstico basal
pacientes tratados = diagnosticados HS × tasa tratamiento
pacientes dinámicos switch = pacientes tratados × % mercado dinámico/dinámico switch
incremento switch = pacientes dinámicos switch × impacto PROMueve
pacientes Cosentyx = incremento switch × share Cosentyx aplicado
```

HISTRACK: incremento pacientes switch biológico. Impacto PROMueve-Derma: 20%.

### PROMUEVE EspA

```text
prevalentes EspAax = población asistencial × 0,0026
diagnosticados = prevalentes EspAax × ratio diagnóstico basal
tratados = diagnosticados × tasa tratamiento
pacientes dinámicos switch = tratados × % mercado dinámico
incremento switch = pacientes dinámicos switch × impacto PROMueve
pacientes Cosentyx = incremento switch × share Cosentyx aplicado
```

HISTRACK: incremento pacientes switch biológico EspAax.

### PROMueve APs

```text
prevalentes APs = población asistencial × 0,0058
diagnosticados = prevalentes APs × ratio diagnóstico basal
tratados = diagnosticados × tasa tratamiento
pacientes dinámicos switch = tratados × % mercado dinámico
incremento switch = pacientes dinámicos switch × impacto PROMueve
pacientes Cosentyx = incremento switch × share Cosentyx aplicado
```

HISTRACK: incremento pacientes switch biológico APs.

### EspANDE

```text
población cubierta = población asistencial × cobertura proyecto
prevalentes Reuma = población cubierta × 0,0084
diagnosticados actuales = prevalentes Reuma × ratio diagnóstico basal
diagnosticados con proyecto = diagnosticados actuales × (1 + impacto proyecto)
impacto nuevos diagnosticados = diagnosticados con proyecto - diagnosticados actuales
```

HISTRACK: impacto nuevos pacientes diagnosticados EspANDE. Cobertura editable en HTML. Impacto proyecto inicial: 20%, parametrizable.

## 6. Interfaz

### Tabla principal

No saturar con más columnas. Añadir configuración de share local mediante modal o botón `⚙️` por proyecto/hospital.

### Vista por proyecto

Mostrar:

- hospitales asignados;
- población cubierta;
- output HISTRACK total;
- subpoblación relevante;
- mercado dinámico;
- share Cosentyx aplicado;
- pacientes Cosentyx;
- narrativa dinámica;
- acceso a metodología.

### Vista por hospital

Mostrar por hospital/proyecto:

- output HISTRACK;
- métricas intermedias principales;
- share usado y origen nacional/local;
- pacientes Cosentyx;
- narrativa granular si procede.

### Vista territorio

Mostrar totales ejecutivos por área:

HS: nuevos diagnosticados, capacidad/tratados, switch, pacientes Cosentyx.  
Reuma: nuevos diagnosticados EspANDE, switch EspAax, switch APs, pacientes Cosentyx.

## 7. Metodología hardcodeada

Añadir sección:

```text
📘 Lógica de cálculo y fuentes
```

Debe explicar por proyecto: qué mide, HISTRACK, fórmulas, parámetros, origen y diferencia HISTRACK/Cosentyx.

Etiquetas:

| Etiqueta | Significado |
|---|---|
| 📚 Bibliografía | Literatura |
| 🏢 Área terapéutica | Supuesto AT validado |
| 👤 Input ISP | Dato usuario |
| 📊 Estimación nacional | Default nacional |
| ✏️ Editable local | Override hospital/proyecto |
| 🎯 HISTRACK | Dato a registrar |
| 🧪 Simulación interna | Dato interno, no HISTRACK |

## 8. Narrativa dinámica

Usar templates con placeholders:

```text
{{territorio}}
{{hospitales}}
{{poblacion}}
{{impacto_histrack}}
{{hurley_ii_iii}}
{{candidatos_biologico}}
{{mercado_dinamico}}
{{pacientes_cosentyx}}
{{share_cosentyx}}
```

Debe generarse en vista proyecto, hospital y consolidado.

## 9. JSON

Exportar/importar:

- hospitales;
- proyectos asignados;
- cobertura AP;
- impactos;
- selección;
- fusiones;
- share local por hospital/proyecto;
- origen nacional/local del share;
- versión de modelo: `SIM_v2_mayo26`.

Si se importa JSON antiguo sin `configProyecto`, inicializar estructura vacía y usar share nacional.

## 10. Orden recomendado de implementación

1. Parámetros ampliados.
2. Outputs ampliados en funciones de cálculo.
3. `histrackValue` y `cosentyxValue` por resultado.
4. Share default + override local.
5. JSON import/export.
6. Vistas proyecto/hospital/territorio.
7. Metodología hardcodeada.
8. Narrativa dinámica.
9. Tooltips.
10. Validación contra Excel maestro.

## 11. Pendientes antes de programar

- [ ] Confirmar etiqueta exacta HISTRACK de HS-Efficiency.
- [ ] Confirmar si EspANDE mostrará Cosentyx visible o solo interno.
- [ ] Confirmar shares nacionales default por proyecto.
- [ ] Confirmar fuentes bibliográficas finales de APs y EspAax.
- [ ] Revisar narrativa base final antes de hardcodear.
- [ ] Definir diseño final del input de share local: inline vs modal.
