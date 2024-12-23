# examen
### mi documentacion
# Documentación del Proyecto enlaces en PHP usando MVC

**Autor**: [FATIN.M]  
**Fecha**: [19/11/2024]  
**Versión**: 1.0

---
## Introducción al Proyecto

El objetivo de este proyecto es construir una aplicación web basada en el patrón de diseño Modelo-Vista-Controlador (MVC), desarrollada en PHP, para gestionar una base de datos de enlaces y categorías con consultas personalizadas. En la primera fase del proyecto, se creó una vista en la base de datos para combinar los datos de las tablas categoría y vinculos. Esta vista unifica información clave como el ID de la categoría, su nombre, su tipo, el ID de la enlace,la ruta y el titulo. A partir de esta vista, realizamos todas las consultas necesarias para las funcionalidades del proyecto.

Para conectarnos a la base de datos y obtener los datos de la vista, hemos desarrollado una clase conectora (ModelBBDD). Esta clase utiliza la extensión SQL de PHP para establecer una conexión segura con la base de datos y manejar consultas de manera eficiente y escalable.

Para organizar mejor el código y hacerlo más fácil de entender y mantener, he usado una arquitectura llamada **MVC (Modelo-Vista-Controlador)**. Esta estructura separa el código en tres partes principales:

1. **Modelo (Model)**: Gestiona la conexión con la base de datos y realiza las consultas necesarias en la vista.
2. **Vista (View)**: donde defino el diseño que verá el usuario, o sea, la interfaz.
3. **Controlador (Controller)**: Actúa como intermediario, recibiendo las solicitudes del usuario, consultando al modelo y mostrando los resultados en la vista.

---

# Documentación del Proyecto - Pregunta 1

Este documento detalla los pasos realizados para la **importación de una base de datos**, la **creación de una vista** en MySQL Workbench, y la **implementación de una clase en PHP** para conectar y mostrar datos desde la vista en la base de datos.

## Parte 1: Importación de Tablas y Creación de una Vista en MySQL Workbench

### 1.1 Importar las Tablas en MySQL Workbench

1. Abre **MySQL Workbench** y selecciona la conexión a tu base de datos donde deseas importar las tablas.

2. Asegúrate de que la base de datos llamada `enlaces` esté creada en tu servidor. Si no existe, puedes crearla ejecutando el siguiente comando en el editor SQL de Workbench:
   ```sql
   CREATE DATABASE enlaces;
   ```
3. En la barra de menú superior, selecciona File > Open SQL Script.
4. Busca el archivo .sql en tu computadora y ábrelo.
5. Selecciona el archivo .sql que contiene el esquema y los datos de las tablas que deseas importar.
6. Haz clic en el botón Execute (ícono del rayo) para ejecutar el script. Esto creará las tablas y datos especificados en el archivo. Si sql incluye datos, los insertará automáticamente en la base de datos.

## 1.2 Crear una Vista en MySQL Workbench

### Objetivo de la Vista

La vista se utiliza para combinar datos de dos tablas diferentes en una sola estructura de consulta y lo guarda en la base de datos. Esto permite obtener información de ambas tablas en una sola consulta, simplificando el acceso a los datos para el modelo de la aplicación.

### 1.2 Pasos para Crear la Vista

1. Una vez importadas las tablas, puedes crear una vista
2. Abre un nuevo editor SQL en MySQL Workbench.
3. Ejecuta el siguiente comando para crear una vista que muestre el id_categoria , el nombre_categoria, tipo, id_enlace, enlace y titulo.

```sql
  SELECT 
    c.pk_categoria AS id_categoria,
    c.categoria AS nombre_categoria,
    c.tipo,
    v.pk_vinculo AS id_enlace,
    v.enlace ,
    v.titulo
  FROM 
        categoria c
  INNER JOIN 
    vinculos v
  ON 
    c.pk_categoria = v.fk_categoria;
```

Esta consulta selecciona todas las columnas de cada tabla y las une en función de un id común.

