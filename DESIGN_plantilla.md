# DESIGN.md — Tablero Café Farallones

> Plantilla del ejercicio «Tablero guiado con IA». Llénala **antes** de pedirle a la IA que construya el tablero y entrégasela como contexto. Todo lo que está entre `<...>` lo reemplazas tú. Si la IA propone algo distinto a lo que dice este archivo, gana este archivo (o lo actualizas a conciencia).

**Equipo:** `<nombres>`
**Fecha:** `<fecha>`
**Versión:** `<v1, v2…>`

---

## 1. Audiencia y propósito

- **¿Quién usa el tablero?** `<p. ej. gerente de operaciones de la cadena>`
- **¿Qué decisión debe tomar con él?** `<una frase>`
- **¿Cuánto tiempo le dedica?** `<p. ej. 2 minutos en la reunión del lunes>`
- **Mensaje principal (big idea):** `<una oración con sujeto, verbo y consecuencia>`

## 2. Preguntas de negocio elegidas

| # | Pregunta | Decisión que habilita | Métrica clave |
|---|----------|----------------------|---------------|
| P_ | `<texto>` | `<qué haría la gerencia con la respuesta>` | `<p. ej. mediana de tiempo de espera>` |
| P_ | | | |
| P_ | | | |

## 3. Inventario de datos

Clasifica **todas** las columnas que vas a usar. Tipos: *continua*, *discreta*, *nominal*, *ordinal*, *temporal*, *texto libre*, *identificador*.

| Columna | Tipo | Rol en el tablero (medida / dimensión / filtro / no se usa) | Problema detectado | Decisión |
|---------|------|------------------------------------------------------------|--------------------|----------|
| `fecha` | temporal | | | |
| `dia_semana` | | | `<p. ej. se ordena alfabéticamente>` | `<orden fijo Lunes→Domingo>` |
| `satisfaccion` | | | | |
| `total_venta` | | | | |
| `<...>` | | | | |

**Reglas de limpieza y cálculo** (la IA debe implementarlas tal cual):

- `<p. ej. comparar sedes con ventas por día abierto, no con totales>`
- `<p. ej. excluir o marcar los pedidos corporativos en promedios>`
- `<p. ej. la satisfacción se reporta como % de respuestas 4–5, no como promedio>`

## 4. Mapa pregunta → gráfico

| Pregunta | Variables | Intención (comparar · tendencia · distribución · relación · composición) | Gráfico elegido | Alternativa descartada y por qué |
|----------|-----------|-------------------------------------------------------------------------|-----------------|----------------------------------|
| P_ | | | | |
| P_ | | | | |
| P_ | | | | |

**Título-conclusión de cada gráfico** (el título dice lo que el lector debe entender, no lo que se graficó):

- P_: `<«Centro pierde clientes satisfechos al almuerzo»>` en lugar de `<«Satisfacción por sede y franja»>`

## 5. Sistema de color

- **Neutro base (contexto):** `<hex>`
- **Color de acento (lo que quiero que miren):** `<hex>` — se usa solo en: `<...>`
- **Paleta categórica** (máx. 5–6 colores, uno por sede si aplica): `<hex, hex, …>`
- **Escala secuencial o divergente** (heatmaps): `<de hex a hex; punto medio si es divergente>`
- **Semántica fija:** `<p. ej. el canal App siempre en el mismo color en todos los gráficos>`
- **Accesibilidad:** `<¿se entiende en escala de grises / para daltonismo? ¿contraste del texto ≥ 4.5:1?>`

## 6. Ejes, escalas y formatos

- **Barras:** eje Y inicia en cero → `<sí/no y por qué>`
- **Orden de categorías ordinales:** `<días Lunes→Domingo, meses Enero→Junio, franjas Mañana→Noche, rango_edad Menos de 18→55 o más, satisfacción 1→5>`
- **Orden de categorías nominales:** `<p. ej. de mayor a menor valor>`
- **Formato de números:** `<COP con separador de miles, p. ej. $1.250.000; minutos con 1 decimal>`
- **Huecos en el tiempo** (días sin datos, sede cerrada): `<cómo se muestran: hueco, anotación, nunca interpolar>`
- **Líneas de referencia / anotaciones:** `<p. ej. meta de 8 min de espera; lanzamiento de la App en febrero; apertura de El Peñón>`

## 7. Layout y jerarquía

```
┌──────────────────────────────────────────────┐
│ Título + mensaje principal       [cargar archivo] │
├──────────┬──────────┬──────────┬──────────┤
│  KPI 1   │  KPI 2   │  KPI 3   │  KPI 4   │
├──────────┴──────────┼──────────┴──────────┤
│  Gráfico P1         │  Gráfico P2         │
├─────────────────────┴─────────────────────┤
│  Gráfico P3 (ancho completo)               │
└──────────────────────────────────────────────┘
```

- **Lectura esperada (patrón Z/F):** `<qué se ve primero, segundo, tercero>`
- **KPIs elegidos y por qué:** `<...>`
- **Qué dejé por fuera a propósito:** `<...>`

## 8. Interactividad

- **Filtros globales:** `<sede, mes, canal…>` — ¿afectan a todos los gráficos? `<sí/no>`
- **Tooltips:** `<qué muestran>`
- **Estados vacíos y errores:** `<qué ve el usuario si el archivo no tiene las columnas esperadas>`

## 9. Requisitos técnicos (para la IA)

- Un solo archivo `index.html`, sin servidor; se abre con doble clic.
- Botón para cargar **.xlsx o .csv** con la estructura de `cafe_farallones_ventas_2026S1` (hoja `ventas`).
- Librerías por CDN: `<p. ej. SheetJS para Excel, PapaParse para CSV, Chart.js o Apache ECharts para gráficos>`.
- Validar que existan las columnas requeridas y avisar cuáles faltan.
- Todos los cálculos se hacen en el navegador a partir del archivo cargado (nada de valores fijos en el código).
- Respetar los colores, órdenes y formatos de las secciones 5 y 6.

## 10. Bitácora de decisiones con IA

| Momento | Qué le pedí a la IA | Qué propuso | Qué acepté / corregí y por qué |
|---------|--------------------|-------------|--------------------------------|
| Exploración | | | |
| Construcción | | | |
| Crítica | | | |

## 11. Autoevaluación final

- [ ] Cada gráfico responde una pregunta de la sección 2
- [ ] Cada título es una conclusión
- [ ] Los ordinales respetan su orden natural
- [ ] Las barras parten de cero
- [ ] El color de acento se usa con intención (≤ 1–2 elementos por gráfico)
- [ ] No hay gráficos de torta con más de 4 porciones
- [ ] Las comparaciones entre sedes son justas (por día abierto)
- [ ] Los valores atípicos están tratados y explicados
- [ ] El tablero funciona con el archivo .csv y con el .xlsx
