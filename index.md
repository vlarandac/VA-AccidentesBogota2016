
## Definición de Accidentes de Tránsito

Accidentes de Tránsito: Definiciones de la Ley 769 de 2002 – Código Nacional de Tránsito
– “Evento generalmente involuntario, generado al menos por un vehículo en movimiento, que causa daños a personas y bienes involucrados en él e igualmente afecta la normal
circulación de los vehículos que se movilizan por la vía o vías comprendidas en el lugar o dentro de la zona de influencia del hecho.”

# Descripción de los datos

## [ACCIDENTES BOGOTÁ 2016](https://www.datos.gov.co/Transporte/2016-ACCIDENTES-DE-TR-NSITO-BOGOT-/79fi-zm8c)

### ¿Qué hay en este Conjunto de Datos?
El datasset elegido esta compuesto por 34931 Filas y 38 Columnas que presentan la información de todos los accidentes registrados en la ciudad de Bogotá durante el año 2016.  Se presentan caracterizaciones del tipo de accidente, gravedad, día y hora de ocurrencia, localidad y otrso atributos de interes frente a los datos. 

La fecha de carga del dataset fue el 11 de mayo de 2017 y fué la única carga que se hizo.

## Lo que se espera conseguir con la visualización:
○ Comparar los días de la semana en las cuales se presentaron más accidentes durante el año 2016 en la ciudad de Bogotá.
○ Presentar cuáles fueron las localidades de Bogotá con mayor cantidad de accidentes para los distintos meses del año 2016.

Hipotesis: las localidades con mayor población presentan mayor cantidad de accidentes.

<!DOCTYPE html>
<meta charset="utf-8">
<style>

.axis--x path {
  display: none;
}

.line {
  fill: none;
  stroke: steelblue;
  stroke-width: 1.5px;
}

 circle {
    fill: white;
    stroke: #ed1e79; 
    stroke-width: 3;
}
.tooltip {
    background: none repeat scroll 0 0 #ed1e79;
    border: 0 solid #ffffff;
    border-radius: 8px;
    color: #ffffff;
    font: 12px sans-serif;
    height: 38px;
    padding: 2px;
    pointer-events: none;
    position: absolute;
    text-align: center;
    width: 90px;
}

</style>
<svg width="960" height="500"></svg>
<script src="//d3js.org/d3.v4.min.js"></script>
<script>

// definición de la variable que contendra el elemneto SVG y sus dimensiones
var svg = d3.select("svg"),
// configuración de las dimensiones y márgenes del gráfico
    margin = {top: 20, right: 80, bottom: 30, left: 50},
    width = svg.attr("width") - margin.left - margin.right,
    height = svg.attr("height") - margin.top - margin.bottom,
    // creación del elemento de tipo svg  (g)
    g = svg.append("g").attr("transform", "translate(" + margin.left + "," + margin.top + ")");

// definición del tipo de dato asociado al mes 
var parseTime = d3.timeParse("%m %Y");

// definición de las escalas
var x = d3.scaleTime().range([0, width]), // escala de tiempo
    y = d3.scaleLinear().range([height, 0]), // escala lineal
    z = d3.scaleOrdinal(d3.schemeCategory10); // rango de colores  usar

  // definición de nombres en español }  
  var es_ES = {
        "decimal": ",",
        "thousands": ".",
        "grouping": [3],
        "currency": ["€", ""],
        "dateTime": "%a %b %e %X %Y",
        "date": "%d/%m/%Y",
        "time": "%H:%M:%S",
        "periods": ["AM", "PM"],
        "days": ["Domingo", "Lunes", "Martes", "Miércoles", "Jueves", "Viernes", "Sábado"],
        "shortDays": ["Dom", "Lun", "Mar", "Mi", "Jue", "Vie", "Sab"],
        "months": ["Enero", "Febrero", "Marzo", "Abril", "Mayo", "Junio", "Julio", "Agosto", "Septiembre", "Octubre", "Noviembre", "Diciembre"],
        "shortMonths": ["Ene", "Feb", "Mar", "Abr", "May", "Jun", "Jul", "Ago", "Sep", "Oct", "Nov", "Dic"]
    };

    var ES = d3.timeFormatLocale(es_ES);
// tipo de linea a utilizar en el gráfico y caracterización de los atributos mes y cantidad de accidentes.
var line = d3.line()
    .curve(d3.curveBasis) // tipo de línea
    .x(function(d) { return x(d.MES); })
    .y(function(d) { return y(d.accidentes); });