4. Ejecuta la consulta en MySQL Workbench para crear la vista.
5. Para verificar que la vista vista_enlaces se ha creado correctamente, ejecuta la siguiente consulta:

```sql
 select * from vista_enlaces;;
```

Esto debería mostrar los datos combinados de las dos tablas.

## Parte 2: Implementación de una Clase ModelBBDD en PHP para conectar con la base de datos y Mostrar Datos desde la Vista

La clase **ModelBBDD** está diseñada para gestionar la conexión a la base de datos y realizar consultas SQL de manera simplificada y reutilizable. Esta clase permite realizar operaciones como seleccionar todos los datos de una tabla o vista, filtrar datos por condiciones específicas, filtrar los preducto por nombre de fabrica o por el precio y manejar conexiones de manera segura. A continuación, se explica cada sección de la clase paso a paso:

```php
   <?php
class ModelBBDD
{
   private static $db_host = 'localhost'; // Servidor de la base de datos
   private static $db_user = 'root';      // Usuario de la base de datos
   private static $db_pass = '';          // Contraseña de la base de datos
   private static $db_name = 'enlaces';    // Nombre de la base de datos
   private static $db_charset = 'utf8';   // Juego de caracteres de la conexión

   private $conn;    // Conexión a la base de datos
   private $query;   // Consulta SQL actual
   private $rows = array();  // Almacenamiento de resultados de consultas
   public $table;    // Nombre de la tabla o vista para la consulta

   // Constructor que acepta el nombre de la tabla o vista
   public function __construct($table) {
       $this->table = $table;
   }

   // Método para abrir la conexión a la base de datos
   private function db_open() {
       $this->conn = new mysqli(
           self::$db_host,
           self::$db_user,
           self::$db_pass,
           self::$db_name
       );
       $this->conn->set_charset(self::$db_charset);
   }

   // Método para cerrar la conexión a la base de datos
   private function db_close() {
       $this->conn->close();
   }

   // Método para ejecutar consultas de acción sin resultado (INSERT, UPDATE, DELETE)
   private function set_query() {
       $this->db_open();
       $this->conn->query($this->query);
       $this->db_close();
   }

   // Método para ejecutar consultas con resultado (SELECT) y almacenar datos en $rows
   private function get_query() {
       $this->db_open();
       $result = $this->conn->query($this->query);

       while ($this->rows[] = $result->fetch_assoc());
       $result->close();
       $this->db_close();

       return array_pop($this->rows);
   }

   // Método para obtener todos los datos de una tabla o vista, o filtrarlos por una condición específica
   public function get($campo = null, $valor = null) {
       $this->query = ($campo && $valor)
           ? "SELECT * FROM $this->table WHERE $campo = '" . $this->conn->real_escape_string($valor) . "'"
           : "SELECT * FROM $this->table";

       $this->get_query();
       return $this->rows;
   }
  
}
```

### Atributos:

- host: Define la dirección del servidor de la base de datos, usualmente localhost en un entorno local.
- usuario: El nombre de usuario para conectar a MySQL.
- contrasena: La contraseña del usuario de la base de datos.
- base_datos: El nombre de la base de datos.

### Explicación de los Métodos

1. __construct($table)__:

   - Constructor que recibe el nombre de la tabla o vista y lo asigna a la propiedad $table para que las consultas se dirijan a ella.

   ```php
   public function __construct($table) {
           $this->table = $table;
       }
    ```

   

2. **Método**db_open():

   - Método privado que abre la conexión a la base de datos usando mysqli y establece el conjunto de caracteres a utf8.

   ```php
   private function db_open() {
           $this->conn = new mysqli(
               self::$db_host,
               self::$db_user,
               self::$db_pass,
               self::$db_name
           );
           $this->conn->set_charset(self::$db_charset);
       }
   ```

3. **Método**db_close():

   - Método privado que cierra la conexión a la base de datos. Es importante cerrar la conexión para liberar recursos cuando no se necesitan.

   ```php
   private function db_close() {
       $this->conn->close();}

   ```

