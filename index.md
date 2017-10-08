
# Accidentalidad en Bogotá en el año 2016 

### Definición de Accidentes de Tránsito

Accidentes de Tránsito: Definiciones de la Ley 769 de 2002 – Código Nacional de Tránsito
– “Evento generalmente involuntario, generado al menos por un vehículo en movimiento, que causa daños a personas y bienes involucrados en él e igualmente afecta la normal circulación de los vehículos que se movilizan por la vía o vías comprendidas en el lugar o dentro de la zona de influencia del hecho.”

### ¿Qué hay en este Conjunto de Datos?

#### [ACCIDENTES BOGOTÁ 2016](https://www.datos.gov.co/Transporte/2016-ACCIDENTES-DE-TR-NSITO-BOGOT-/79fi-zm8c)

El datasset elegido esta compuesto por 34931 Filas y 38 Columnas que presentan la información de todos los accidentes registrados en la ciudad de Bogotá durante el año 2016.  Se presentan caracterizaciones del tipo de accidente, gravedad, día y hora de ocurrencia, localidad y otros atributos de interes frente a los datos. 

La fecha de carga del dataset fue el 11 de mayo de 2017 y fué la única carga que se hizo.

### Lo que se espera lograr:
○ Comparar los días en los cuales se presentaron más accidentes durante el año 2016 en la ciudad de Bogotá.
○ Presentar cuáles fueron las localidades de Bogotá con mayor cantidad de accidentes para los distintos meses del año 2016.

Hipotesis 1: Las localidades con mayor canidad de accidentes son las que mayor población residente tienen.
Hipotesis 2: Existen meses del año y días en los cuales se presentan mayor cantidad de accidentes.

#$# Análisis e Insights
         	 
<title>Accidentalidad por localidad en Bogotá en el año 2016</title>
<html lang="en">
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
</style>
<div>
<h4>Accidentalidad por localidad en Bogotá en el año 2016</h4>
</div>
<svg width="640" height="500"></svg>
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

});

function type(d, _, columns) {
  d.MES  = parseTime(d.MES);
  for (var i = 1, n = columns.length, c; i < n; ++i) d[c = columns[i]] = +d[c];
  return d;
}

</script>
</html>


Podemos observar que para el año 2016 existe una clara evidencia de cuáles fueron las localidades con mayor y menor cantidad de accidentes reportados, así vemos que la clara tendencia es que la localidad con menos cantidad de accidentes reportados es la localidad de La Candelaria, la cual presenta por gran diferencia muchos menos accidentes que las demás localidades, y es un comportamiento que se mantiene a lo largo de todo el año.   Así mismo se puede ver que la localidad que mayor cantidad de accidentes reportó fue la localidad de Kennedy, que junto con la localidad de Suba y Engativa reportaron una cifra cercana a los 220 accidentes al iniciar el año, y posteriormente presentaron un incremento más notorio en los meses de mayo y noviembre para Kennedy, y en abril, julio y octubre para Engativa.   

De acuerdo con nuestra gráfica podemos decir que varias de las localidades permanecieron con una cifra entre los 50 y 100 accidentes mensuales a lo largo del año, como es el caso de Tunjuelito, Rafael Uribe Uribe, Santa Fe, San Cristobal, Antonio Nariño y Usme. Y localidades como Fontibón, Puente Aranda y Chapinero fluctúan entre los 100 y 250 accidentes mensuales a lo largo del año. 

De igual manera podemos ver en la siguiente gráfica cómo estuvieron distribuidos los accidentes a los largo del año en toda la ciudad, y al igual que en la gráfica anterior es de notar que hacia el final de año, en los meses de octubre, noviembre y diciembre se presentaron la mayor cantidad de accidentes. 

<div>
<h4>Accidentalidad diaria en 2016</h4>
</div>

<style>
#calendar {
  margin: 20px;
}
.month {
  margin-right: 8px;
}
.month-name {
  font-size: 85%;
  fill: #777;
  font-family: Arial, Helvetica;
}
.day.hover {
  stroke: #6d6E70;
  stroke-width: 2;
}
.day.focus {
  stroke: #ffff33;
  stroke-width: 2;
}
</style>
<body>

<div id="calendar"></div>

<script src="https://d3js.org/d3.v4.min.js"></script>
<script src="https://d3js.org/d3-scale-chromatic.v1.min.js"></script>
<script>

