---
title: Compilación de una aplicación web de Node.js para Azure Cosmos DB | Microsoft Docs
description: Este tutorial de Node.js explora cómo usar Microsoft Azure Cosmos DB para almacenar datos de una aplicación web Node.js Express hospedada en Azure Websites y acceder a ellos.
keywords: Desarrollo de aplicaciones, tutorial de base de datos, información sobre node.js, tutorial de node.js
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 03/23/2018
ms.author: sngun
ms.openlocfilehash: d18e6dd9464ef103157a8532215fa797ab282437
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2018
ms.locfileid: "34797488"
---
# <a name="_Toc395783175"></a>Creación de una aplicación web de Node.js con Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

En este tutorial de Node.js se muestra cómo usar Azure Cosmos DB y la API de SQL para almacenar y acceder a los datos de una aplicación Node.js Express hospedada en Azure Websites. Compile una aplicación de administración de tareas basadas en web sencilla, una aplicación ToDo, que permite crear, recuperar y completar tareas. Las tareas se almacenan como documentos JSON en Azure Cosmos DB. Este tutorial le guiará a través de la creación e implementación de la aplicación y le explicará lo que sucede en cada fragmento de código.

![Captura de pantalla de la aplicación My Todo List creada en este tutorial de Node.js](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

¿No tiene tiempo para completar el tutorial y solo desea obtener la solución completa? No es un problema, puede obtener la solución de ejemplo completa en [GitHub][GitHub]. Solo tiene que leer el archivo [Léame](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md) para obtener instrucciones sobre cómo ejecutar la aplicación.

## <a name="_Toc395783176"></a>Requisitos previos
> [!TIP]
> En este tutorial de Node.js se presupone que tiene experiencia previa con el uso de Node.js y Azure Websites.
> 
> 

Antes de seguir las instrucciones del presente artículo, debe asegurarse de tener lo siguiente:

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] versión v0.10.29 o superior. Se recomienda Node.js 6.10 o superior.
* [Express Generator](http://www.expressjs.com/starter/generator.html) (puede instalarlo mediante `npm install express-generator -g`)
* [Git][Git].

## <a name="_Toc395637761"></a>Paso 1: Creación de una cuenta de base de datos de Azure Cosmos DB
Para comenzar, creemos una cuenta de Azure Cosmos DB. Si ya tiene una cuenta o si usa el Emulador de Azure Cosmos DB en este tutorial, puede ir directamente al [Paso 2: Creación de una nueva aplicación Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Paso 2: Creación de una aplicación Node.js
Ahora aprendamos a crear un proyecto Node.js básico de Hello World usando el marco [Express](http://expressjs.com/) .

1. Abra su terminal favorito como, por ejemplo, el símbolo del sistema de Node.js.
2. Navegue hasta el directorio en el que desea almacenar la nueva aplicación.
3. Use Express Generator para generar una nueva aplicación denominada **todo**.
   
        express todo
4. Abra el nuevo directorio **todo** e instale las dependencias.
   
        cd todo
        npm install
5. Ejecute la nueva aplicación.
   
        npm start
6. Para ver la nueva aplicación, vaya a [http://localhost:3000](http://localhost:3000) desde el explorador.
   
    ![Aprendizaje de Node.js - Captura de pantalla de la aplicación Hello World en una ventana del explorador](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

    A continuación, para detener la aplicación, presione CTRL+C en la ventana de terminal y, a continuación, haga clic en **y** para finalizar el trabajo por lotes (solo en máquinas Windows).

## <a name="_Toc395783179"></a>Paso 3: Instalación de módulos adicionales
El archivo **package.json** es uno de los archivos creados en la raíz del proyecto. Este archivo contiene una lista de módulos adicionales necesarios para una aplicación Node.js. Posteriormente, cuando implemente esta aplicación en Azure Websites, este archivo se usará para determinar qué módulos se deben instalar en Azure para ofrecer respaldo a su aplicación. Todavía hay que instalar dos paquetes más para este tutorial.

1. De nuevo en el terminal, instale el módulo **async** mediante npm.
   
        npm install async --save
2. Instale el módulo **documentdb** mediante npm. En este módulo es donde ocurre toda la magia de Azure Cosmos DB.
   
        npm install documentdb --save

## <a name="_Toc395783180"></a>Paso 4: Uso del servicio Azure Cosmos DB en una aplicación de nodo
Se encarga de toda la configuración e instalación iniciales; ahora volvamos a la razón por la que estamos aquí, que es escribir código con Azure Cosmos DB.

### <a name="create-the-model"></a>Crear el modelo
1. En el directorio del proyecto, cree un directorio denominado **models** en el mismo directorio que el archivo package.json.
2. En el directorio **models**, cree un archivo con el nombre **task-model.js**. Este archivo contendrá el modelo para las tareas que crea nuestra aplicación.
3. En el mismo directorio **models**, cree otro archivo con el nombre **cosmosdb-manager.js**. Este archivo contendrá código útil y reutilizable que se utilizará en toda nuestra aplicación. 
4. Copie el siguiente código en **cosmosdb-manager.js**
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;

    module.exports = {
    getOrCreateDatabase: (client, databaseId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: databaseId }]
        };

        client.queryDatabases(querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let databaseSpec = { id: databaseId };
            client.createDatabase(databaseSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    },

    getOrCreateCollection: (client, databaseLink, collectionId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id=@id',
        parameters: [{ name: '@id', value: collectionId }]
        };

        client.queryCollections(databaseLink, querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let collectionSpec = { id: collectionId };
            client.createCollection(databaseLink, collectionSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    }
    };
    ```
5. Guarde y cierre el archivo **cosmosdb-manager.js**.
6. Al principio del archivo **task-model.js**, agregue el código siguiente para hacer referencia a **DocumentDBClient** y al archivo **cosmosdb-manager.js** creado anteriormente: 

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager.js');
    ```
7. A continuación, agregará código para definir y exportar el objeto Task. Esta función se encarga de inicializar el objeto Task y de configurar la base de datos y la colección de documentos que se utilizarán.  

    ```nodejs
    function TaskModel(documentDBClient, databaseId, collectionId) {
      this.client = documentDBClient;
      this.databaseId = databaseId;
      this.collectionId = collectionId;
   
      this.database = null;
      this.collection = null;
    }
   
    module.exports = TaskModel;
    ```
8. A continuación, agregue el siguiente código para definir métodos adicionales en el objeto Task, que permite las interacciones con los datos almacenados en Azure Cosmos DB.

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager');

    function TaskModel(documentDBClient, databaseId, collectionId) {
    this.client = documentDBClient;
    this.databaseId = databaseId;
    this.collectionId = collectionId;

    this.database = null;
    this.collection = null;
    }

    TaskModel.prototype = {
    init: function(callback) {
        let self = this;

        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function(err, db) {
        if (err) {
            callback(err);
        } else {
            self.database = db;
            docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function(err, coll) {
            if (err) {
                callback(err);
            } else {
                self.collection = coll;
            }
            });
        }
        });
    },

    find: function(querySpec, callback) {
        let self = this;

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results);
        }
        });
    },

    addItem: function(item, callback) {
        let self = this;

        item.date = Date.now();
        item.completed = false;

        self.client.createDocument(self.collection._self, item, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            callback(null, doc);
        }
        });
    },

    updateItem: function(itemId, callback) {
        let self = this;

        self.getItem(itemId, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            doc.completed = true;

            self.client.replaceDocument(doc._self, doc, function(err, replaced) {
            if (err) {
                callback(err);
            } else {
                callback(null, replaced);
            }
            });
        }
        });
    },

    getItem: function(itemId, callback) {
        let self = this;
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: itemId }]
        };

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results[0]);
        }
        });
    }
    };

    module.exports = TaskModel;
    ```
9. Guarde y cierre el archivo **task-model.js**. 

### <a name="create-the-controller"></a>Crear el controlador
1. En el directorio **routes** del nuevo proyecto, cree un nuevo archivo denominado **tasklist.js**. 
2. Agregue el siguiente código a **tasklist.js**. De este modo, se cargan los módulos DocumentDBClient y async, que utiliza **tasklist.js**. También se define la función **TaskList**, que pasa a una sesión del objeto **Task** que definimos anteriormente:
   
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let async = require('async');

    function TaskList(taskModel) {
    this.taskModel = taskModel;
    }

    module.exports = TaskList;
    ```
