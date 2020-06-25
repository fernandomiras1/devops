# Cómo escribir su primer plugins para el Framework serverless

En este tutorial, aprenderá con ejemplos cómo escribir sus propios plugins. Comenzaremos con ejemplos muy simples y los desarrollaremos para escribir plugins útiles que puedan ayudarlo con sus implementaciones diarias.

## Crea tu primer plugins
Serverless Framework es una plataforma de código abierto increíblemente bien construida. Es casi indefinidamente extensible y le permite agregar nuevas funciones con sorprendente facilidad. Veamos cómo puede agregar un complemento de la manera más simple posible.

>En algún lugar de un nuevo directorio ejecute:

`serverless create --template plugin`

>Este comando creará un index.js. Cuando abra este archivo, verá una clase con un constructor y algunas funciones simples. Echemos un vistazo paso a paso.

``` js
class ServerlessPlugin {
  constructor(serverless, options) {
    this.serverless = serverless;
    this.options = options;
    // ...
  }
  // ...
}
```
Cada plugin es una Clase. Esta Clase se instancia con un `serverless` objeto y un montón de `options`. Los abordaremos con más detalle en un momento, pero por ahora es suficiente decir que estos lo ayudarán a hacer cosas en su plugin.

## Defina sus Plugins

``` js
class ServerlessPlugin {
  constructor(serverless, options) {
    // ...
    this.commands = {
      welcome: {
        usage: 'Helps you start your first Serverless plugin',
        lifecycleEvents: [
          'hello',
          'world',
        ],
        // ...
      },
    };
  }
}
```

Lo siguiente a tener en cuenta en el constructor es la definición de comandos que introduce su plugin. En el boilerplate que inicializamos usando el ayudante de la CLI `serverless`, serverless welcomese agrega el plugin.

## Ayuda en tus Plugin

En la seccion `usage`, puede especificar una pista sobre cómo se debe usar el plugin. Esta sugerencia aparecerá cuando se ejecute `serverless --help`.

## Eventos de ciclo de vida

El bit crucial en cualquier plugin es la `lifecycleEvents`. Los eventos del ciclo de vida le permiten definir los pasos que es más probable que se den al ejecutar el comando. Por ejemplo, estos son los eventos del ciclo de vida del comando: `serverless deploy`

+ cleanup
+ initialize
+ setupProviderConfiguration
+ createDeploymentArtifacts
+ compileFunctions
+ compileEvents
+ deploy

De `cleanup` a `deploy`, tenemos una lista de tareas que debemos cumplir en nuestro camino hacia la implementación de un servicio. Sin embargo, todavía no escribimos ninguna implementación. Solo estamos describiendo el proceso de una manera muy general que no incluye ningún detalle de implementación.

La ventaja de este enfoque es que no nos vincula a una forma de manejar la implementación (o ejecutar cualquier otro comando), lo que a su vez nos ayuda a evitar el bloqueo del proveedor.

Como tal, puede ver la definición del comando como una guía que puede usar más adelante para escribir su código. Esto hace que el código sea ​​más legible y auto documentado.

## Opciones - Banderas

Puede notar que en este plugin que también tenemos la seccion de `options` junto a `usage` y `lifecycleEvents`.

``` js
this.commands = {
  welcome: {
    usage: 'Helps you start your first Serverless plugin',
    lifecycleEvents: [
      'hello',
      'world',
    ],
    options: {
      message: {
        usage:
          'Specify the message you want to deploy '
          + '(e.g. "--message \'My Message\'" or "-m \'My Message\'")',
        required: true,
        shortcut: 'm',
      },
    },
  },
};
```
La sección `options` se puede usar para describir qué banderas se pueden usar en comando del CLI. En esta definición, la opción `--message` es obligatoria y tiene un acceso directo `-m` para que pueda escribir `serverless welcome --message "Hello!"` y `serverless welcome --m "Hello!"`.

## Requisitos para definir un comando

El fragmento discutido anteriormente es la definición de eventos del ciclo de vida, la descripción de la ayuda y una marca. Para la bandera, nuevamente tenemos una sección de descripción de ayuda, información sobre si la bandera es necesaria y un acceso directo a la bandera. Como se puede adivinar, **no todos son obligatorios**.

``` js
this.commands = {
  welcome: {
    lifecycleEvents: [
      'hello',
      'world',
    ],
    options: {
      message: {},
    },
  },
};
```

Esto funcionará de manera muy similar a la definición anterior. Sin embargo, no le proporcionará una comprobación automática de que la opción requerida se pasa al comando o cualquier información de ayuda.

Desde esta perspectiva, sugeriría pasar el tiempo escribiendo usage, requisitos y atajos. Hará que sea mucho más fácil para los usuarios de su complemento descubrir cómo usarlo realmente.