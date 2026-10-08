# DESIGN.md — Tablero Café Farallones (ejemplo resuelto)

> Ejemplo de referencia para el docente. Muestra un DESIGN.md completo y el razonamiento detrás de cada decisión. El tablero `index.html` de esta carpeta implementa exactamente lo que dice este archivo.

**Equipo:** Ejemplo docente
**Fecha:** 7 de octubre de 2026
**Versión:** v2 (v1 usaba ventas totales en P2; se corrigió tras la crítica)

---

## 1. Audiencia y propósito

- **¿Quién usa el tablero?** La gerente de operaciones de Café Farallones.
- **¿Qué decisión debe tomar con él?** Dónde poner personal y dinero en el segundo semestre: un barista nuevo, el futuro de El Peñón y la política de descuentos de la App.
- **¿Cuánto tiempo le dedica?** Dos minutos en la reunión del lunes, proyectado en pantalla.
- **Mensaje principal (big idea):** Centro necesita un barista al almuerzo, El Peñón ya vende al nivel de las demás sedes y la App crece mientras las sedes antiguas venden menos por día, así que el descuento merece revisión. Las otras cuatro decisiones (lluvia, menú, turnos y fidelización) se resumen al inicio del tablero.

> **Por qué:** el mensaje principal se escribe antes que cualquier gráfico. Si no cabe en una oración, el tablero todavía no tiene foco.

## 2. Preguntas de negocio elegidas

| # | Pregunta | Decisión que habilita | Métrica clave |
|---|----------|----------------------|---------------|
| P1 | ¿En qué sede y franja contratamos un barista adicional? | Asignar el nuevo cargo | Mediana de espera (min) y % de calificaciones 1–2 |
| P2 | ¿Fue un error abrir El Peñón? | Mantener, ajustar o cerrar la sede | Ventas por día abierto, por mes |
| P3 | ¿Mantenemos el 15 % de descuento de la App? | Política de descuentos | Participación de la App, descuento otorgado, ventas/día de sedes antiguas |
| P4 | ¿Qué cambiamos en días de lluvia? | Plan operativo de lluvias | % de domicilios y de cada categoría según lluvia; % de bebidas frías por tramo de temperatura |
| P5 | ¿Qué productos salen del menú? | Nuevo menú | % de ventas y % de unidades por producto |
| P6 | ¿Cómo redistribuimos el personal por día de la semana? | Turnos por sede | Ventas por día abierto, sede × día |
| P7 | ¿Quiénes son los clientes de mayor valor? | Foco de fidelización | Ticket por tipo de cliente y edad; Likert agrupada |

> **Por qué las siete:** el ejercicio pide tres; este ejemplo resuelve todas para que sirva de referencia. Un estudiante que elija P1, P2 y P3 cumple la regla de selección: P1 usa una ordinal (franja) y una cualitativa difícil (satisfacción Likert); P2 es temporal y compara sedes; P3 es temporal y de composición.

## 3. Inventario de datos

