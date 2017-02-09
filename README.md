# PDAM-03

En este laboratiorio nos enfocaremos en los plugins que pueden usarse para acceder a funcionalidades mas avanzadas
como push notifications o geo localización. Esto servirá de guía para el proyecto de la aplicación híbrida

# Configuración del proyecto base de ionic

Para comenzar instale un proyecto ionic con un template de prueba desde la linea de comandos:

```bash
ionic start pdam-3 sidemenu
```

Esto generará la aplicación base dentro de la carpeta pdam-3 con un menu lateral. confirme que la instalación 
haya sido exitosa corriendo la aplicación en modo web.

## Uso de Google maps

Para usar el API de Google maps, es necesario crear las credenciales indicadas. Siga las instrucciones en 
[este](https://developers.google.com/maps/documentation/javascript/get-api-key) link para crearlas. 

Debe obtener un código similar al siguiente, esta credencial no es valida, solo sirve como ejemplo:

```bash
AIzaSygO5HcSBk9arJFACQ5yepCKNospBtM3a4o
```

### Añadir un mapa a la aplicación

En el archivo index.html inserte el siguiente código para cargar la libreria de Google maps, incluya el código generado
en el punto anterior

```html
<script src="http://maps.google.com/maps/api/js?key=YOUR_API_KEY_GOES_HERE"></script>
...
```

Añada un item al menu como se hizo en el laboratorio pasado, el item debe llamarse "Mapa", recuerde añadir la ruta y el 
controlador respectivos para que el elemento pueda ser navegado desde el menu lateral.

En la vista que se destinó para el mapa, inserte el siguiente código HTML para incluir el mapa de Google.

```html
<ion-content>
    <div id="map" data-tap-disabled="true"></div>
...
```

En el controlador añada el codigo de inicialización del mapa

```javascript
var mapOptions = {
    center: new google.maps.LatLng(4.624335, -74.063644),
    zoom: 15,
    mapTypeId: google.maps.MapTypeId.ROADMAP
};

$scope.map = new google.maps.Map(document.getElementById("map"), mapOptions);
```

En el archivo style.css, añada el estilo CSS necesario para que el mapa llene la vista de la aplicación

 ```css
 .scroll {
     height: 100%;
 }
 
 #map {
     width: 100%;
     height: 100%;
 }
 ```

El resultado debe ser:

![alt text](http://gabo.com.co/pdam/lab-03-01.png)

## Añadir Geolocalización

Para permitir que el usuario pueda usar el GPS y podamos ubicarlo en el mapa, debemos usar un plugin de cordova que nos dará
acceso al hardware del dispositivo, con los permisos necesarios.

Para iniciar instalaremos el modulo de angular ngCordova, que nos permite usar los plugins de cordova en angular

```bash
bower install ngCordova --save
```

Si bower no funciona correctamente en su equipo, descargue la libreria en [este](http://gabo.com.co/pdam/ngCordova.zip) 
link y copiela dentro de la carpeta "www/lib"

Una vez tenga la libreria debe cargar el archivo javascript e inyectarla en su aplicación, en el index.html:

```html
<script src="lib/ngCordova/dist/ng-cordova.min.js"></script>
```

y en el app.js:

```javascript
angular.module('starter', ['ionic', 'starter.controllers', 'ngCordova'])
```

Para que sea posible interactuar con el hardware del device es necesario instalar el [plugin de geolocalización](http://ngcordova.com/docs/plugins/geolocation/)
en la raíz de su aplicación de ionic, ejecute el siguiente comando

```bash
cordova plugin add cordova-plugin-geolocation
```

Si la instalación no se ejectuta correctamente puede descargar los plugins en [este](http://gabo.com.co/pdam/plugins.zip) 
link y ubicarlos dentro de la carpeta "plugins"

En el controlador del mapa, incluiremos el código necesario para obtener la información de geolocalización. Inyecte
el modulo de geolocalización ($cordovaGeolocation) en su controlador 

```javascript
var options = {timeout: 10000, enableHighAccuracy: true};

$cordovaGeolocation.getCurrentPosition(options).then(function (position) {
    var latLng = new google.maps.LatLng(position.coords.latitude, position.coords.longitude);

    var mapOptions = {
        center: latLng,
        zoom: 15,
        mapTypeId: google.maps.MapTypeId.ROADMAP
    };

    var map = new google.maps.Map(document.getElementById("map"), mapOptions);

    var marker = new google.maps.Marker({position:latLng,  map: map});

    $scope.map = map;

}, function (error) {
    console.log("Could not get location");
});
```

Ahora el mapa ubicara el centro en nuestra posición actual con un marcador.

## Añadir Soporte para Fotografías

Para usar la cámara del dispositivo en un app, debemos instalar otro plugin que nos permitira interactuar con el hardware

```bash
cordova plugin add cordova-plugin-camera
```

Si el plugin no instala correctamente puede descargar la carpeta de plugins en [este](http://gabo.com.co/pdam/plugins.zip) link

Construya una vista para la cámara, la vista debe poderse navegar desde el menu lateral. Dentro del controlador de esta nueva
sección debe inyectar el modulo para el manejo de la camara. 

El resultado final debe ser:

![alt text](http://i.giphy.com/l3q2Y8KoFlX3cCzx6.gif)

Para guiarse puede usar la documentación del plugin en [este](http://ngcordova.com/docs/plugins/camera/) link.

Para previsualizar la foto, utilize la opción 

```javascript
...
destinationType: Camera.DestinationType.DATA_URL,
...
```

Esta configuración devolverá la imagen como un string en Base64, puede utilizar la directiva ng-src para asignar esta 
información a un elemento HTML de imagen

```html
<img id="picture" ng-src="{{pictureData}}">
```

Tenga en cuenta que la cámara solamente se podrá probar desde un dispositivo real.
