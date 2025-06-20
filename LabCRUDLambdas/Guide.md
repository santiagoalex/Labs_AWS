# Creación de CRUD con lambda, Api gateway y DynamoDB

## 1. Arquitectura!

[Lab_lambda-crud](https://github.com/user-attachments/assets/d8aed8fc-fd72-4c22-a17e-c82d4ef8b737)


## 2. Definicion

Vamos a tener una aproximacion a una base de datos basica usando aws lambda empezando por el metodo POST

### 2.1 Servicios

- CloudWatch:  nos permite llevar registros en aws de lo que configuremos para la misma
- Lambda: capacidad de computo en minutos
- DynamoDB: una base de datos no relacional para usar cuando queramps
- IAM: manejador de roles permisos y accesos
- API Gateway: nos permite consumir enpoints para integrar con diferentes necesidades

### 2.2 Costo

Vamos a cancelar todos nuestros servicios entonces virtualmente no tendra costo.


Pero en capa gratuita costaria esto

https://calculator.aws/#/estimate?id=3726ecde6980b5db44627efb75d9394d4dbd7d64

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

![image](https://github.com/user-attachments/assets/b314157e-c9ce-42bb-9c23-3b38cc996c2e)



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
Vamos a crear una nueva politica de permisos (principio de minimo acceso) en el Servicio de **IAM** y en Politicas vamos a crear una nueva

![image](https://github.com/user-attachments/assets/2127ad1e-f04b-45cd-8599-767573e0b365)





Vamos a escoger el servicio y los permisos y donde aplicarian y por ultimo vamos a agregar el ARN de nuestra tabla


![alt text](image-31.png)

En ARN nos traeremos el ARN de nuestra tabla en dynamo (navegamos a ella en otra pestaña) y los campos se ajustaran solos

![alt text](image-32.png)

![alt text](image-33.png)


![alt text](image-34.png)




Le damos en agregar y luego en el panel del permiso clickeamos siguiente

![alt text](image-35.png)


Ahora colocaremos un nombre a la policy *CreateBookPolicy* y  crearemos el book policy

![alt text](image-36.png)


Luego la ligaremos a nuestro policy , buscaremos, colocamos si nombre, la agregamos 
![image](https://github.com/user-attachments/assets/1f536113-7cf9-48fd-a1df-8a1638de0302)



![alt text](image-37.png)


![alt text](image-38.png)

ahora probamos en postman




![alt text](image-39.png)


Vamos a nuestra tabla

![image](https://github.com/user-attachments/assets/ff2d9e4c-c1fb-4792-99ef-eaf34ac6268a)


![alt text](image-40.png)


Y buala tenemos nuestra primera operacion en nuestra base de datos!




## 4. Eliminación


### 4.1 Tabla
![alt text](image-41.png)
### 4.2 Lambda
![alt text](image-42.png)


### 4.3 API Gateway

![alt text](image-43.png)


### 4.4 CloudWatch Log groups

![image](https://github.com/user-attachments/assets/c7b1c6a3-d899-4f8e-be3d-b6f28060de2d)

### 4.5 (Opcional) Eliminamos las politicas

Es opcional por que las politicas de IAM no tienen costo

![image](https://github.com/user-attachments/assets/833aa9e1-972d-4341-8325-b2a21ad89b90)


