### Amazon Web Services

Ahora es el turno de hacer la integración con la plataforma de Amazon Web Services (AWS). Se utilizará el ejemplo del sensor de temperatura, sin embargo puede utilizarse el ejemplo del sensor ultrasónico.

Creamos una cuenta en la plataforma de [Amazon](https://aws.amazon.com/es/)

Una vez creada la cuenta la dejamos abierta y accedemos al backend para visualizar nuestro dispositivo. Damos click en el "Device type"

![aws1](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws1.png?raw=true)

en la columna del lado izquierdo damos click en "Callbacks"

![aws2](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws2.png?raw=true)

enseguida, en la parte superior derecha, daremos click en "NEW" y de las diferentes opciones de callbacks, seleccionaremos "AWS IoT"

![aws3](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws3.png?raw=true)

ahora tendremos que configurar el callback hacia AWS. Tener en cuenta el "External ID", ya que lo necesitaremos mas adelante para configurar la parte de AWS. Hacemos click en "Launch Stack"

![aws4](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws4.png?raw=true)

a continuación nos abrirá una pagina donde configuraremos el STACK creado en AWS. En la esquina superior derecha seleccionaremos la region, en este caso se seleccionó "US East (N. Virginia)". En la parte de "Select template" dejamos todo como está y damos click en "Next"

![aws5](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws5.png?raw=true)

ahora tenemos que configurar "Specify Details", donde necesitaremos nuestro "Account number", el cual podemos obtener en la esquina superior derecha, en Support-> Support Center

![aws13](https://github.com/NXTIoT/Callbacks-hacia-plataformas/blob/master/images/aws1010.png?raw=true)

Seleccionamos un nombre para nuestro Stack. Copiamos nuestro "Account number" y lo pegamos en "AWSAccountId" en la pagina de AWS. Ahora necesitaremos el External ID que nos proporciona sigfox en el backend. Regresamos al backend, lo copiamos y lo pegamos en "ExternalId". Dejamos la region como "us-east-1" y escribimos un "Topic name". Damos click en next

![aws7](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws7.png?raw=true)

En la parte de "Options", no modificamos nada y damos click en "Next". Finalmente en el "Review", seleccionamos "I acknowledge that AWS CloudFormation might create IAM resources" y seleccionamos "Create"

![aws8](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws8.png?raw=true)

Despues de unos minutos estara creado nuestro Stack. Con esto ya queda configurada la parte de AWS del Callback.

![aws9](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws9.png?raw=true)

Ahora falta terminar el Callback en el backend de Sigfox. Una vez que aparezca la leyenda "Create_complete", seleccionamos nuestro Stack y nos vamos a la pestaña "Outputs" y Copiamos el "ARNRole".

![aws10](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws10.png?raw=true)

### Callback AWS

Pegamos el "ARNRole" que obtuvimos de AWS. En "topic", escribimos el mismo que pusimos en nuestro Stack, escogemos la misma region (US East (N. Virginia)) y escribimos el siguiente json

	{
 		"device" : "{device}",
		"data" : "{data}",
 		"time" : "{time}",
 		"snr" : "{snr}",
		"station" : "{station}",
	 	"avgSnr" : "{avgSnr}",
 		"lat" : "{lat}",
 		"lng" : "{lng}",
 		"rssi" : "{rssi}",
 		"seqNumber" : "{seqNumber}"
	}

![aws11](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws11.png?raw=true)

y damos click en OK. Con esto ya tenemos creado nuestro Callback en el backend. Para verificar que no hay ningun problema con la configuración, mandamos un mensaje hacia sigfox y observamos el indicador del callback,que debe quedar de color verde, lo que indica que se realizó de manera exitosa

![aws12](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws12.png?raw=true)

### Creación de una Tabla en DynamoDB

Ahora que tenemos nuestro Stack creado, vamos a crear una tabla por medio de DynamoDB con los datos que mandamos por medio de nuestro dispositivo. Vamos a Services-> DynamoDB

![aws13](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws13.png?raw=true)

damos click en "Crear tabla"

![aws13](https://github.com/NXTIoT/Callbacks-hacia-plataformas/blob/master/images/aws1001.png?raw=true)

Ahora tenemos que configurar nuestra tabla. Le asignamos un nombre, escribimos "deviceid" en Partition Key, seleccionamos "Añadir clave de ordenación" y escribimos "timestamp" y damos click a "Crear"

![aws14](https://github.com/NXTIoT/Callbacks-hacia-plataformas/blob/master/images/aws1002.png?raw=true)

despues de unos minutos, se habrá creado nuestra tabla

![aws15](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws15.png?raw=true)

ahora debemos crear una regla que nos permita enviar enviar los datos recividos hacia nuestra tabla recien creada. Nos vamos a Services-> IoT Core

![aws16](https://github.com/NXTIoT/Callbacks-hacia-plataformas/blob/master/images/aws1004.png?raw=true)

y seleccionamos "ACTUAR"

![aws17](https://github.com/NXTIoT/Callbacks-hacia-plataformas/blob/master/images/aws1005.png?raw=true)

damos click en "Crear"

![aws17](https://github.com/NXTIoT/Callbacks-hacia-plataformas/blob/master/images/aws1006.png?raw=true)

le asignamos el mismo nombre que nuestro Stack, y agregamos una pequeña descripcion (opcional)

![aws18](https://github.com/NXTIoT/Callbacks-hacia-plataformas/blob/master/images/aws1007.png?raw=true)

en el campo "Instruccion de consulta de regla" escribimos SELECT * FROM 'sigfox' donde 'sigfox' es el mismo nombre del topic que pusimos en el callback. Despues tenemos que agregar la accion que queremos que se ejecute. Damos click en "Añadir acción"

![aws17](https://github.com/NXTIoT/Callbacks-hacia-plataformas/blob/master/images/aws1008.png?raw=true)

y escogemos "DynamoDB"

![aws21](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws21.png?raw=true)

posteriormente, tenemos que configurar la acción de insertar los datos en DynamoDB. Seleccionamos nuestra tabla creada anteriormente y escribimos ${device} en "Hash key value", $ {timestamp()} en "Range key value" y "payload" en "Write message data to this column"

![aws22](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws22.png?raw=true)

Creamos un nuevo role dando click en "Create a new role"

![aws23](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws23.png?raw=true)

y le asignamos el nombre "dynamodbsigfox" y damos click en "Add action"

![aws24](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws24.png?raw=true)

finalmente damos click en "Crear una regla"

![aws17](https://github.com/NXTIoT/Callbacks-hacia-plataformas/blob/master/images/aws1009.png?raw=true)

una vez creada, nos aparecera en nuestras reglas

![aws25](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws25.png?raw=true)

si le damos click nos mostrará las caracteristicas de la regla que hemos creado

![aws26](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws26.png?raw=true)

Finalmente, si nos regresamos a DynamoDB->Tables->sigfox->Items, podremos ver los mensajes que enviemos por medio de nuestro Devkit

![aws27](https://github.com/NXTIoT/NXTIoT_DEVKIT/blob/master/images/aws27.png?raw=true)