4. **Método**set_query():

   - Ejecuta consultas que no devuelven resultados (como INSERT, UPDATE, DELETE).
   - Abre la conexión, ejecuta la consulta y luego cierra la conexión.

   ````php
       private function set_query() {
       $this->db_open();
       $this->conn->query($this->query);
       $this->db_close();}
       ```

   ````

5. **Método** get_query():
   - Este método ejecuta consultas que devuelven resultados, como SELECT.
   - Utiliza fetch_assoc() para almacenar los resultados en el array $rows.
   - Al final, cierra la conexión y devuelve los datos obtenidos.

```php
   private function get_query() {
    $this->db_open();
    $result = $this->conn->query($this->query);

    while ($this->rows[] = $result->fetch_assoc());
    $result->close();

    $this->db_close();

    return array_pop($this->rows);
}
```

6.  **Método** get($campo = null, $valor = null):

    - Este método generico genera una consulta SELECT para obtener todos los datos de una tabla o vista, o genera una consulta SELECT para obtener un dato por una condición específica.
    - Si $campo y $valor no estan **null**, se filtra por esa condición; si no, se obtiene todo.

    ```php
    public function get($campo = null, $valor = null) {
        $this->query = ($campo !== null && $valor !== null)
    	?"SELECT * FROM $this->table WHERE $campo = '$valor'"
    	:"SELECT * FROM $this->table";

        $this->get_query();

        $num_rows = count($this->rows);

        $data = array();

        foreach ($this->rows as $key => $value) {
    	array_push($data, $value);
        }

        return $data;
    }
    ```



# Documentación del Proyecto (Preguna 2)

## 1. Introducción

Este proyecto utiliza el patrón de diseño MVC (Modelo-Vista-Controlador) para gestionar una aplicación en PHP que se conecta a una base de datos, crea una vista en MySQL y muestra los resultados al usuario a través de una interfaz web.
La aplicación permite realizar las siguientes búsquedas desde una interfaz web construida con Bootstrap:

- Búsqueda por categoría: Permite al usuario listar categoria filtradss por el nombre de la categoría.
- Búsqueda por lenguage de programacíon : El usuario puede buscar  lenguajes de programación en la vista vista_enlaces.
- Búsqueda por palabras en el titulo:  El usuario puede buscar una palabra clave en el título de los enlaces.

## 2. Objetivo

El objetivo de este proyecto es construir una pequeña aplicación en PHP basado en arquitectura MVC que permita consultar una vista en la base de datos y presentar los resultados de forma organizada en una página web.
Esta estructura separa el código en tres partes:

- Modelo (Model): donde guardo la lógica para acceder a los datos en la base de datos.
- Vista (View): donde defino el diseño que verá el usuario, o sea, la interfaz.
- Controlador (Controller): actúa como intermediario entre la vista y el modelo. Llama al modelo para obtener datos y los envía a la vista correspondiente.

## 3. Estructura de Archivos

El proyecto está dividido en carpetas según el patrón MVC:

```
  /assets
      ├── css
      ├── img
      └── js
  /controllers
      ├── Autoload.php
      ├── Router.php
      ├── PreguntasController.php
      └── ViewController.php
  /models
      └── ModelBBDD.php
  /views
      ├── header.php
      ├── footer.php
      ├── preguntas.php
      └── respuesta.php
  index.php