| Columna | Tipo | Rol en el tablero | Problema detectado | Decisión |
|---------|------|------------------|--------------------|----------|
| `fecha` | temporal | Base para contar días abiertos | Sedes con distintos días de operación | Contar días únicos con ventas por sede y mes |
| `mes` | temporal (ordinal) | Dimensión de P2 y P3 | `nombre_mes` se ordena alfabéticamente | Usar `mes` numérico para ordenar y mostrar abreviatura |
| `dia_semana` | ordinal | No se usa en este tablero | Orden alfabético | Si se usa: Lunes → Domingo |
| `franja_horaria` | ordinal | Columna del mapa de calor | Orden alfabético rompe el día | Orden fijo: Mañana, Media mañana, Almuerzo, Tarde, Noche |
| `sede` | nominal | Dimensión principal | Distintas fechas de apertura y cierres | Comparar por día abierto; unir con hoja `sedes` |
| `canal` | nominal | Composición en P3; filtro en P1 | Domicilio no tiene espera | Excluir Domicilio de P1 |
| `cantidad` | discreta | Regla de atípicos | 12 líneas con 25–60 unidades | Línea con ≥ 20 unidades = pedido corporativo |
| `precio_unitario` | continua | Cálculo del descuento | — | Descuento = precio × cantidad − total |
| `descuento_pct` | discreta | Contexto de P3 | — | — |
| `total_venta` | continua | Medida de ventas | Los corporativos son el 10 % del total | Excluirlos por defecto, con interruptor |
| `tiempo_espera_min` | continua (sesgada) | Medida de P1 | Cola larga a la derecha; vacío en Domicilio | Mediana y tramos; vacío ≠ 0 |
| `satisfaccion` | ordinal (Likert 1–5) | Medida de P1 | Solo responde el 35 %; no es continua | % de 1–2 y % de 4–5; advertir tasa de respuesta |
| `dia_semana` (P6) | ordinal | Columnas del mapa de calor de turnos | Orden alfabético; lunes festivos frecuentes | Lunes → Domingo; ventas por día abierto |
| `categoria` | nominal | Mezcla de ventas en P4 | — | Orden fijo en ambos grupos |
| `producto` | nominal (24 valores) | Pareto en P5 | Demasiadas categorías para color o torta | Barras horizontales ordenadas; color = recomendación |
| `tipo_cliente` | ordinal | P7 | Se ordena alfabéticamente | Nuevo → Recurrente → Fidelizado |
| `rango_edad` | ordinal | P7 | «Menos de 18» queda al final en orden alfabético | Orden fijo de menor a mayor edad |
| `temperatura_c` | continua | P4 | 181 valores diarios, dispersión ruidosa | Tramos ≤ 26, 26–28, 28–30, > 30 °C |
| `lluvia` | nominal (Sí/No) | P4 | Pocos días de lluvia frente a días secos | Proporciones dentro de cada tipo de día |
| `tamano` | ordinal | No se usa | Vacío en no bebidas | — |
| `comentario` | texto libre | No se usa (siguiente versión) | — | — |

**Reglas de limpieza y cálculo:**

- Pedido corporativo = `cantidad ≥ 20`. Se excluye de todos los cálculos por defecto; el interruptor «Incluir pedidos corporativos» lo devuelve.
- Ventas por día = suma de `total_venta` ÷ número de fechas distintas con ventas, en el mismo grupo (sede, mes o sede × mes).
- Espera: mediana, solo canales Mostrador y App.
- Satisfacción: nunca promedio. % de respuestas 1–2 (insatisfechos) o 4–5 (satisfechos), sobre quienes respondieron.
- «Sedes antiguas» = todas menos El Peñón, para que la sede nueva no esconda cambios en las demás.
- Producto candidato a salir: aporta menos del 2 % de las ventas. Si además aporta 4 % o más de las unidades, se queda por generar tráfico.
- Sede «de semana» = mayor relación ventas entre semana / fin de semana; sede «de fin de semana» = mayor relación inversa.

## 4. Mapa pregunta → gráfico

| Pregunta | Variables | Intención | Gráfico elegido | Alternativa descartada y por qué |
|----------|-----------|-----------|-----------------|----------------------------------|
| P1 | sede × franja × espera | Encontrar un cruce | Mapa de calor con escala secuencial | Barras agrupadas 5 × 5: 25 barras, el ojo no encuentra el pico |
| P1 | espera (tramos) × satisfacción | Relación | Barras por tramo de espera | Dispersión: la Likert apila los puntos en 5 líneas |
| P2 | mes × sede × ventas/día | Tendencia | Líneas, una destacada | Barras de ventas totales: castigan a la sede nueva |
| P2 | sede × ventas/día (último mes) | Comparar | Barras horizontales ordenadas | Torta: no compara magnitudes cercanas |
| P3 | mes × canal | Composición en el tiempo | Barras apiladas al 100 % | Seis tortas: no se comparan entre sí |
| P3 | ticket, descuento, ventas/día | Cifra única | Tarjetas con cifra grande | Un gráfico de una sola barra no aporta |
| P4 | categoría × lluvia | Comparar mezclas | Barras agrupadas con proporciones | Conteos absolutos: los días secos dominan |
| P4 | temperatura (tramos) × bebidas frías | Relación | Barras por tramo con rampa ordinal | Dispersión de 181 días: ruido |
| P5 | producto × ventas | Ranking / Pareto | Barras horizontales ordenadas + tabla de candidatos | Torta de 24 porciones; doble eje con % acumulado |
| P6 | sede × día × ventas/día | Patrón 2D | Mapa de calor con cifras | Cinco líneas cruzadas; totales en orden alfabético |
| P7 | tipo de cliente × ticket | Comparar | Barras, Fidelizado destacado | Torta de tipos de cliente |
| P7 | tipo de cliente × Likert | Distribución | Barras apiladas al 100 % (1–2 / 3 / 4–5) | Promedio con eje truncado |
| P7 | edad × ticket | Comparar ordinales | Barras en orden de edad, mediana | Orden alfabético de rangos |

