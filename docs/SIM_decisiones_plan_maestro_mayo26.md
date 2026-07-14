# SIM HIS Inmuno — Decisiones y plan maestro

**Fecha:** 14 julio 2026  
**Versión:** v0.1 operativa  
**Proyecto:** SIM HIS Inmuno / Herramienta de Impacto HIS Inmuno

Este documento congela las decisiones de lógica y evolución antes de corregir el Excel maestro y evolucionar `index.html`.

---

## 0. Principio rector

SIM debe funcionar en el día a día con:

1. `index.html` publicado en GitHub Pages.
2. La base de datos de hospitales/poblaciones asistenciales.

El Excel de parámetros/cálculo será un documento maestro vivo para validación y actualización esporádica, pero no será el documento operativo diario.

Decisiones:

- La metodología/leyenda irá hardcodeada en `index.html`.
- El Excel servirá para validar fórmulas, documentar supuestos y actualizar defaults.
- La herramienta debe ser autosuficiente una vez cargada la base hospitalaria.

---

## 1. Estado actual

El `index.html` actual es una evolución de `SIM HIS Inmuno_SUR_3.0.html`.

Cambios ya realizados:

- selección nacional dinámica;
- lectura de todos los ISP válidos;
- exclusión de `NO ISP`, vacíos, `Sin asignar`, `N/A`, etc.;
- exclusión de población 0 o negativa;
- filtros por ISP, CCAA y provincia;
- helpers de robustez como `parsearPoblacion`, `normalizarTexto`, `escaparHTML` y `esISPValido`.

El motor de cálculo todavía es el básico y no incorpora toda la nueva lógica del Excel evolucionado.

---

## 2. IDs canónicos de proyecto

Se mantienen los IDs antiguos porque son los que espera `index.html`:

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

No se adoptan como IDs finales los nombres nuevos del Excel evolucionado (`HSConnect-AP`, `HSPROMUEVE-FH`, `PROMUEVE SPAAX`, etc.).

---

## 3. HISTRACK vs Cosentyx

Regla central:

> HISTRACK es el output operativo/asistencial principal del proyecto. Cosentyx es una capa interna de oportunidad, posterior al output asistencial.

Outputs HISTRACK canónicos:

| Proyecto | Output HISTRACK | Tipo |
|---|---|---|
| HS-Connect-AP | Impacto nuevos pacientes diagnosticados | Expansión diagnóstica |
| HS-Connect Intra | Impacto nuevos pacientes diagnosticados intra | Expansión diagnóstica intrahospitalaria |
| HS-Efficiency | Incremento pacientes tratados/capacidad validado internamente | Capacidad/tratamiento |
| HS-PROMueve-Derma | Incremento pacientes switch biológico | Switch |
| HS-PROMUEVE-FH | Incremento pacientes switch biológico | Switch |
| PROMUEVE EspA | Incremento pacientes switch biológico EspAax | Switch |
| PROMueve APs | Incremento pacientes switch biológico APs | Switch |
| EspANDE | Impacto nuevos pacientes diagnosticados EspANDE | Expansión diagnóstica |

---

## 4. Share Cosentyx

Decisión cerrada:

- El Excel maestro incluirá share Cosentyx nacional por defecto.
- `index.html` permitirá override local por hospital/proyecto.
- Si existe override local, se usa ese valor.
- Si no existe, se usa el default nacional.

Regla:

```text
shareCosentyxAplicado = shareLocalHospitalProyecto ?? shareNacionalDefault
```

---

## 5. Prevalencias Reuma

Valores aceptados:

| Patología / proyecto | Prevalencia |
|---|---:|
| APs / PsA | 0,0058 |
| EspAax / axSpA | 0,0026 |
| EspANDE combinado | 0,0084 |

Regla:

```text
EspANDE = APs + EspAax = 0,0058 + 0,0026 = 0,0084
```

Pendiente: documentar fuente bibliográfica limpia para APs y EspAax en la metodología hardcodeada.

---

## 6. Decisiones por proyecto

