# Node JS
#### Es un entorno de ejecución de javascrip creado en 2009 basado en "Chromium V8"

## ¿Para que lo Usamos?
#### Utiliza un modelo de entrada(solicitudes) y salida(respuestas) **sin bloqueo** controlado por eventos. Es decir, busca ser liviano y eficiente. Node es capaz de manejar múltiples conexiones, tiene una alta **escalabilidad** .


## ¿Como Funciona?
- Node Js al utilizar JavaScript trabaja de forma **asincrona**
 
- Tabmien utiliza Npm, un gestor de paquetes. Su función es la de traer librerías y ayudarnos a administrar nuestros módulos. Para instalar un modulo escribe en la consola: `npm install` y el modulo que quieras. Los modulos npm se enecuenctran en la [pagina oficial]:https://www.npmjs.com/
#### Ej:
~~~
npm install colors
~~~
>Sirve para poner colores a tu consola

~~~
var colors = require('colors/safe');
 
console.log(colors.green('hello'));
conole.log('chau');
~~~
> Hello sera printeado en verde mientras que chau en gris.

- Es mejor trabajar con **modulos**. Estos son subdiviciones de nuestros archivos en nuestra aplicacion. Se trabaja asi para que todo sea lo mas liviano posible.

___

## Instalacion
1. Abre tu navegador y dirjete a la pagina oficial de [node js]:https://nodejs.org/es/ .
2. Aparecerán dos botones, es mejor elejir la opcion [recomendado para la mayoría]: https://nodejs.org/dist/v14.15.1/node-v14.15.1-x64.msi
3.Se descargará el instalador y lo unico que tiene que hacer es seguir los pasos del instalador.

## Crear un Proyecto
### La idea es crear una aplicacion que tenga dos rutas: Una para hacer un input de texto y otra ruta que reciba los datos
1. El primer paso es crear una carpeta donde pondra los archivos para la creacion del proyecto.
2. Cree un archivo llamado `server.js`.
3. Abra el archivo con cualquier editor de texto.
4. En el archivo escriba:
~~~
const http = require('http');

http.createServer(function() {}).listen(8888);
~~~

> Importamos un modulo local de node llamado http para crear nuestro servidor local y hacemos que escuche en el puerto 8888.

5. Agregaremos request y response.
~~~
const http = require('http');

http.createServer(function(request, response) {
  response.writeHead(200, {"Content-Type": "text/html"});
  response.write("Hola Mundo");
  response.end();
}).listen(8888);
~~~
> Con esto estamos diciendo que nuestro servidor puede o responder o requerir algo. En este caso estmos respondiendo con un codigo 200(OK) y un HTML.

6. Para correr el programa es necesario tener una consola abierta en el directorio del proyecto y escribir  `node server.js`

7. Luego necesitaremos abrir nuestro navegador e insertar el link de nuestro [local host]: http://localhost:8888/
> Veremos una pagina que solo dice Hola mundo

8. Lo siguiente es hacer crear un archivo llamado `index.js`

> Aca vamos a ver implementado los **modulos** ya que subdiviremos nuestra aplicacion en varias partes.

9. Antes de escribir en el archivo **index.js* vamos a modificar nuestro **server.js**

~~~
const http = require('http');

function iniciar(){
	http.createServer(function(request, response) {
  	response.writeHead(200, {"Content-Type": "text/html"});
  	response.write("Hola Mundo");
  	response.end();
	}).listen(8888);
}

exports.iniciar = iniciar;
~~~
>Escribimos exports para poder utilizar la funcion iniciar en el archivo **index.js**

10. El archivo **index.js** quedaría:
~~~
var server = require("./server.js");

server.iniciar();
~~~
> A la hora de correr el codigo tendremos que correr `node server.js`.

11. Lo siguiente es saber donde estamos parados(la ruta). Modificaremos el archivo **server.js**:

~~~
var http = require("http");
var url = require("url");

function iniciar() {
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Petición para " + pathname + " recibida.");
    response.writeHead(200, {"Content-Type": "text/html"});
    response.write("Hola Mundo");
    response.end();
  }

  http.createServer(onRequest).listen(8888);
  console.log("Servidor Iniciado.");
}