// lectura de los datos desde archivo .tsv
d3.tsv("accidentes_localidad_MES.tsv", type, function(error, data) {
  if (error) throw error;

// definición de la variable que establece las líneas a pintar, para nuestro caso las localidades
  var localidades = data.columns.slice(1).map(function(id) {
    return {
      id: id,
      values: data.map(function(d) {
        return {MES : d.MES , accidentes : d[id]}; }) }; // cantidad de accidentes por localidad para cada mes
  });

  // definición del dominio para el eje de las x, dado por los meses en términos de línea de tiempo
  x.domain(d3.extent(data, function(d) { return d.MES; }));
  // definición del dominio para el eje de las y, dado por las localidades 
  // devuelve la cantidad de accidentes por localidad por cada mes
  y.domain([
    d3.min(localidades, function(c) { return d3.min(c.values, function(d) { return d.accidentes; }); }),
    d3.max(localidades, function(c) { return d3.max(c.values, function(d) { return d.accidentes; }); })
  ]);
  // definición del dominio de las Z el cual nos da el dato para la línea
  z.domain(localidades.map(function(c) { return c.id; }));

  // eje x
  g.append("g")
      .attr("class", "axis axis--x")
      .attr("transform", "translate(0," + height + ")")
      .call(d3.axisBottom(x));

  // eje y, el rango esta dado por los valores mínimo y máximo de la cantidad de accidentes por mes por localidad
  g.append("g")
      .attr("class", "axis axis--y")
      .call(d3.axisLeft(y))
    .append("text")
      .attr("transform", "rotate(-90)")
      .attr("y", 3)
      .attr("dy", "0.71em")
      .attr("fill", "#000")
      .text("Accidentes");
  
  // variable con nombres de localidades para ser pintadas 
  var local = g.selectAll(".local")
    .data(localidades)
    .enter().append("g")
      .attr("class", "local");
  
  // path de lineas por localidad con datos de cantidad de accidentes
  local.append("path")
      .attr("class", "line")
      .attr("d", function(d) { return line(d.values); })
      .style("stroke", function(d) { return z(d.id); });
  
  // texto de las líneas
  local.append("text")
      .datum(function(d) { return {id: d.id, value: d.values[d.values.length - 1]}; })
      .attr("transform", function(d) { return "translate(" + x(d.value.MES) + "," + y(d.value.accidentes) + ")"; })
      .attr("x", 1.5)
      .attr("dy", "0.35em")
      .style("font", "8px sans-serif")
      .text(function(d) { return d.id; });

  svg.selectAll("g.dot")
        .data(data)
        .enter().append("g")
        .attr("class", "dot")
        .selectAll("circle")
        .data(function(d) { return d.values; })
        .enter().append("circle")
        .attr("r", 5)
        .attr("cx", function(d,i) {  return x(d.MES); })
        .attr("cy", function(d,i) { return y(d.value.accidentes); })

// Tooltip stuff after this
      .on("mouseover", function(d) {              // when the mouse goes over a circle, do the following
      div.transition()                  // declare the transition properties to bring fade-in div
        .duration(200)                  // it shall take 200ms
        .style("opacity", .9);              // and go all the way to an opacity of .9
      div .html(d.localidades + "<br/>" + d.MES + "<br/>"  + d.value.accidentes)  // add the text of the tooltip as html 
        .style("left", (d3.event.pageX) + "px")     // move it in the x direction 
        .style("top", (d3.event.pageY - 28) + "px");  // move it in the y direction
      })                          // 
    .on("mouseout", function(d) {             // when the mouse leaves a circle, do the following
      div.transition()                  // declare the transition properties to fade-out the div
        .duration(500)                  // it shall take 500ms
        .style("opacity", 0);             // and go all the way to an opacity of nil
    });   

});

function type(d, _, columns) {
  d.MES  = parseTime(d.MES);
  for (var i = 1, n = columns.length, c; i < n; ++i) d[c = columns[i]] = +d[c];
  return d;
}

</script>
## Insights

Podemos observar que para el año 2016 existe una clara evidencia de cuáles fueron las localidades con mayor y menor cantidad de accidentes reportados, así vemos que la clara tendencia es que la localidad con menos cantidad de accidentes reportados es la localidad de La Candelaria, la cual presenta por gran diferencia muchos menos accidentes que las demás localidades, y es un comportamiento que se mantiene a lo largo de todo el año.   Así mismo se puede ver que la localidad que mayor cantidad de accidentes reportó fue la localidad de Kennedy, que junto con la localidad de Suba y Engativa reportaron una cifra cercana a los 220 accidentes al iniciar el año, y posteriormente presentaron un incremento más notorio en los meses de mayo y noviembre para Keneddy, y en abril, julio y octubre para Engativa.   

De acuerdo con nuestra gráfica podemos decir que varis de las localidades permanecieron con una cifra entre los 50 y 100 accidentes mensuales a lo largo del año, como es el caso de Tunjuelito, Rafael Uribe Uribe, Santa Fe, San Cristobal, Antonio Nariño y Usme. Y localidades como Suba, Fontibón, Puente Aranda y Chapinero fluctúan entre los 150 y 250 accidentes mensuales a lo largo del año. 
