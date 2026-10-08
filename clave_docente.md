# Clave docente — Ejercicio «Tablero guiado con IA: Café Farallones»

Uso exclusivo del docente. Cifras calculadas sobre `cafe_farallones_ventas_2026S1` (5.637 líneas de venta, 1 ene – 30 jun 2026, 5 sedes, datos ficticios). Ventas en COP.

## Historias sembradas en los datos

| # | Historia | Dónde se ve |
|---|----------|-------------|
| 1 | Centro colapsa al almuerzo entre semana: espera larga → baja satisfacción | `sede` × `franja_horaria` × `tiempo_espera_min` × `satisfaccion` |
| 2 | El Peñón abrió el 2 de marzo con curva de arranque | `fecha`, hoja `sedes` |
| 3 | La App (feb) crece con 15 % de descuento | `canal` × `mes`, `descuento_pct` |
| 4 | La lluvia mueve a domicilio y a bebidas calientes; el calor a bebidas frías | `lluvia`, `temperatura_c`, `categoria`, `canal` |
| 5 | 24 productos con distribución tipo Pareto; unidades ≠ ingresos | `producto`, `cantidad`, `total_venta` |
| 6 | Patrón semanal opuesto: Centro de oficina vs Granada de fin de semana | `dia_semana` × `sede` |
| 7 | Fidelizados y Ciudad Jardín: mayor ticket y satisfacción | `tipo_cliente`, `sede`, `rango_edad` |

## Respuestas esperadas

### P1. ¿En qué sede y franja conviene contratar un barista adicional?

- Centro al almuerzo: mediana de espera **10,7 min** (las demás sedes al almuerzo: 5,5–6,0 min). Satisfacción promedio **3,61** vs ≥ 4,02 en las otras sedes en esa franja.
- Relación espera–satisfacción: con ≤ 10 min, ≤ 3 % de calificaciones 1–2; con 10–15 min sube a 17 %; con > 15 min, **53 %**.
- Centro tiene 2 baristas por turno (hoja `sedes`) y es la sede con más ventas por día.
- **Decisión esperada:** barista extra en Centro, L–V ~11:30–14:30; complementar empujando la recogida por App (mediana de espera App 2,4 min vs Mostrador 4,2).
- **Gráficos buenos:** heatmap sede × franja (mediana de espera, escala secuencial); barras de % de calificaciones 1–2 por tramo de espera; dispersión con *jitter* si usan la escala Likert.
- **Trampas:** promediar una variable sesgada (usar mediana); tratar Likert como continua sin advertirlo; olvidar que domicilio no tiene espera.

### P2. ¿Es El Peñón un fracaso?

- En totales parece la peor sede (8,0 M vs 18,3 M de Granada) porque operó 118 días vs 174.
- Ventas por día abierto: marzo **51 k** → abril **75 k**, mayo **72 k**, junio **72 k**. Comparable con Ciudad Jardín (79 k) y San Antonio (82 k) en el semestre.
- **Decisión esperada:** no cerrar; evaluar con ventas por día y con la curva de los primeros 90 días.
- **Gráficos buenos:** línea de ventas/día por mes por sede con anotación de apertura; barras de ventas/día.
- **Trampas:** comparar totales con periodos distintos; línea que arranca en cero en enero–febrero (no existía: es dato faltante, no cero).

### P3. ¿Mantener el 15 % de descuento de la App?

- Participación de la App en líneas de venta: 0 % (ene) → 8 % (feb) → 13 % → 22 % → 27 % → **28 % (jun)**.
- Ticket promedio App **11,5 k** vs Mostrador **13,8 k** (sin pedidos corporativos). Descuento otorgado en el semestre: **1,94 M** sobre 10,98 M vendidos por App.
- La App reduce espera (2,4 vs 4,2 min) y tiene satisfacción algo mayor (4,31 vs 4,17).
- Ventas por día de la cadena: enero 478 k, junio 483 k (+1 %), **aunque se abrió una sede nueva** → indicio de canibalización del mostrador.
- **Decisión esperada:** abierta y argumentada (p. ej. bajar a 10 %, limitarlo a franjas valle, o condicionarlo a fidelización). Se evalúa la calidad del argumento.
- **Gráficos buenos:** área o barras apiladas al 100 % por mes y canal; KPI de descuento acumulado.
- **Trampas:** meses en orden alfabético (Abril, Enero, Febrero…); comparar ventas totales de febrero (28 días) con otros meses.

### P4. ¿Qué hacer en los días de lluvia?