```

## 4. Explicación del Código Paso a Paso

Voy a explicarte cada componente del proyecto:

### 4.1. Modelo (Model): ModelBBDD.php

Este archivo contiene la clase ModelBBDD, que se encarga de gestionar la conexión a la base de datos y ejecutar consultas SQL.tiene métodos para abrir y cerrar la conexión, ejecutar consultas y obtener resultados. es la misma clase que hemos creado en la pregunta 1,lo hemos explicado paso paso en la documentacion de la pregunta 1.
pero la unica diferencia es que hemos añadido otros funciones para hacer las consultas que necesitamos para nuestro proyecto mvc.
**Método** getDatos($campo, $valor):
    - Este método generico busca datos en la tabla o vista utilizando LIKE para coincidencias parciales.
    - Toma el campo y el valor dinamicamente a buscar como parámetros, construye la consulta y utiliza get_query para ejecutarla.

    ```php
    public function getDatos($campo, $valor) {
        $valor = "'%" . addslashes($valor) . "%'";
        $qry = "SELECT * FROM $this->table WHERE $campo LIKE $valor";
        $this->query = $qry;

        $this->get_query();
        return !empty($this->rows) ? $this->rows : false;
    }
    ```
> [!NOTE]
> **$campo**: La columna de la tabla/vista donde se aplicará el filtro. **$valor**: El valor que se desea buscar parcialmente en el campo.
> 

**Método** buscarPorLenguaje($camop, $valor) que es la que se encarga de buscar los lenguajes de programacion que queremos en la tabla categoria.
```php
public function buscarPorLenguaje($campo, $valor) {
		$qry= "SELECT * FROM vista_enlaces WHERE $campo = 'LENGUAJE' AND nombre_categoria = '$valor'";
		$this->query = $qry;
        $result = $this->get_query();
        $num_rows = count($this->rows);
    
        if($num_rows > 0) {
            return $this->rows;
        } else {
            return false;
        }
	}
```
> [!NOTE]
> **$campo**: $campo: El campo que debe ser igual a "Ltipo". **$valor**: El valor que se desea buscar parcialmente en el campo.


y lo hemos utlizado el metedo:  
**get($campo = null, $valor = null)** : para hacer la consulta de la tabla vista_enlaces por categoria 
```php
 public function get($campo = null, $valor = null) {   
		$this->query = ($campo !== null && $valor !== null)
			?"SELECT * FROM $this->table WHERE $campo = '$valor'"
			:"SELECT * FROM $this->table";
	
		$this->get_query();

		$num_rows = count($this->rows);

		$data = array();

		foreach ($this->rows as $key => $value) {
			array_push($data, $value);
		}
       
		return $data;
	}