exports.iniciar = iniciar;
~~~
> Exportamos otro modulo local llamado url que lo utilizaremos para saber el **pathname** de nuestra ruta. Creamos una variable llamada pathname donde hacemos una reuquest al server de la url y la *parseamos* para que solo nos de el pathname

12. Creamos un nuevo archivo llamado `router.js` que contenga lo siguiente:
~~~
function route(pathname) {
  console.log("A punto de rutear una peticion para " + pathname);
}

exports.route = route;
~~~

> Vemos que router esta esperando un objeto **pathname** asique lo siguiente sera pasarselo y exportamos la funcion para usarla mas adelante.

13. Para pasar el pathname de **server.js** a **router.js** necesitamos cambiar nuestra función iniciar y tambien modificaremos **index.js**:
  
~~~
var http = require("http");
var url = require("url");

function iniciar(route) {
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Peticion para " + pathname + " recibida.");

    route(pathname);

    response.writeHead(200, {"Content-Type": "text/html"});
    response.write("Hola Mundo");
    response.end();
  }

  http.createServer(onRequest).listen(8888);
  console.log("Servidor Iniciado.");
}

exports.iniciar = iniciar;
~~~

#### Server.js

~~~
var server = require("./serve.js");
var router = require("./router.js");

server.iniciar(router.route);
~~~
> Requerimos router debido a que la funcion iniciar de **server.js** esta esperando que le pasemos router(). Para eso especificamos que router = router.route.

14. El siguiente paso es definir que hacer cuando estemos en la ruta que queremos. Llamaremos /iniciar donde se cargaran los datos y /subir donde se recibiran. Crearemos un nuevo archivo para que nuestro programa sepa que hacer cuando este en esas rutas. Cree un nuevo archivo llamado `handelers.js` que tendra:

~~~
function iniciar() {
  console.log("Manipulador de petición 'iniciar' ha sido llamado.");
}

function subir() {
  console.log("Manipulador de petición 'subir' ha sido llamado.");
}

exports.iniciar = iniciar;
exports.subir = subir;
~~~

15. Ahora haremos que nuestro server este esperando los **pathnames** que le queremos pasar:

~~~
var server = require("./server");
var router = require("./router");
var requestHandlers = require("./handelers");

var handle = {}
handle["/"] = handlers.iniciar;
handle["/iniciar"]=handlers.iniciar;
handle["/subir"] = handlers.subir;

server.iniciar(router.route, handle);
~~~

16. Tal como hicimos con el router al principio tenemos que pasar handle a la funcion route de **router.js**
~~~
var http = require("http");
var url = require("url");

function iniciar(route, handle) {
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Peticion para " + pathname + " recibida.");

    route(handle, pathname);

    response.writeHead(200, {"Content-Type": "text/html"});
    response.write("Hola Mundo");
    response.end();
  }

  http.createServer(onRequest).listen(8888);
  console.log("Servidor Iniciado.");
}

exports.iniciar = iniciar;
~~~

17. Pasamos a router *handle*:

~~~
function route(handle, pathname) {
  console.log("A punto de rutear una peticion para " + pathname);
  if (typeof handle[pathname] === 'function') {
    return handle[pathname]();
  } else {
    console.log("No se encontro manipulador para " + pathname);
    return "404 No Encontrado";
  }
}
~~~
> Todo el if se escribe porque node js no acepta que pasemos handle[pathnae]() si no le especificamos que es una funcion.

18. Por ultimo nos queda hacer que cada ruta se encargue de cargar su pripio HTML asique necesitamos pasar el parametro response del la funcion iniciar() de server.js a las funciones iniciar() y subir() de handelers.js. Recordemos que server.js solo se tiene que encargar de la parte del server.

~~~
var http = require("http");
var url = require("url");

function iniciar(route, handle) {
  function onRequest(request, response) {
    var pathname = url.parse(request.url).pathname;
    console.log("Peticion para " + pathname + " recibida.");

    route(handle, pathname, response);
  }

  http.createServer(onRequest).listen(8888);
  console.log("Servidor Iniciado.");
}

exports.iniciar = iniciar;
~~~

##### Router.js