**Títulos-conclusión** (se calculan con los datos cargados):

- P1: «Centro hace esperar 10,7 minutos al almuerzo, casi el doble que las demás sedes» (no «Tiempo de espera por sede»).
- P2: «El Peñón pasó de $51 mil a $72 mil por día y ya está cerca de las sedes consolidadas».
- P3: «La App ya es 28 % de las ventas, pero las sedes antiguas venden 8 % menos por día que en enero».
- P4: «Con lluvia, los domicilios pasan de 12 % a 23 % y el café caliente de 30 % a 44 % de las ventas».
- P5: «Los 10 productos más vendidos dejan el 68 % de los ingresos; 4 productos pueden salir del menú».
- P6: «Centro vive de lunes a viernes y Granada del fin de semana».
- P7: «Los clientes fidelizados gastan 15 % más por ticket que los nuevos y casi no se quejan».

> **Por qué títulos calculados:** si la gerente carga el archivo de julio, el título se actualiza. Un título fijo puede quedar mintiendo.

## 5. Sistema de color

- **Neutro base (contexto):** `#b5b6c4` — todo lo que no es el foco.
- **Color de acento:** `#5454e9` (azul Icesi) — se usa solo en: la sede en evaluación (El Peñón), el canal App y los valores positivos de KPIs.
- **Alerta:** `#e9683b` — la peor celda del mapa de calor, los tramos de espera sobre la meta, los productos que salen del menú, las calificaciones 1–2 y los KPIs fuera de rango.
- **Paleta categórica (canales):** App `#5454e9`, Domicilio `#f0a07f`, Mostrador `#b5b6c4`. Solo tres categorías.
- **Escala secuencial (mapas de calor y tramos de temperatura):** un solo tono azul, de `#e3e7fb` a `#33349a`, en 6 pasos.
- **Likert agrupada:** naranja (1–2) · gris claro `#dcdce6` (3) · azul (4–5): dos polos opuestos y un centro neutro.
- **Semántica fija:** App siempre azul; naranja siempre significa «requiere acción».
- **Accesibilidad:** el gris de contexto tiene bajo contraste con el fondo, así que todas las series llevan leyenda o etiqueta y tooltip. El mapa de calor muestra el número en cada celda, así que no depende solo del color. Azul y naranja se distinguen con daltonismo rojo-verde.

> **Por qué no cinco colores para cinco sedes:** cada pregunta trata de una sede. «Destacar una, apagar el resto» dirige la mirada; cinco colores la dispersan.

## 6. Ejes, escalas y formatos

- **Barras:** el eje Y inicia en cero, siempre. La longitud es el dato.
- **Orden de ordinales:** meses Ene → Jun; franjas Mañana → Noche; tramos de espera 0–4, 4–6, 6–8, 8–10, 10–15, 15+.
- **Orden de nominales:** sedes de mayor a menor ventas en el mapa de calor y en las barras horizontales.
- **Formato de números:** pesos con separador de miles y abreviados («$72 mil», «$1,9 M»); minutos con un decimal y coma decimal.
- **Huecos en el tiempo:** El Peñón no tiene puntos en enero y febrero. La línea empieza en marzo; nunca se dibuja un cero ni se interpola.
- **Líneas de referencia:** meta de espera de 8 minutos, expresada con color en los tramos (naranja = sobre la meta).
- **Una sola escala por gráfico.** No hay doble eje: ventas y tiempos van en gráficos separados.

## 7. Layout y jerarquía