### HS-Connect-AP

- Mide expansión diagnóstica desde Atención Primaria.
- Cobertura AP editable por hospital en `index.html`.
- Impacto proyecto inicial: 20%, parametrizable en Excel.
- HISTRACK: impacto nuevos pacientes diagnosticados.
- Capas posteriores: Hurley II/III, candidatos biológico, mercado dinámico, pacientes Cosentyx.

### HS-Connect Intra

- Mide expansión diagnóstica intrahospitalaria.
- Valor base: 20%, parametrizable.
- Corregir fórmula para que use la celda correspondiente y no valores hardcodeados inconsistentes.
- HISTRACK: impacto nuevos pacientes diagnosticados intra.

### HS-Efficiency

- Mide mejora de capacidad/eficiencia en consultas de Dermatología para HS.
- Lógica validada internamente.
- Debe conservar capas Hurley II/III, mercado dinámico y Cosentyx.
- Pendiente: etiqueta exacta HISTRACK.

### HS-PROMueve-Derma

- Mide estandarización del seguimiento en Dermatología y aceleración de switch.
- Impacto PROMueve: 20%.
- HISTRACK: incremento pacientes switch biológico.
- Cosentyx como capa posterior.

### HS-PROMUEVE-FH

- Mide seguimiento conjunto FH–Especialista, adherencia, persistencia, seguridad y detección de pacientes no controlados.
- Lógica análoga a PROMueve-Derma.
- HISTRACK: incremento pacientes switch biológico.
- Share Cosentyx editable por hospital/proyecto.

### PROMUEVE EspA

- Prevalencia EspAax: 0,0026.
- HISTRACK: incremento pacientes switch biológico EspAax.
- Cosentyx como capa posterior.

### PROMueve APs

- Prevalencia APs: 0,0058.
- HISTRACK: incremento pacientes switch biológico APs.
- Cosentyx como capa posterior.

### EspANDE

- Expansión diagnóstica Reuma APs + EspAax.
- Análogo a HSConnect-AP.
- Cobertura e impacto diagnóstico deben ir separados.
- Impacto proyecto inicial: 20%, parametrizable.
- Prevalencia combinada: 0,0084.
- HISTRACK: impacto nuevos pacientes diagnosticados EspANDE.

---

## 7. HISlyfecycle

Decisión cerrada:

- Queda fuera de esta fase.
- No se incorpora a `index.html`.
- Debe ignorarse en el Excel maestro.

---

## 8. Metodología / leyenda en `index.html`

Se añadirá una sección hardcodeada:

```text
📘 Lógica de cálculo y fuentes
```

Debe incluir por proyecto:

- qué mide;
- dato HISTRACK;
- fórmula paso a paso;
- parámetros usados;
- origen de cada parámetro;
- distinción entre output asistencial y oportunidad Cosentyx.

Etiquetas acordadas:

| Etiqueta | Significado |
|---|---|
| 📚 Bibliografía | Literatura o referencia externa |
| 🏢 Área terapéutica | Supuesto definido/validado internamente |
| 👤 Input ISP | Dato introducido por usuario |
| 📊 Estimación nacional | Default nacional |
| ✏️ Editable local | Modificable localmente |
| 🎯 HISTRACK | Dato que debe trasladarse a HISTRACK |
| 🧪 Simulación interna | Dato interno, no HISTRACK |

---

## 9. Narrativa dinámica

Se añadirá una capa de narrativa por proyecto, hospital y territorio.

La narrativa base se inspirará en `Narrativa interna por proyecto.pptx`, especialmente para:

- HSConnect-AP;
- HSEfficiency;
- PROMueve;
- PROMueve-FH.

La herramienta deberá soportar placeholders como:

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

---

## 10. Outputs esperados en `index.html`

### Vista por proyecto

- hospitales asignados;
- población cubierta;
- output HISTRACK total;
- subpoblación relevante;
- mercado dinámico;
- share Cosentyx aplicado;
- pacientes estimados Cosentyx;
- narrativa dinámica.

### Vista por hospital