3. Continúe agregando al archivo **tasklist.js** los métodos usados para **showTasks, addTask** y **completeTasks**:
   
   ```nodejs
    TaskList.prototype = {
    showTasks: function(req, res) {
        let self = this;

        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.completed=@completed',
        parameters: [
            {
            name: '@completed',
            value: false
            }
        ]
        };

        self.taskModel.find(querySpec, function(err, items) {
        if (err) {
            throw err;
        }

        res.render('index', {
            title: 'My ToDo List ',
            tasks: items
        });
        });
    },

    addTask: function(req, res) {
        let self = this;
        let item = req.body;

        self.taskModel.addItem(item, function(err) {
        if (err) {
            throw err;
        }

        res.redirect('/');
        });
    },

    completeTask: function(req, res) {
        let self = this;
        let completedTasks = Object.keys(req.body);

        async.forEach(
        completedTasks,
        function taskIterator(completedTask, callback) {
            self.taskModel.updateItem(completedTask, function(err) {
            if (err) {
                callback(err);
            } else {
                callback(null);
            }
            });
        },
        function goHome(err) {
            if (err) {
            throw err;
            } else {
            res.redirect('/');
            }
        }
        );
    }
    };
    ```        
4. Guarde y cierre el archivo **tasklist.js** .

### <a name="add-configjs"></a>Agregar config.js
1. En el directorio del proyecto, cree un nuevo archivo con el nombre **config.js**.
2. Agregue lo siguiente a **config.js**. Así se definen las opciones de configuración y los valores necesarios para nuestra aplicación.
   
    ```nodejs
    let config = {}
   
    config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.databaseId = "ToDoList";
    config.collectionId = "Items";
   
    module.exports = config;
    ```
