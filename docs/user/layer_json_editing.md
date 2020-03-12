# Introducción

El árbol de capas del portal SNMB-Argentina se maneja desde el fichero `layers.json`. Por este motivo lo primero vamos a intentar explicar algunos conceptos para manejar este tipo de archivos.

Un fichero JSON es un conjunto de pares propiedad-valor. Tanto la propiedad como el valor van entre comillas dobles (" "), con dos puntos (:) entre los dos y, cada par, separados por comas. El conjunto tiene que ir dentro de llaves ({ }). Por ejemplo:

```json
{ "propiedad1": "valor1", "propiedad2": "valor2", ... }
```

Un array se asemeja al concepto matemático de vector. Representan valores múltiples para una misma propiedad. Los valores también van entre comillas dobles y separados por comas, pero en este caso van dentro de corchetes ([ ]). Ejemplo:

```json
"propiedad": ["valor1", "valor2",...]
```

Los valores de una propiedad pueden a su vez ser un JSON. Ejemplo:

```json
{"propiedad1": [{"propiedad1.1": "valor1.1",
                "propiedad1.2": "valor1.2"}....]}
```

Más ejemplos del caso que nos ocupa serían

JSON básico:

```json
{"id": "unique-id-1520610491279",
 "type": "wms",
 "label": ""
}
```

JSON con valores únicos y múltiples (array):

```json
{
    "items": [
        "departamentos",
        "unique-id-1520344804542",
        "limitenacional"
        ],
        "id": "admin",
        "label": "Áreas administrativas"
}
```

Valores JSON:

```json
{"wmsLayers": [
        {
            "queryType": "wms",
            "baseUrl": "http://ide.siia.gov.ar/geosever/wms",
            "label": "DEM SRTM 500X500 V1",
            "visible": true,
            "imageFormat": "image/png8",
            "id": "dem",
            "wmsName": "siia:MOSAICO_ARGENTINA_500x500_V1",
            "legend": "dem.png"
        },
        {
            "id": "modis",
            "type": "wms",
            "label": "",
            "sourceLink": "",
            "sourceLabel": "",
            "visible": true,
            "baseUrl": "https://tiles.maps.eox.at/wms"
        }]
}
```

El fichero layers.json tiene cuatro propiedades principales, `default-server`, `wmsLayers`, `portalLayers` y `groups`.

Más abajo se entran en detalles de cada una de las propiedades, pero para una primera aproximación diremos que, salvo `default-server`, que define el servidor que se usará en cado de que las capas no incluyan URL propias, las demás se encargan del manejo de las capas y la distribución de las mismas en el árbol.

De abajo a arriba dentro de la distribución de las propiedades en el fichero, la propiedad `groups` especifica las etiquetas iniciales del árbol. En este caso serían `Áreas Administrativas`, `Regiones forestales`, `Áreas de bosque` etc. Asimismo especifican los subgrupos (p.ej. `Bosque Nativo` y `Bosque cultivado` dentro del grupo `Áreas de bosque`) y las capas que van dentro de cada grupo o subgrupo.
La propiedad `portalLayers` especifica lo que es visible al usuario, tanto las capas propiamente dichas como las leyendas.
En la propiedad `wmsLayers` van todas las capas, 
leyendas del portal, etc, sean visibles al usuario o no.

## Edición del arbol de capas usando el editor
El portal está preparado para la edición del arbol de capas mediante la herramienta de edición del mismo. Lo primero que necesitaremos para poder empezar con la edición es tener cuenta de usuario con permisos de edición. Mediante el login deberemos acceder con nuestras credenciales:

![](images/login.png)

*Fig. 1. Dialogo de acceso con credenciales*

Una vez que estemos logueados con éxito veremos que nuestro arbol de capas tiene los controles necesarios para la edición:

![](images/layer_editor.gif)

*Fig. 2. Aparición de los controles de edición*

Utilizando los iconos de edición:

![](images/edition_icons.png)

*Fig. 3. Iconos de edición*

desplegaremos los formularios de creación ![](images/add.png) eliminación ![](images/remove.png) o edición ![](images/edition.png) o creación de un nuevo grupo ![](images/new_group.png)

Empezando por la creación de unn grupo, pulsaríamos sobre el botón nuvo grupo ![](images/new_group.png) y aparecerá el formulario de creación de nuevo grupo:

