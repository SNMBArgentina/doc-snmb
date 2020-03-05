### Introducción

Las instrucciones que se detallan a continuación se realizan en el editor de capas, tanto al añadir una capa nueva como al editar una existente.

### Añadir icono de información y la documentación asociada

Para realizarlo se utilizan dos propiedades, `info File` e `info Link`. No es necesario utilizar las dos. De hecho si se define `info File`, `info Link` se ignorará.
En `info File` se define el nombre del fichero HTML que contiene la información de la capa. Este fichero HTML se localiza en la ruta `static/loc/{lang}/html`, siendo `{lang}` el idioma utilizado, por lo que, en este caso, habría que sustituirlo por `es`, `en` o `fr`.
En `info Link` se define la ruta completa (URL) al fichero HTML. Por ejemplo, para el idioma español habría que poner `static/loc/es/html/fichero.html`.

### Añadir icono de estadísticas

Para añadir el icono en una capa que tenga estadísticas, simplemente hay que activar la propiedad `Layer has stats` del editor.

### Añadir icono de descargas

Para añadir este icono hay que activar la propiedad `Enable download Layer` del editor

### Añadir icono de enlace a los metadatos

Para añadirlo hay que definir en la propiedad `Metadata Link` la URL completa al enlace de los metadatos.