~~~
function route(handle, pathname, response) {
  console.log("A punto de rutear una peticion para " + pathname);
  if (typeof handle[pathname] === 'function') {
    handle[pathname](response);
  } else {
    console.log("No hay manipulador de peticion para " + pathname);
    response.writeHead(404, {"Content-Type": "text/html"});
    response.write("404 No Encontrado");
    response.end();
  }
}

exports.route = route;
~~~

#### handelers.js
~~~
const fs = require('fs');

function inicio(response, dataCompleta){
    console.log("Manipulador para Inicio ha sido llamdo")
    response.writeHead(200, {"Content-Type": "text/html"});
    var pepe = fs.readFileSync("body.html", "utf-8");

    response.write(pepe);
    response.end();
}

function subir(response, dataCompleta){
    console.log("Manipulador para subir ha sido llamdo")
    response.writeHead(200, {"Content-Type": "text/html"});
    response.write("Hola soy subir");
    response.end();
}

exports.inicio = inicio;
exports.subir = subir;
~~~
> requiere('fs') es un modulo local y lo uso para leer un archivo html llamdo body.html

#### body.html
~~~
<html>
    <head>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    </head>
    <body>
        <form action="/subir" method="post">
        <textarea name="text" rows="20" cols="60"></textarea>
        <input type="submit" value="Enviar texto" />
        </form>
    </body>
</html>';
~~~
>Este html tiene un input y un *submit* que nos lleva al pathname de subir, asique subir() tiene que esperar datos de iniciar().

19. Necesitamos que el server se encargue de esta peticion post. Node al ser un *single thread* necesita ser lo mas liviano y rapido posible. Para eso las peticiones POST las divide en trozos y luego las junta para mandarlas. Para eso se necesita una funcion que reciva la informacion y otra que la mande completa. 

~~~
request.addListener("data", function(chunk) {
    // funcion llamada cuando un nuevo trozo (chunk) 
    // de informacion (data) es recibido.
});

request.addListener("end", function() {
    // funcion llamada cuando todos los trozos (chunks) 
    // de informacion (data) han sido recibidos.
});    
~~~

#### Entonces server.js quedaría:

~~~
var http = require("http");
var url = require("url");

function iniciar(route, handle) {
  function onRequest(request, response) {
        var dataPosteada = "";
        var pathname = url.parse(request.url).pathname;
        console.log("Peticion para " + pathname + " recibida.");

        request.setEncoding("utf8");

        request.addListener("data", function(trozoPosteado) {
          dataPosteada += trozoPosteado;
          console.log("Recibido trozo POST '" + trozoPosteado + "'.");
    });

    request.addListener("end", function() {
      route(handle, pathname, response, dataPosteada);
    });

  }

  http.createServer(onRequest).listen(8888);
  console.log("Servidor Iniciado");
}

exports.iniciar = iniciar;
~~~

20. Modificamos router para que podamos pasar la data posetada a las funciones iniciar() y subir()

~~~
function route(handle, pathname, response, postData) {
  console.log("A punto de rutear una peticion para " + pathname);
  if (typeof handle[pathname] === 'function') {
    handle[pathname](response, postData);
  } else {
    console.log("No se ha encontrado manipulador para " + pathname);
    response.writeHead(404, {"Content-Type": "text/html"});
    response.write("404 No encontrado");
    response.end();
  }
}

exports.route = route;
~~~

21. Ya terminando tenemos que pasarle postData a ambas funciones

~~~
const fs = require('fs');
const queryString = require("querystring")

function inicio(response, dataCompleta){
    console.log("Manipulador para Inicio ha sido llamdo")
    response.writeHead(200, {"Content-Type": "text/html"});
    var pepe = fs.readFileSync("body.html", "utf-8");

    response.write(pepe);
    response.end();
}

function subir(response, dataCompleta){
    console.log("Manipulador para subir ha sido llamdo")
    response.writeHead(200, {"Content-Type": "text/html"});
    response.write(queryString.parse(dataCompleta)["text"]);
    response.end();
}

exports.inicio = inicio;
exports.subir = subir;
~~~
> iniciar() no usuará **dataCompleta** pero tenemos que pasarsela porque en router() dijimos que ambas la iban a recibir. Utilizo queryString para pasar la data *posteada* a un string, si no lo hago quedaría como: text = "Hola"+"soy"+"un"+texto". Cuando queremos que quede: hola soy un texto