![](images/form_new_group.png)

*Fig. 4. Formulario de nuevo grupo*

Los campos a cubrir están descritos en la referencia [Arbol de Capas](../dev/layers.md).

En este caso lo que haremos será definir un identificador de grupo y una etiqueta de la siguiente manera

![](images/form_new_group_filled.png)

*Fig. 5. Formulario de nuevo grupo cubierto*

Seguidamente pulsaremos sobre el botón `Aplicar` para guardar los cambios en el arbol de capas.

!!! note

    Los cambios no serán grabados en el portal hasta que no se pulse sobre el botón guardar que se encuentra en la barra de navegación. 
    
!!! warning

    Si antes de guardar se refresca el portal se perderían las ediciones realizadas

![](images/new_group_created.png)

*Fig. 6. Nuevo grupo creado*

## Edición directa del archivo layers.json
Una manera de poder editar el arbol de capas es mediante la edición directa del archivo `layers.json`

### Incorporación de una nueva capa
Para incorporar una nueva capa hay que añadir un nuevo elemento JSON en el array de la propiedad `wms-layer`. Es muy importante la posición donde se inserta el nuevo elemento, ya que el orden de los elementos en el array definen el orden de las capas en el dibujado del mapa. En este caso, por ejemplo, las capas base deben ser las primeras para que las demás capas se pinten encima.

Una vez insertada en `wms-layer` hay que insertarla a su vez en `portalLayers`, de la misma manera que el paso anterior. Únicamente indicar que en esta propiedad no importa el orden.

La nueva capa insertada va a seguir el formato json, recordamos, pares propiedad-valor, tanto en `wms-layer` como en `portalLayers`. Las propiedades que lleva cada una se describen en el fichero [layers.md](../dev/layers.md). Ejemplos:

```json
{
    "wmsLayers": [
        {
            "id": "unique-id-1520344804542",
            "type": "wms",
            "legend": "auto",
            "label": "",
            "sourceLink": "",
            "sourceLabel": "",
            "visible": true,
            "baseUrl": "http://geo.ambiente.gob.ar/geoserver/ows",
            "wmsName": "bosques:provincias",
            .
            .
            .
        }]
}
```

```json
{"portalLayers": [
        {
             "layers": [
                "unique-id-1520344804542"
            ],
            "id": "unique-id-1520344804542",
            "label": "Límites provinciales",
            "infoFile": "ign.html",
            "infoLink": "",
            "inlineLegendUrl": "auto",
            .
            .
            .            
        }]
}
```

### Incorporación de un grupo
El procedimiento es el mismo que el explicado para la incorporación de capas, añadiendo la/s capa/s en la propiedad `groups`. Aquí vuelve a ser importante el orden de inserción del elemento ya que determina el orden de las etiquetas en el árbol. Simplemente sería añadir un nuevo JSON con una propiedad `items`, que es un array, los `id` de las capas que queremos que pertenezcan al grupo. Además hay que añadir las propiedades que se describen en el fichero [layers.md](../dev/layers.md), con habíamos comentado anteriormente. Por ejemplo:

```json
    "groups": [
        {
            "items": [
                "departamentos",
                "unique-id-1520344804542",
                "limitenacional"
            ],
            "id": "admin",
            "label": "Áreas administrativas"
        },
        .
        .
        .
]
```

### Incorporación de un subgrupo
Un subgrupo se incorpora dentro de un grupo. En la propiedad `items` del grupo, insertariamos el subgrupo de la misma forma que se inserta un grupo.

```json
"groups": [
        {
            "items": [
                {
                    "items": [
                        "selvamis_t",
                        "stb_t",
                        "pchbn06_3857",
                        "bap",
                        "espnan06",
                        "espcal06",
                        "mon98"
                    ],
                    "id": "innerbosquenativo",
                    "label": "Bosque Nativo"
                },
                {
                    "items": [
                        "unique-id-1521076972517",
                        "unique-id-1521077123304"
                    ],
                    "id": "innerbosquescultivados",
                    "label": "Bosque cultivado",
                    "infoFile": "",
                    "infoLink": ""
                }
            ],
            "id": "area_bosques",
            "infoFile": "bn_pc.docx.html",
            "label": "Áreas de bosque",
            "infoLink": ""
        }
        .
        .
        .
]
```