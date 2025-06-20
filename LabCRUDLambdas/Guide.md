# Creación de CRUD con lambda, Api gateway y DynamoDB

## 1. Arquitectura

## 2. Definicion

### 2.1 Servicios

### 2.2 Costo

## 3. Proceso

### 3.1 Creacion de lamnda

Vamos a buscar en consola algo El servicio Lamda

![alt text](image.png)

Luego vamos a crear una funcion ( lambda)

![alt text](image-1.png)


Ahora vamos a crear nuestra primera lambda y seguimos los siguientes pasos:

1. Seleccionamos crear lambda desde cero
2. Le damos un nombre a nuestra lmanda
3. Escogemos un environment 

![alt text](image-2.png)

Luego clickeamos en crear funcion
![alt text](image-3.png)


Deberiamos poder ver algo como esto

![image](https://github.com/user-attachments/assets/628a6159-ffe8-4f8a-8e2e-6d59151304c8)



### 3.2 Disparador de Lambda

Ahora vamos a ir a la seccion trigger y vamos asignar nuestro lambda con el trigger de **API Gateway**

### 3.2 Creacion de API Gateway


Vamos a crear nuestro API gateway, esto nos permitira disparar nuestra lambda por metio de peticiones Get Put Post Delete  etc

En consola vamos a buscar el servicio API Gateway

![alt text](image-4.png)


Le daremos click en crear una API

![alt text](image-5.png)


Vamos a seleccionar API http

![image](https://github.com/user-attachments/assets/c5aacdae-539d-4e2b-bc0f-40f0caa628a7)



Vamos a colocar nuestra API gateway con una integracion, que va a ser con nuestra lambda por eso seleccionamos lambda y escogemos la nuestra, le colocamos un nombre y vamos a siguiente

![alt text](image-15.png)


Luego escogemos la accion  que sera **post** y  la ruta que sera la siguiente */books*, y escogemos en target a nuestra lambda creada

![alt text](image-16.png)


Dejamos los stages en **$default**

![alt text](image-17.png)

Y luego le damos click a crear

![alt text](image-18.png)

Ahora podemos ver nuestra API http con  su url de invocacion y sus rutas definidas tambien


![alt text](image-19.png)


![alt text](image-20.png)


Tambien podemos colocar una url por diferente con nuestros dominios en custom domain names


![alt text](image-21.png)


### 3.3 Pruebas de API

Vamos a probar nuestra API, podemos hacerlo de muchas formas, con postman, imsonia, etc


Vamos a hacerlo con Postman

colocamos nuestra ulr base, la nueva ruta y probamos

![alt text](image-22.png)


como vemos esta funcionando



### 3.4 Creacion de Base de Datos

Vamos a crear la base de datos la cual estariamos modificando con nuestras APIS

En consola vamos a buscar el servicio **Dynamo DB**, Dynamo DB es una base de datos no relacional es muy flexible, precisamente lo que necesitamos para este laboratorio

![alt text](image-7.png)



En el servicio le daremos click
![alt text](image-8.png)


Crearemos una tabla con la siguiente configuracion;

1. nombre de la tabla *books*
2. Clave para la tabla *id* o en su caso tipo **string** (cadena)


![alt text](image-9.png)


Le daremos click sin modificar nada más para crear nuestra tabla

![alt text](image-10.png)


Veremos algo como lo siguiente (puede tardar algunos segundos en la creación)


![alt text](image-12.png)


### 3.5 Modificacion tabla base de datos

En la base de datos

Ahora vamos a modificar nuestra peticion para agregar valores a nuestra base de datos, agregamos el siguiente objeto en su body


```
  {
    "title":"Aws lambdas",
    "author": "Santiago Sanchez"
  }
```
![alt text](image-24.png)

y vamos a modificar nuestra lambda para confirmar que funciona

![alt text](image-23.png)

Y comprobaremos que en lamnda en el event, venga esa informacion confirmando un **body**

![alt text](image-25.png)



Ahora modificaremos el codigo del lambda para poder agregar un valor cuando nos llegue


```
import { DynamoDBClient } from "@aws-sdk/client-dynamodb";
import { DynamoDBDocumentClient, PutCommand } from "@aws-sdk/lib-dynamodb";
import { randomUUID } from "crypto";

const ddbDocClient = DynamoDBDocumentClient.from(new DynamoDBClient({ region: "us-east-1" }));

export const handler = async (event, context) => {
  try {
    const book = JSON.parse(event.body);

    const newBook = {
      ...book,
      id: randomUUID(),
    };
    
    await ddbDocClient.send(new PutCommand({
      TableName: "books",
      Item: newBook,
    }));

    return {
      statusCode: 201,
      body: JSON.stringify(newBook),
    };
  } catch (error) {
    console.error(error);
    return {
      statusCode: 500,
      body: JSON.stringify({ message: error.message }),
    };
  }
};


```

El codigo anterior nos permitira modificar nuestra base de datos con un post... si tuvieramos permisos, como se ve en la siguiente imagen, vamos a modificarlos

### 3.6 Permisos y Logs

Ahora  nos falta un permiso..., pero antes de eso, donde vemos los logs? los errores?

Pues vamos a **cloud Watch** en consola

![alt text](image-13.png)

Vamos a registros y grupos de registros (logs groups)


![alt text](image-14.png)

Aca podemos ver el error que tenemos


Ahora en cuanto a permisos debemos ir a nuestro lambda, la pestaña de configuracion y permisos

![alt text](image-26.png)

Vamos a ir a nuestro rol y le damos click

![alt text](image-27.png)


Ya en nuestro rol vamos primero a mirar los permisos y luego a agregar un permiso(policy)
Vamos a crear una nueva politica de permisos (principio de minimo acceso)

![alt text](image-30.png)

Vamos a escoger el servicio y los permisos y donde aplicarian y por ultimo vamos a agregar el ARN de nuestra tabla


![alt text](image-31.png)

En ARN nos traeremos el ARN de nuestra tabla en dynamo (navegamos a ella en otra pestaña) y los campos se ajustaran solos


![alt text](image-33.png)

![alt text](image-34.png)











## 4. Eliminación
