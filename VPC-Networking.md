# Como montar tu  propia VPC para tus recursos

## 1. Arquitectura
![VPC-subnets drawio](https://github.com/user-attachments/assets/b2b0180f-65b3-4951-b75a-3f04fe6ec6ab)

## 2. Definicion

Vamos a tener dos recursos de computo (ec2) establecidos en dos redes independientes, una publica y otra privada, crearemos nuestra tabla de rutas y lo expondremos con un internet gateway

### 2.1 Servicios

- EC2: Servicio de amazon para computo [ver más](https://aws.amazon.com/es/ec2/)
- VPC: red virtual privada para tener una segmentación logica (en red) de nuestros servicios [ver más](https://aws.amazon.com/es/vpc/). Adicional tambien cuenta con varias funcionalidades que nos permiten realizar lo que necesitamos
    - Route tables
    - Internet Gateway
    - Sub nets
### 2.2 Costo


## 3. Proceso 


### 3.1 Crearemos nuestra  VPC
    
Vamos al buscador y escribiremos vpc, y navegaremos a este servicio
![Buscador consola aws](imagen.png)

Nota: _Por defecto siempre exite una vpc que es la que se crea cuando creas tu cuenta en AWS_

Luego daremos click en crear el servicio
![Servicio VPC](image.png)




Luego haremos 5 pasos
![Pasos creacion nueva vpc](image-2.png)

1. Seleccionamos solo vpc
2. Colocamos una etiqueta a nuestra VPC
3. Colocaremos el rango de ips para nuestra VPC *12.0.0.16*
4. Agregamos la etiqueta de creador (opcional)
5. Crearemos la VPC



Deberia verse una pantalla como la siguiente

![Resultado creación vpc](image-3.png)


### 3.2 Crearemos nuestro Internet Gateway

Ahora crearemos nuestro intenert gateway; en el panel lateral nos iremos a internet gateway o puertas de enlace de internet

![Panel VPC](image-4.png)

Estableceremos una etiqueta en nuestro internetGateway  y lo crearemos

![Crear internet gateway](image-5.png)

Deberiamos ver una pantalla como la siguiente


![Creacion gateway exitosa](image-6.png)


ahora algo  **Importante**, tenemos nuesotro gateway... pero hay que asociarlo, actualmente solo existe

![Evidencia no asociado gateway](image-7.png)

Navegamos a nustra vpc y le damos click en opciones y asociar a vpc

![Opciones gateway](image-8.png)


Escogemos nuestra VPC y la asociamos

![Asociar gateway](image-9.png)

Luego deberiamos poder ver el proceso de la gateway atada correctamente

![gateway atada](image-10.png)

### 3.3 Creacion de sub redes

Vamos a nuestro panel de VPC  y navegaremos a sub redes


![Panel generla vpc](image-11.png)

![Navegacion a subredes](image-12.png)


Tenemos subredes por defecto añadadidas por AWS **No usaremos esas subredes** , crearemos la nuestra propia

![Creacion VPC](image-13.png)

Ahora realizaremos varios pasos

![alt text](image-14.png)

1. Escogeremos nestra vpc
2. Le colocaremos el nombre a nuestra subred *vpc-lab-public-2a*
3. Estableceremos la zona de disponibilidad
4. Estableceremos el rango de ips de nuestra subred *12.0.1.0/24*
5. Crearemos otra sub red


De forma analoga crearemos la otra subred


![Creacion segunda subred](image-15.png)

1. Le colocaremos el nombre a nuestra subred *vpc-lab-private-2a*
2. Estableceremos la zona de disponibilidad
3. Estableceremos el rango de ips de nuestra subred *12.0.3.0/24*


Deberiamos ver una pantalla como la siguiente

![Creacion subred exitosa](image-16.png)


### 3.4 Creacion de tabla de rutas


Iremos a nuestra creacion de tablas de rutas en el panel derecho y crearemos una nueba tabla de rutas


![Tabla de rutas panel](image-17.png)


Crearemos nuestra tabla de rutas para nuestra red publica con el nombre *RT-vpc-lab-public*

![Creacion tabla de rutas publica](image-18.png)

deberiamos ver la siguiente pantalla

![alt text](image-19.png)



Pero con eso no basta, por que es nuestra red publica, necesita acceso a internet, entonces vamos a editarla

Vamos a nuestra tabla de rutas y seleccionamos editar rutas


![alt text](image-20.png)




1. Agregamos nuestra destinacion  *0.0.0.0/0* que significa que tiene acceso a internet y puede ser accedida desde internet
2. Escogemos el objetivo, con internet gateway que nosotros creamos
3. Guardamos los cambios


![alt text](image-21.png)


Deberiamos ver una pantalla como la siguiente, con las dos rutas, la que agregamos y la por defecto


![alt text](image-22.png)


ahora tenemos que asignar nuestra route table a la red publica


![alt text](image-23.png)


1. Navegamos hacia asociaciones de subredes
2. Clickeamos en editar subredes


![alt text](image-24.png)


Seleccionamos nuestra red publica y guardamos, deberiamos ver una pantalla como la siguiente

![alt text](image-25.png)




Ahora tenemos que crear otra tabla de rutas, para nuestra red privada:

Vamos a la navegacion de nuestra tablas de rutas y vamos a repetir el proceso de creacion de tablas de rutas
con el nombre *RT-vpc-lab-private*

![alt text](image-26.png)


debemos ver un mensaje como el siguiente

![alt text](image-27.png)


Ahora lo debemos asociar con nuestra subred privada

![alt text](image-28.png)

Y asociamos la tabla con nuestra red

![alt text](image-29.png)

Deberiamos ver un mensaje como el siguiente
![alt text](image-30.png)



ahora si vamos a crear nuestros recursos los EC2

### 3.5 Creacion EC2


Vamos a buscar nuestra consola de navegacion e iremos a EC2

![alt text](image-31.png)


Lanzaremos una instancia

![alt text](image-32.png)


ahora haremos lo siguiente en varios pasos:

![alt text](image-33.png)


1. Le colocaremos un nombre a nuestra instancia *ec2-vpc-lab-public*
2. escogeremos ubunto
3. tomamos la capa gratuita 


Ahora navegaremos hacia los pares de claves


Vamos a crear los pares de claves con el nombre *PK-vpc-lab*

![alt text](image-34.png)


La clave privada se guardara en tu pc y la publica se asociara con el ec2


Luego vamso a configuraciones de red y vamos a editarlo


![alt text](image-36.png)


1. Vamos a escoger nuestro vpc
2. Seleccionamos nuestra red publica
3. Habilitamos un acceso de ip publico(por que es la publica)
4. Creamos un grupo de seguridad nuevo
5. Colocamos el nombre en le grupo de seguridad *SG-ec2-lab-vpc*
6. Establecemos la descripcion del grupo de seguridad *SG-ec2-lab-vpc*



Luego cambiamos la memoria y lanzamos la instancia

![alt text](image-37.png)


deberiamos ver una pantalla como la siguiente

![alt text](image-38.png)

Ahora necesitamos comunicacion SSH con la instancia

![alt text](image-39.png)

Vamos a nuestra instancia corriendo y en la seccion de network copiamos la direccion publica


Ahora vamos a probar la conexion, clickeamos en conectar
![alt text](image-40.png)


y vamos a la pestaña cliente SSH, donde veremos las instrucciones, esto nos permitira concetarnos con ssh con nuestra clave privada

![alt text](image-41.png)



siguiendo los pasos, podremos ver lo siguiente


![alt text](image-42.png)


Con exito nos conectamos al sitio!

Ahora otro tema importante

## 4 Eliminar

Como es un proceso que puede ser costoso y esto es con propositos educativos, vamos a eliminar la configuracion


Vamos a darle exit a nuestra consola y luego en los servicios de EC2 vamos a clickear en terminar, como es con fines educativos no nos importa que se elimine

![alt text](image-43.png)


**Importante:** Validar correctamente que no este corriendo


Luego en la consola navegamos a nuestra VPC

Vamos primero a nuestras tablas de rutas 


![alt text](image-44.png)


Seleccionamos nuestra tabla privada
![alt text](image-45.png)

vamos a sus asociaciones y las editamos


![alt text](image-46.png)


Luego desclickeamos la que tiene asociada y guardamos


![alt text](image-47.png)



Ahora si eliminamos nuestra tabla de rutas


![alt text](image-48.png)


Repetimos el proceso para la tabla de red publica
![alt text](image-49.png)

Vamos a sub redes la seleccionamos(una a la vez)  y damos click en eliminar, refrescamos  y esperamos que se haya eliminado

![alt text](image-50.png)

Ahora vamos a eliminar nuestro internet gateway,

![alt text](image-51.png)

Vamos a nuestro gateway, eliminamos su conexion con la vpc y luego lo eliminamos

Por ultimo eliminamos nuestra vpc


![alt text](image-52.png)



Con esto hemos limpiado todo y eliminado nuestros recursos!
