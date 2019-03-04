# maps-geoweb
Práctica
## Descripción del proyecto
El objetivo del proyecto es realizar una aplicación web utilizando la librería open-source JavaScript Leaflet. En este caso, se trata de una mapa temático que representa el Índice de Desarrollo Humano de los páises. Para cada país, se puede consultar su nombre y el valor del índice, así como saber su nivel de desarrollo consultando la leyenda. 

El Índice de Desarrollo Humano (HDI por sus siglas en inglés) es un índicador del desarrollo de los países elaborado por el Programa de las Naciones Unidas para el Desarrollo (PNUD). Se trata de un indicador que integra mediciones referentes a tres dimensiones: salud, educación y nivel de vida. 

## Requisitos
Cargar en la cabecera de la página web la librería  leaflet.js (que contiene el código de la librería) y la hoja de estilo leaflet.css (que contiene la hoja de estilos de la librería).

```bash
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.4.0/dist/leaflet.css" />
        <script src="https://unpkg.com/leaflet@1.4.0/dist/leaflet.js"></script>
```
## Desarrollo de la aplicación

### Paso 1: Mapa base
- Como capa base se escoge [CartoDB.PositronNoLabels](http://leaflet-extras.github.io/leaflet-providers/preview/#filter=CartoDB.PositronNoLabels), un fondo de características neutras que se adapta bien a la elaboración de un mapa temático. 
- Entre los ajustes de entrada se introduce el nivel de zoom, las coordenadas para centrar el mapa y se habilita el control del zoom. 
 ```bash
var map, capa1;
            function init() {
                map = L.map("map", {
                    center: [14.971744967479937, 9.754996597712307],
                    zoom: 3,
                    zoomControl: true
                });
                capa1 = L.tileLayer('https://{s}.basemaps.cartocdn.com/light_nolabels/{z}/{x}/{y}{r}.png',
                    {
                        maxZoom: 8,
                        minZoom: 1,
                        attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors &copy; <a href="https://carto.com/attributions">CARTO</a>'
                    });
                capa1.addTo(map);
            }
```
### Paso 2: Preparación y carga de los datos
- Se parte de un archivo shape de los países del mundo descargado de la web [Natural Earth](https://www.naturalearthdata.com/downloads/10m-cultural-vectors/)
- Con QGIS se añaden, en la tabla de atributos del archivo shape, los campos HDI y NAME que se utilizarán respectivamente para la elaboración del temático y para rellenar el contenido de las ventanas emergentes al hacer clic en cada país. Los datos se obtienen del informe de [UNDP](http://hdr.undp.org/sites/default/files/2018_human_development_statistical_update.pdf) 2018. 
- Con QGIS se convierte el archivo shape en .geojson.
- El nuevo archivo .geojson se guarda en el mismo directorio que el documento HTML que estamos creando y se cambia la extensión del archivo a “countryhdi.js“.
- Abrimos el archivo “countryhdi.js“ y añadimos 
```bash
var countryhdi = { "type": "FeatureCollection",...}
```
- Mediante el paso anterior, hemos establecido que el contenido de nuestro archivo “countryhdi.js“ es igual a la variable "countryhdi". Ahora podemos cargar nuestro archivo con los datos en la cabecera de nuestro documento HTML.
```bash
<script type="text/javascript" src="countryhdi.js"></script>
```
- Finalmente, para añadir los datos al mapa usamos el siguiente código. 
```bash
L.geoJson(countryhdi, {style: style}).addTo(map);
```
### Paso 3: Visualización de los datos
- Elaboramos un mapa temático que asigna diferentes colores en función de los valores del HDI de los países. Escribimos la función, el return y los parámetros de visualización. 
```bash
function getColor(i) {
                return i > 0.798 ? '#5167b9' :
                i > 0.699  ? '#78dfc5' :
                i > 0.556  ? '#d79e9e' :
                i > 0.353  ? '#ee5656' :
                      '#ffffff';
            }

            function style(feature) {
                return {
                fillColor: getColor(feature.properties.HDI),
                weight: 1.5,
                opacity: 1,
                color: 'white',
                dashArray: '3',
                fillOpacity: 0.7
                };
            }
```
### Paso 4: Título
- Añadir un control para incluir el título del mapa. 
```bash
var info = L.control();

            info.onAdd = function (map) {
            this._div = L.DomUtil.create('div', 'info');
            this.update();
            return this._div;
            };

            info.update = function (props) {
            this._div.innerHTML = '<h1>Human Development Index</h1><br>Click on each country'
            };

            info.addTo(map);
```
- El título se ubica en un div en el cuerpo de la página web.
```bash
<div class="info"></div>
```
### Paso 5: Interacción del usuario con el mapa
- Incluir una funcionalidad que permita, al hacer clic sobre cada uno de los países, desplegar una ventana que contenga el nombre del país y el valor del HDI. El código utiliza la funcionalidad popup de Leaflet.
```bash
function popup(feature, layer) { 
                if (feature.properties && feature.properties.NAME) 
                { 
                layer.bindPopup(feature.properties.NAME); 
                } 
                }

            geojson = L.geoJson(countryhdi, { 
            style: style, onEachFeature: popup 
            }) 
            .addTo(map);
```
### Paso 6: Leyenda
- Añadir un control para incluir la leyenda del mapa, la cual se carga como una imagen estática (archivo map-hdi.jpg).
```bash
var legend = L.control({position: 'bottomleft'});
 
            legend.onAdd = function (map) {
 
            this._div = L.DomUtil.create('div', 'info-legend');
            this.update();
            return this._div;
            };

            legend.update = function (props) {
            this._div.innerHTML = '<b><img src="hdilegend.jpg"/></b>'
            };

            legend.addTo(map);
```
- La leyenda se ubica en un div en el cuerpo de la página web.
```bash
<div class="info-legend"></div>
```
## Referencias
- [Leaflet Tutorials](https://leafletjs.com/examples/choropleth/)
- [Mappinggis](https://mappinggis.com/2013/08/como-crear-un-mapa-web-a-partir-de-un-shapefile/)

## Licencia
[MIT](https://choosealicense.com/licenses/mit/)