```
┌────────────────────────────────────────────────────┐
│ Pregunta de la gerencia + mensaje principal   [Cargar] │
├────────────┬────────────┬────────────┬────────────┤
│ Ventas/día │ Espera     │ El Peñón   │ App        │
│ cadena     │ (alerta)   │ (en rango) │ (%)        │
├────────────┴────────────┴────────────┴────────────┤
│ Resumen: siete decisiones con enlace a su sección   │
├────────────────────────────────────────────────────┤
│ P1  título-conclusión                              │
│ [mapa de calor]          [barras por tramo]        │
│ Decisión · ▸ Por qué se diseñó así                 │
├────────────────────────────────────────────────────┤
│ P2  [líneas]             [barras horizontales]     │
├────────────────────────────────────────────────────┤
│ P3  [apiladas 100 %]     [4 cifras]                │
├────────────────────────────────────────────────────┤
│ P4  [cifras] [barras agrupadas] [tramos de temp.]  │
├────────────────────────────────────────────────────┤
│ P5  [Pareto horizontal]   [tabla de candidatos]    │
├────────────────────────────────────────────────────┤
│ P6  [mapa de calor sede × día, ancho completo]     │
├────────────────────────────────────────────────────┤
│ P7  [ticket] [Likert 100 %] / [ticket por edad]    │
├────────────────────────────────────────────────────┤
│ Trampas tratadas (tabla)                           │
└────────────────────────────────────────────────────┘
```

- **Lectura esperada:** mensaje principal → KPI en naranja (lo urgente) → resumen de decisiones → detalle de cada pregunta.
- **KPIs:** uno por pregunta más el contexto de la cadena. Cada KPI lleva una etiqueta de estado («Sobre la meta», «En rango») para no depender solo del color.
- **Qué dejé por fuera a propósito:** comentarios de texto libre y método de pago. No responden ninguna de las siete preguntas.

## 8. Interactividad

- **Filtro global:** interruptor «Incluir pedidos corporativos». Afecta a todos los gráficos y KPIs.
- **Tooltips:** en cada celda del mapa de calor (mediana, número de ventas, % de satisfechos y respuestas) y en todos los gráficos.
- **Modo didáctico:** botón «Ver la versión con error» en cada pregunta, que muestra el gráfico ingenuo para discutirlo en clase.
- **Estados vacíos y errores:** sin archivo, el tablero pide cargarlo; si faltan columnas, dice cuáles.

## 9. Requisitos técnicos

- Un solo `index.html`. Abre con doble clic; con servidor local o publicado, carga los datos de ejemplo automáticamente.
- Carga `.xlsx` (SheetJS, hoja `ventas`) o `.csv` (PapaParse).
- Gráficos con Chart.js 4; mapa de calor en HTML.
- Valida las 18 columnas que usa.
- Todo se calcula en el navegador a partir del archivo.

## 10. Bitácora de decisiones con IA

| Momento | Qué le pedí a la IA | Qué propuso | Qué acepté / corregí y por qué |
|---------|--------------------|-------------|--------------------------------|
| Exploración | Clasificar columnas | Marcó `satisfaccion` como continua | Corregí: es ordinal (Likert). Promediarla esconde la forma de la distribución |
| Exploración | Señalar problemas | Detectó los pedidos corporativos | Acepté y fijé el umbral en 20 unidades |
| Crítica | Revisar el DESIGN.md v1 | Advirtió que P2 comparaba totales con periodos distintos | Acepté: pasé a ventas por día abierto (v2) |
| Crítica | Revisar el DESIGN.md v1 | Sugirió cinco colores, uno por sede | Rechacé: la pregunta es sobre una sede; usé destacar y apagar |
| Construcción | Primer tablero | Meses en orden alfabético en P3 | Corregí citando la sección 6 |
| Construcción | Primer tablero | Ventas/día de toda la cadena en P3 | Corregí: la sede nueva escondía la caída de las antiguas |

## 11. Autoevaluación final

- [x] Cada gráfico responde una pregunta de la sección 2
- [x] Cada título es una conclusión
- [x] Los ordinales respetan su orden natural
- [x] Las barras parten de cero
- [x] El color de acento se usa con intención
- [x] No hay gráficos de torta (solo en la versión con error de P5, a propósito)
- [x] Las comparaciones entre sedes son justas (por día abierto)
- [x] Los valores atípicos están tratados y explicados
- [x] El tablero funciona con el .csv y con el .xlsx