```	
> [!NOTE]
> **$campo**: $campo (opcional): El nombre de la columna por la que se desea filtrar. **$valor** (opcional): El valor a buscar en el campo indicado.

> [!NOTE]  
> los metedos que utilizado en este clase conectora son genericaos que se puede reutilizar para cualquierproyecto que  necesite una conexion a una base de datos.



### 4.2. Vista (View):, footer.php, preguntas.php, respuesta.php y header.php

Las Vistas son los archivos donde está el HTML, css y js que el usuario ve en pantalla.

- **header.php**: carga el encabezado de la página para todo la página. contiene los enlaces de css y cdn y script de jequery, bootstrap, datatable y ...etc, segun nuestra necesidad en el codigo
- **footer.php**: carga el pie de página para todo la página.
- **preguntas.php**: carga el formulario de preguntas (las consultas) y esta hecho con html y utilizando el checkbox para seleccionar las consultas que se van a realizar y un campo de texto para introducir el valor de la pregunta.

  -En este archivo, el usuario elige cómo quiere buscar (por categoria,  lenguaje de programacion , palabras en el titulo) y escribe el término de búsqueda en un campo de texto. Cuando hace clic en "Consultar", el formulario envía los datos al controlador para procesarlos. esta formulario tiene el metedo post para enviar los datos al controlador.
  ```html
   <div class="container my-5">
      <h1 class="text-center mb-5 fw-bold" style="color: #343a40;">CONSULTAS VISTA_ENLACES</h1>
      <hr>
        <form method="post" action="">
            <!-- Opciones de búsqueda -->
            <div class="mb-4">
                <h4 class="form-label" style="color: #6c757d;">Selecciona el tipo de búsqueda:</h4>
                <div class="d-flex flex-column gap-3">
                    <div class="form-check">
                        <input class="form-check-input" type="radio" checked name="consultas" id="categoria" value="nombre_categoria">
                        <label class="form-check-label" for="categoria" style="color: #343a40;">
                        Búsqueda por Categorías
                        </label>
                    </div>
                    <div class="form-check">
                        <input class="form-check-input" type="radio" name="consultas" id="tipo" value="tipo">
                        <label class="form-check-label" for="tipo" style="color: #343a40;">
                        Búsqueda por LLENGUATGE DE PROGRAMACIÓ
                        </label>
                    </div>
                    <div class="form-check">
                        <input class="form-check-input" type="radio" name="consultas" id="titulo" value="titulo">
                        <label class="form-check-label" for="titulo" style="color: #343a40;">
                        Búsqueda por Título
                        </label>
                    </div>
                </div>
            </div>
            <!-- Input de búsqueda -->
            <div class="input-group mb-4">
                <span class="input-group-text bg-warning text-white" id="basic-addon1">
                Filtro
                </span>
                <input type="text" class="form-control" name="palabra" placeholder="Introduce el filtro (Ej: nombre, tipo, precio)" aria-label="Filtro" aria-describedby="basic-addon1" required>
            </div>
            <input type="hidden" name="r" value="respuesta">
            <!-- Botón de búsqueda -->
            <div class="d-grid">
                <button type="submit" class="btn btn-warning btn-lg">
                Consultar
                </button>
            </div>
        </form>
    </div>
    
  ```

- **respuesta.php**: carga el resultado de la consulta realizada en el formulario de preguntas.

    #### - Instanciación del Controlador

    Crea una nueva instancia de PreguntasController, que se encargará de realizar las consultas en la vista **vista_enlaces** de la base de datos.

    ```php
    $pregunta_controller = new PreguntasController('vista_enlaces');
    ```

    #### 2. Verificación y Asignación de Campos de Búsqueda

    Verifica si el formulario fue enviado correctamente. y si La variable $_POST['r'] es igual 'respuesta', y existir los campos consultas y palabra en el formulario.

    ```php
    if ($_POST['r'] == 'respuesta' && isset($_POST['consultas']) && isset($_POST['palabra'])) {}
    ```

    #### 3. Asignación de Campo y Valor

    una vez entramos en la condicion de la arriba , aqui capturamos  el valor de  ($campo) y el valor ($valor) en base a la opción seleccionada en el formulario (por fabricante, tipo, precio.) y el valor ingresado por el usuario en el campo de texto.
    Y segun el valor de la variable $campo y con un switch, se realiza una consulta diferente en la base de datos.

    ```php
     $campo = $_POST['consultas']; // Tipo de búsqueda
    $valor = $_POST['palabra'];    // Filtro introducido por el usuario

    switch ($campo) {
        case 'nombre_categoria':
            $resultados = $pregunta_controller->get($campo,$valor);
            break;
        case 'tipo':
            $resultados = $pregunta_controller->buscarPorLenguaje($campo,$valor);
            break;
       
        case 'titulo':
            $resultados = $pregunta_controller->getDatos($campo, $valor);
            break;
            default:
            $resultados = [];
    }
    ```

    #### 4. Manejar Resultados Vacíos

   Si no se encuentran resultados en la variable ** $resultados** , se muestra un mensaje de alerta en la interfaz y se recarga la página de preguntas para permitir otra consulta.

    #### 5. Creación de la Tabla HTML para Mostrar Resultados

    Crea la estructura HTML para una tabla, con encabezados específicos, y hemos utilizado aqui la datatable para mostrar los resultados de la consulta.

    #### 6. Rellenar la Tabla con los Resultados de la Consulta

 
### 4.3. Controlador (Controller):

actúa como un intermediario. Recibe las solicitudes del usuario (como hacer clic en "buscar"), consulta al modelo si necesita datos y luego los vuelva a la  vista para mostrar.En mi proyecto hay 4 controladores:

- **Autoload - Autoload.php**:
  - Este archivo contiene una clase llamada Autoload que se encarga de cargar automáticamente las clases de modelo y controlador cuando se necesitan, sin tener que escribir require en todas partes.Utilizando la funcion **spl_autoload_register**

    ```php
     class Autoload {
         public function __construct() {
             spl_autoload_register(function($class_name) {
                 $models_path = './models/' . $class_name . '.php';
                 $controllers_path = './controllers/' . $class_name . '.php';
                 if(file_exists($models_path)) require_once($models_path);
                 if(file_exists($controllers_path)) require_once($controllers_path);
             });
         }
     }
    ```

> [!NOTE]
> Como requerimiento para que funciona la funcion **spl_autoload_register** , los nombres de los archivos deben ser **iguales**
> a los nombres de las clases que en ellos se encuentran.

- **Router.php:**
    Este clase determina qué vista debe cargarse en función de la URL. La clase Router actúa como un controlador de rutas en un esquema MVC (Modelo-Vista-Controlador). Su objetivo principal es:
      1. Decidir qué vista cargar  basándose en los datos recibidos desde el cliente através el formulario
      2. Delegar la carga de vistas a la clase ViewController
    * El constructor comienza creando una instancia de la clase ViewController. Esta clase es responsable de cargar las vistas
    * Si no existe la clave **r** en la array  $_POST, se asume que el usuario no ha realizado una pregunta(consulta). En este caso, se carga la vista por defecto, que es 'preguntas'
    * Si el parámetro r tiene el valor 'respuesta', se asume que el usuario solicitó la vista relacionada con las respuestas, y se carga la vista 'respuesta  
      **Diagrama de Flujo Simplificado:**
      ```
      Inicio -> $_POST['r'] está definido?
                |            |
            No, cargar        Sí, es 'respuesta'?
        'preguntas'             |       
                                |        
                            Cargar 'respuesta'
        ```

    ```php
    class Router {
    public function __construct() {
        $controller = new ViewController();
        if (!isset($_POST['r'])) $controller->load_view('preguntas');
        else if ($_POST['r'] == 'resultados') $controller->load_view('respuesta');
    }

    }
    ```

- **ViewController.php**
  Este archivo contiene la clase ViewController, que se encarga de cargar las vistas.

  ```php
    class ViewController {
  private static $view_path = './views/'; // Ruta donde se encuentran las vistas

  public function load_view($view) { //aqui pasamos el nombre de la vista como parametro para este funcion
  	require_once( self::$view_path . 'header.php' );  // Carga el encabezado
  	require_once( self::$view_path . $view . '.php' ); // Carga la vista solicitada , este vista controla el rutador(Router)
  	require_once( self::$view_path . 'footer.php' );  // Carga el pie de página
  }
  }
  ```

- **PreguntasController.php**
  Este archivo contiene la clase PreguntasController, que se encarga de manejar la lógica de la vista preguntas.php.
  Este clase se instancia la clase del ModelBBDD prara puede llamar los metedos del modelo para obtener los datos.

```php
class PreguntasController{
    private $model;
    private $table;

    public function __construct($table) {
        $this->table = $table;
		// Instancia el modelo correspondiente a la tabla
		$this->model = new ModelBBDD($this->table);
	}
    public function get($campo = null, $valor = null) {
        return $this->model->get($campo, $valor);
    }
    public function getDatos($campo, $valor){
        return $this->model->getDatos($campo, $valor);
    }
    public function buscarPorLenguaje($campo,$valor){
        return $this->model->buscarPorLenguaje($campo,$valor);
    }
}
```

### 4.4 index.php

Este archivo es el punto de entrada de la aplicación. Inicializa la aplicación y carga el enrutador y Autoload.

```php
require_once('./controllers/Autoload.php');
$autoload = new Autoload();
$app = new Router();
```

### Requisitos Técnicos
- HTML5
- PHP 7.4+
- MySQL 5.7+
- Librerías:
  - jQuery
  - Bootstrap
  - DataTable


