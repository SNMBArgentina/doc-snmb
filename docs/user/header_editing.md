### Introducción

Para realizar cambios en la imágen y el logo de la cabecera de la página (header) hay que editar el fichero `overrides.css`, que se encuentra en el directorio `app-snmb/static`. El fichero tiene cinco etiquetas `title`, `flag`, `banner`, `toolbar` y `body`. Su contenido es como sigue:

```css
#title {
  top: 15px;
  left: 400px;
  font-size: 25px;
  color: #084d0e;
}

#flag {
  position: relative;
  top: 12px;
  left: 12px;
  height: 69px;
  width: auto;
  z-index: 1100;
  background: url(../static/img/logoambiente.png) no-repeat 0 0;
  background-size: 330px 70px;
}

#banner {
    height: 92px;
    width: 100%;
    background-color: rgba(255,255,255,.9);
    background: url(../static/img/right.jpg) no-repeat 0 0;
    background-size: 100% 100%;
}

#toolbar {  
    background-color: rgba(0,0,0,0.3);
    height: 50px;
    z-index: 2000;
    position: relative;
    padding-right: 10px;
}

body {
  font: "Trebuchet MS", sans-serif;
}
```

### Cambiar la imagen de fondo

La imagen de fondo de la cabecera se maneja con la etiqueta `banner`. Para cambiarla hay copiar el fichero con la imagen nueva en el directorio `app-snmb/static/img` y modificar la 
propiedad `background` reflejando la ruta al nuevo fichero, es decir,

```css
#banner{
    .
    .
    background: url(../static/img/nuevaImagen.jpg) no-repeat 0 0;
    .
    .

}
```

Las otras etiquetas manejan el color de fondo y el tamaño de la imagen, por lo que, en principio, no se necesitaría modificarlas.

### Cambiar el logo

Para cambiar el logo se procedería igual que en el caso de la imagen, pero la modificación hay que realizarla en la etiqueta `flag`. Habría que insertar la imagen en la misma ruta (`app-snmb/static/img`) y modificar la propiedad `background` de la misma manera, quedando como sigue

```css
#flag{
    .
    .
    background: url(../static/img/nuevaImagen.jpg) no-repeat 0 0;
    .
    .
}
```

De la misma forma que en el caso anterior, las demás etiquetas manejan la posición del logo por lo que tampoco debería ser necesario modificarlas.