3. En el archivo **config.js**, actualice los valores de HOST y AUTH_KEY con los valores de la página Claves de la cuenta de Azure Cosmos DB en [Microsoft Azure Portal](https://portal.azure.com).
4. Guarde y cierre el archivo **config.js** .

### <a name="modify-appjs"></a>Modificar app.js
1. En el directorio del proyecto, abra el archivo **app.js** . Este archivo se creó anteriormente, cuando se creó la aplicación web de Express.
2. Agregue el código siguiente al principio del archivo **app.js**:
   
    ```nodejs
    var DocumentDBClient = require('documentdb').DocumentClient;
    var config = require('./config');
    var TaskList = require('./routes/tasklist');
    var TaskModel = require('./models/taskModel');
    ```
3. Este código determina el archivo de configuración que se debe usar. Además, lee los valores de este archivo en algunas variables que usaremos pronto.
4. Sustituya las dos líneas siguientes en el archivo **app.js** :
   
    ```nodejs
    app.use('/', index);
    app.use('/users', users); 
    ```
   
    por el siguiente fragmento de código:
   
    ```nodejs
    let docDbClient = new DocumentDBClient(config.host, {
        masterKey: config.authKey
    });
    let taskModel = new TaskModel(docDbClient, config.databaseId, config.collectionId);
    let taskList = new TaskList(taskModel);
    taskModel.init();
   
    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    app.set('view engine', 'jade');
    ```
5. Estas líneas definen una nueva instancia del objeto **TaskModel**, con una nueva conexión a Azure Cosmos DB (con los valores leídos en **config.js**). Además, inicializan el objeto de la tarea y enlazan las acciones del formulario a los métodos del controlador **TaskList**. 
6. Por último, guarde y cierre el archivo **app.js**. Estamos a punto de terminar.

## <a name="_Toc395783181"></a>Paso 5: Creación de una interfaz de usuario
Ahora dirijamos nuestra atención a la generación de la interfaz de usuario para que un usuario pueda realmente interactuar con nuestra aplicación. La aplicación Express que hemos creado utiliza **Jade** como motor de vistas. Para obtener más información sobre Jade, consulte [http://jade-lang.com/](http://jade-lang.com/).

1. El archivo **layout.jade** del directorio **views** se utiliza como plantilla global para otros archivos **.jade**. En este paso podrá modificarlo para utilizar [Twitter Bootstrap](https://github.com/twbs/bootstrap), un kit de herramientas que facilita el diseño de un sitio web atractivo. 
2. Abra el archivo **layout.jade** que se encuentra en la carpeta **views** y reemplace el contenido con lo siguiente:

    ```
    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body
        nav.navbar.navbar-inverse.navbar-fixed-top
          div.navbar-header
            a.navbar-brand(href='#') My Tasks
        block content
        script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
        script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
    ```

    Esto indica al motor **Jade** que represente HTML de nuestra aplicación y que cree un **block** llamado **content** donde podemos especificar el diseño de nuestras páginas de contenido.

    Guarde y cierre este archivo **layout.jade** .

3. Ahora abra el archivo **index.jade** , la vista que utilizará nuestra aplicación, y reemplace el contenido del archivo por lo siguiente:
   
        extends layout
        block content
           h1 #{title}
           br
        
           form(action="/completetask", method="post")
             table.table.table-striped.table-bordered
               tr
                 td Name
                 td Category
                 td Date
                 td Complete
               if (typeof tasks === "undefined")
                 tr
                   td
               else
                 each task in tasks
                   tr
                     td #{task.name}
                     td #{task.category}
                     - var date  = new Date(task.date);
                     - var day   = date.getDate();
                     - var month = date.getMonth() + 1;
                     - var year  = date.getFullYear();
                     td #{month + "/" + day + "/" + year}
                     td
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

Esto amplía el diseño y proporciona contenido para el marcador de posición **content** que hemos visto anteriormente en el archivo **layout.jade**.
   
En este diseño hemos creado dos formularios HTML.

El primer formulario contiene una tabla para nuestros datos y un botón que nos permite actualizar los elementos publicando en el método **/completetask** de nuestro controlador.
    
El segundo formulario contiene dos campos de entrada y un botón que nos permite crear un nuevo elemento publicando en el método **/addtask** de nuestro controlador.

Esto debería ser todo lo que necesitamos para que nuestra aplicación funcione.

## <a name="_Toc395783181"></a>Paso 6: Ejecución de la aplicación de forma local
1. Para probar la aplicación en el equipo local, ejecute `npm start` en el terminal para iniciar la aplicación y, a continuación, actualice la página del explorador [http://localhost:3000](http://localhost:3000). La página se debería parecer a la de la imagen siguiente:
   
    ![Captura de pantalla de la aplicación MyTodo List en una ventana del explorador](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Si recibe un error acerca de la sangría en el archivo layout.jade o el archivo index.jade, asegúrese de que las dos primeras líneas de los dos archivos están justificadas a la izquierda, sin espacios en blanco. Si hay espacios delante de las dos primeras líneas, quítelos, guarde ambos archivos y, a continuación, actualice la ventana del explorador. 

2. Utilice los campos Elemento, Nombre del elemento y Categoría para especificar una nueva tarea y, a continuación, haga clic en **Agregar elemento**. Esto crea un documento en Azure Cosmos DB con esas propiedades. 
3. La página debería actualizarse para mostrar el elemento recién creado en la lista ToDo.
   
    ![Captura de pantalla de la aplicación con un nuevo elemento en la lista de tareas pendientes](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Para completar una tarea, simplemente marque la casilla en la columna Complete y, a continuación, haga clic en **Actualizar tareas**. Con ello, se actualizará el documento que ya haya creado y lo quitará de la vista.

5. Para detener la aplicación, presione CTRL+C en la ventana de terminal y, a continuación, haga clic en **Y** para finalizar el trabajo por lotes.

## <a name="_Toc395783182"></a>Paso 7: Implementación del proyecto de desarrollo de aplicación en Azure Websites
1. Si todavía no lo ha hecho, habilite un repositorio para el sitio web de Azure. En el tema [Implementación de Git local en Azure App Service](../app-service/app-service-deploy-local-git.md) puede encontrar instrucciones para hacerlo.
2. Agregue el sitio web de Azure como un git remoto.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Efectúe la implementación enviando los cambios al equipo remoto.
   
        git push azure master
4. En pocos segundos, git terminará de publicar su aplicación web y ejecutará un explorador donde podrá ver su útil trabajo ejecutándose en Azure.

    Felicidades. Acaba de generar su primera aplicación web Express de Node.js mediante Azure Cosmos DB y de publicarla en Azure Websites.

    Si desea descargar o hacer referencia a la aplicación de referencia completa de este tutorial, la puede descargar desde [GitHub][GitHub].

## <a name="_Toc395637775"></a>Pasos siguientes

* ¿Desea realizar pruebas de escala y de rendimiento con Azure Cosmos DB? Consulte [Pruebas de escala y rendimiento con Azure Cosmos DB](performance-testing.md)
* Obtenga información acerca de cómo [supervisar una cuenta de Azure Cosmos DB](monitor-accounts.md).
* Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).
* Explore la [documentación de Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app