- proyecto;
- output HISTRACK;
- share nacional/local usado;
- pacientes Cosentyx estimados;
- métricas intermedias;
- narrativa hospitalaria si procede.

### Vista territorio/consolidado

HS:

- nuevos diagnosticados HISTRACK;
- incremento pacientes tratados/capacidad;
- incremento switch;
- pacientes estimados Cosentyx.

Reuma:

- nuevos diagnosticados EspANDE;
- incremento switch EspAax;
- incremento switch APs;
- pacientes estimados Cosentyx.

---

## 11. Modelo de datos HTML

Se ampliará el modelo de hospital/proyecto para soportar override local de share:

```text
hospital.shareCosentyxOverrides[proyectoId] = valorLocalOpcional
```

O bien:

```text
hospital.configProyecto[proyectoId] = {
  shareCosentyxOverride: null,
  usaShareLocal: false
}
```

Debe conservarse en importación/exportación JSON.

---

## 12. Fases de trabajo

### Fase 1 — Excel maestro corregido

- [ ] Partir de versión compatible, no primigenia.
- [ ] Mantener IDs antiguos.
- [ ] Corregir nombres de hojas.
- [ ] Corregir HSConnect-Intra.
- [ ] Separar cobertura e impacto en EspANDE.
- [ ] HSConnect-AP con impacto 20% parametrizable.
- [ ] EspANDE con impacto 20% parametrizable.
- [ ] HSPROMueve-Derma a 20%.
- [ ] Incorporar prevalencias Reuma.
- [ ] Estandarizar outputs.
- [ ] Estandarizar HISTRACK.
- [ ] Estandarizar origen de datos.
- [ ] Ignorar HISlyfecycle.
- [ ] Verificar fórmulas.

### Fase 2 — Especificación funcional SIM v2

- [ ] Tabla de parámetros.
- [ ] Tabla de outputs.
- [ ] Vistas proyecto/hospital/territorio.
- [ ] Narrativa base.
- [ ] Metodología hardcodeada.
- [ ] Etiquetas de trazabilidad.
- [ ] Comportamiento share local.
- [ ] JSON.

### Fase 3 — Integración en `index.html`

- [ ] Ampliar parámetros.
- [ ] Ampliar cálculos.
- [ ] Añadir HISTRACK explícito.
- [ ] Añadir Cosentyx.
- [ ] Añadir share por hospital/proyecto.
- [ ] Añadir metodología.
- [ ] Añadir narrativa dinámica.
- [ ] Añadir tooltips.
- [ ] Actualizar vistas.
- [ ] Actualizar JSON.

### Fase 4 — Validación

- [ ] Comparar HTML vs Excel maestro.
- [ ] Probar cobertura variable.
- [ ] Probar share nacional/local.
- [ ] Probar narrativas.
- [ ] Validar HISTRACK vs Cosentyx.

### Fase 5 — GitHub/despliegue

- [ ] Crear rama de trabajo.
- [ ] No subir Excel real ni datos sensibles.
- [ ] Commit incremental.
- [ ] Probar local.
- [ ] Publicar GitHub Pages.
- [ ] Smoke test.
- [ ] Changelog.

---

## 13. Pendientes abiertos

- [ ] Confirmar output exacto HISTRACK de HS-Efficiency.
- [ ] Confirmar si EspANDE mostrará Cosentyx visible o solo como capa interna.
- [ ] Confirmar fuentes bibliográficas APs/EspAax.
- [ ] Confirmar shares nacionales default por proyecto.
- [ ] Confirmar nombres visibles finales.
- [ ] Reescribir narrativa de todos los proyectos.

---

## 14. Resumen ejecutivo

SIM debe evolucionar de calculadora básica a herramienta con:

1. simulador de impacto asistencial;
2. output HISTRACK explícito;
3. estimación interna Cosentyx;
4. share editable por hospital/proyecto;
5. metodología trazable;
6. narrativa dinámica.

Prioridad inmediata: crear Excel maestro limpio y compatible antes de tocar `index.html`.