- Domicilio pasa de **11,7 %** a **23,2 %** de las líneas; café caliente de **29,5 %** a **44,3 %**; bebidas frías de **19,2 %** a **7,2 %**.
- Calor: bebidas frías 7 % (≤ 26 °C) → 26 % (> 30 °C).
- Días con lluvia por mes: ene 0, feb 5, mar 10, abr 14, may 15, jun 6.
- **Decisión esperada:** reforzar domiciliarios e inventario de café caliente en días de lluvia (temporada abril–mayo); promos de bebidas frías en días calurosos.
- **Gráficos buenos:** barras agrupadas o *small multiples* con proporciones (lluvia sí/no); línea o barras por tramos de temperatura.
- **Trampas:** comparar conteos absolutos (hay muchos más días secos); confusión con el mes (la lluvia se concentra cuando la App ya creció).

### P5. ¿Qué productos sacar o simplificar del menú?

- 24 productos: los 10 primeros concentran el **68 %** de las ventas. Top en ingresos: Wrap de pollo, Sándwich cubano, Ensalada César (almuerzos).
- Tinto es el **1.º en unidades** (10 % de unidades) pero el 10.º en ingresos; Pandebono, 2.º en unidades y 17.º en ingresos.
- Candidatos a salir: Barra de granola (0,9 % de ventas, 1,3 % de unidades), Papas de paquete, Galletas. Buñuelo vende poco en pesos pero mucho en unidades (posible generador de tráfico): buena discusión.
- **Gráficos buenos:** barras horizontales ordenadas + línea de % acumulado (Pareto); comparación lado a lado unidades vs ingresos.
- **Trampas:** torta con 24 porciones; incluir los pedidos corporativos (inflan los almuerzos).

### P6. ¿Cómo ajustar personal y horario por día de la semana?

- Centro: ~155–175 k/día de lunes a viernes, 70 k el sábado, cerrado domingos y festivos.
- Granada: **159 k** el sábado y **139 k** el domingo vs 71–99 k entre semana.
- Granada estuvo cerrada del 11 al 17 de mayo (remodelación): hueco en la serie.
- **Decisión esperada:** reasignar personal de Centro (fin de semana) a Granada (fin de semana); evaluar horario reducido de Centro el sábado.
- **Gráficos buenos:** heatmap día × sede de ventas por día abierto, con días en orden Lunes→Domingo.
- **Trampas:** días en orden alfabético; Centro el martes da **291 k** con los pedidos corporativos y **161 k** sin ellos; mostrar el domingo de Centro como 0 en lugar de «cerrado»; interpolar la semana de remodelación.

### P7 (bonus). ¿Quiénes son los clientes de mayor valor?

- Fidelizados: ticket promedio **14,8 k** vs Nuevos **12,9 k**; satisfacción 4,39 vs 4,06.
- Ciudad Jardín: mediana de ticket **11,9 k** vs 9,4 k de la cadena; clientela de mayor edad.
- **Trampa:** `rango_edad` ordenado alfabéticamente deja «Menos de 18» al final.

## Problemas de graficación sembrados (para la crítica en clase)

| Problema | Columna | Qué debería hacer el estudiante |
|----------|---------|-------------------------------|
| Orden alfabético de ordinales | `dia_semana`, `nombre_mes`, `franja_horaria`, `rango_edad` | Orden explícito |
| Atípicos | 12 pedidos corporativos (25–60 almuerzos, `comentario` = «Pedido corporativo») = **10 %** de las ventas | Excluir o separar, y decirlo |
| Variable sesgada | `tiempo_espera_min` | Mediana / percentiles |
| Likert | `satisfaccion` (solo 35 % responde; 1:8, 2:47, 3:295, 4:843, 5:804) | % top-2-box o barras divergentes; advertir sesgo de no respuesta |
| Vacíos con significado | `tamano` (solo bebidas), `tiempo_espera_min` (domicilio) | No tratarlos como error ni como cero |
| Periodos desiguales | El Peñón, febrero, remodelación Granada, Centro sin domingos | Normalizar por día abierto |
| Muchas categorías | `producto` (24) | Nada de tortas; ordenar y agrupar |
| Texto libre | `comentario` | Conteo de temas (espera, precio, sabor…) con apoyo de IA |
| Escalas distintas | ventas (millones) vs tiempos (minutos) | Evitar doble eje; gráficos separados |
| Relación con dos tablas | hoja `sedes` (baristas, mesas, apertura) | Unir por `sede` para normalizar |

## Rúbrica sugerida (100 puntos)

| Criterio | Pts | Excelente |
|----------|-----|-----------|
| DESIGN.md completo y coherente con el tablero | 20 | Cada decisión del tablero está justificada en el archivo |
| Respuesta a preguntas de negocio | 25 | 3 preguntas respondidas con cifra, gráfico y decisión |
| Selección de gráficos | 15 | Intención ↔ gráfico; alternativas descartadas con razón |
| Color y ejes | 15 | Acento con intención, ceros, ordinales en orden, formatos COP |
| Narrativa y layout | 15 | Títulos-conclusión, jerarquía clara, KPIs pertinentes |
| Uso crítico de la IA | 10 | Bitácora muestra correcciones propias, no solo aceptación |