function drawCalendar(dateData){

  var weeksInMonth = function(month){
    var m = d3.timeMonth.floor(month)
    return d3.timeWeeks(d3.timeWeek.floor(m), d3.timeMonth.offset(m,1)).length;
  }

// elegir las fechas mínimas y máximas a mstrar en el calendario de acuerdo con los datos
  var minDate = d3.min(dateData, function(d) { return new Date(d.Fecha) })
  var maxDate = d3.max(dateData, function(d) { return new Date(d.Fecha) })

  var cellMargin = 2,
      cellSize = 20;

  var day = d3.timeFormat("%w"),
      week = d3.timeFormat("%U"),
      format = d3.timeFormat("%d-%m-%Y"),
      titleFormat = d3.utcFormat("%a, %d-%b");
      monthName = d3.timeFormat("%B"), // Formato del nombre del mes
      months= d3.timeMonth.range(d3.timeMonth.floor(minDate), maxDate);

  
    var svg = d3.select("#calendar").selectAll("svg")
    .data(months)
    .enter().append("svg")
      .attr("class", "month")
      .attr("width", (cellSize * 7) + (cellMargin * 8) )
      .attr("height", function(d) {
        var rows = weeksInMonth(d);
        return (cellSize * rows) + (cellMargin * (rows + 1)) + 20; // the 20 is for the month labels
      })
    .append("g")

    svg.append("text")
    .attr("class", "month-name")
    .attr("x", ((cellSize * 7) + (cellMargin * 8)) / 2 )
    .attr("y", 15)
    .attr("text-anchor", "middle")
    .text(function(d) { return monthName(d); })

    var rect = svg.selectAll("rect.day")
    .data(function(d, i) {
      return d3.timeDays(d, new Date(d.getFullYear(), d.getMonth()+1, 1));
    })
    .enter().append("rect")
      .attr("class", "day")
      .attr("width", cellSize)
      .attr("height", cellSize)
      .attr("rx", 3).attr("ry", 3) // rounded corners
      .attr("fill", '#eaeaea') // default light grey fill
      .attr("x", function(d) {
        return (day(d) * cellSize) + (day(d) * cellMargin) + cellMargin;
      })
      .attr("y", function(d) {
        return ((week(d) - week(new Date(d.getFullYear(),d.getMonth(),1))) * cellSize) +
               ((week(d) - week(new Date(d.getFullYear(),d.getMonth(),1))) * cellMargin) +
               cellMargin + 20;
       })
      .on("mouseover", function(d) {
        d3.select(this).classed('hover', true);
      })
      .on("mouseout", function(d) {
        d3.select(this).classed('hover', false);
      })
      .datum(format);

  rect.append("title")
    .text(function(d) { return titleFormat(new Date(d)); });

  var lookup = d3.nest()
    .key(function(d) { return d.Fecha; })
    .rollup(function(leaves) {
      return d3.sum(leaves, function(d){ return parseInt(d.Total); });
    })
    .object(dateData);

   var scale = d3.scaleLinear()
    .domain(d3.extent(dateData, function(d) { return parseInt(d.Total); }))
    .range([0,1]); // Se deja el rango completo para que se marque mas la diferencia de colores

    var formatTime = function(input, formatInput, formatOutput){
    var dateParse = d3.timeParse(formatInput);
    var dateFormat = d3.timeFormat(formatOutput);
    return dateFormat(dateParse(input));
};

  rect.filter(function(d) { return d in lookup; })
    .style("fill", function(d) { return d3.interpolatePuBu(scale(lookup[d])); })
    .select("title")
    .text(function(d) { 
    return formatTime(d,"%d-%m-%Y","%a, %d-%b") + ":  " + lookup[d]; });
}

d3.csv("accidentes_diarios_bogota.csv", function(response){
  drawCalendar(response);
})
</script>

De acuerdo con la Secretaría Distrital de Planeación en su [Boletin 69 de Proyecciones de población por Localidades] http://www.sdp.gov.co/portal/page/portal/PortalSDP/InformacionTomaDecisiones/Estadisticas/Bogot%E1%20Ciudad%20de%20Estad%EDsticas/2014/Bolet%EDn69.pdf se indica que cinco las localidades con mayor población proyectada para 2016 fueron Suba(1.250.734), Kennedy (1.187.315), Usaquen (472.908), Engativa (873.243) y Fontibón (403.519), lo cual nos permite validar nuestra hipótesis de que las localidades con mayor población son las que reportan mayor cantidad de accidentes. 

<div>
          	<p>Vivian Lucia Aranda</p>
			<p>{vl.aranda140}@uniandes.edu.co</p>
			<p>ISIS4822 – Visual Analytics - 20172 </p>			
            <p>Universidad de los Andes, Bogotá, Colombia</p>
      </div>
