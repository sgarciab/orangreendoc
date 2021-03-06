# Empezando

Aquí se puede empezar la integración de los web services de Orangreen Insurances&Solutions

# Prueba nuestros servicios

Para poder acceder a la documentación de los endpoints se lo puede realizar desde aquí:

[Documentación Servicios Rest](https://develop.orangreen.com.co/swagger-ui/)

Nuestros servicios web están implementados con Swagger y pueden encontrarse aquí:

[Especificación de API](https://develop.orangreen.com.co/v2/api-docs)



# Creación de Usuario

Para crear un usuario es necesario que se utilice el endpoint de registrar
[Crear Cuenta](https://develop.orangreen.com.co/swagger-ui/#/Users/registerUsingPOST)



# Cotización SOAT Colombia
## Cotización por placa
La cotización de placa es un endpoint libre dentro OranGreen. Para poder consultarlo y probarlo visite este url [Cotización](https://develop.orangreen.com.co/swagger-ui/#/Lead/postLeadPlateDataUsingPOST).

La cotización va a responder con datos devueltos por AXA Colpatria junto con los datos del Vehículo. 

# Proceso de Pago SOAT No Controlado
## Servicios Web Rest
El proceso de Pago SOAT se encuentra habilitado solo para nuestros canales aliados que venden nuestros productos Orangreen, este servicio no controlado tiene el objetivo de que el aliado controla la recaudación e informa a través de nuestros APIs una vez que ha sido pagado. En este proceso no interviene nuestro botón de pagos PlacetoPay. Para poder realizar las siguientes acciones, el usuario debe tener permisos específicos.
Estos permisos pueden ser solicitados a través del envío de un correo a juan.vallejo@ixion.com.ec con el asunto "Permisos de proceso de Pago No Controlado".

Una vez contando con los permisos necesarios, ya se podrán las siguientes acciones que consta consta de 3 pasos:

1. Login: para poder acceder a los servicios web es necesario tener un access token. Este se puede obtener haciendo login hacia el endpoint de login. El endpoint se puede ver en el siguiente enlace: [Login](https://develop.orangreen.com.co/swagger-ui/#/Contracts/postCreateContractUsingPOST)

2. Creacion de contrato: La creación de contrato es necesaria para nosotros poder tener un registro en nuestro sistema de algunos parámetros necesarios como vigencia, tipo de contrato, etc. El endpoint se puede ver en el siguiente enlace: [Crear Contrato](https://develop.orangreen.com.co/swagger-ui/#/Contracts/postCreateContractCustomUsingPOST)

3. Creación de transacción: La creación de transaccion nos permite saber los montos, contrato y el estado de la transacción. Los estados disponibles para que nos puedan informar son los siguiente:`PAID`,`CANCELLED`,`REJECTED`. Una vez que el estado es  `PAID` se procede a emitir el SOAT en COLPATRIA. El endpoint se puede ver en el siguiente enlace: [Crear Transacción](https://develop.orangreen.com.co/swagger-ui/#/Transactions/requestPaymentCustomUsingPOST)
## Webhook
Cada vez que nosotros recibimos la notificación del aliado del nuevo estado de la transacción, ya sea pagada, rechazado o procesando. Nosotros vamos a notificar a nuestro aliado el estado de la emisión del SOAT. Hay 3 estados que se puede recibir:
* `ACTIVE`: La emisión del soat fue activada desde el procesador de pagos.
* `PENDING`: La emisión del soat todavía se encuentra en estado pendiente.
* `VOIDED`: La emisión del soat fue anulada desde nuestra plataforma por algún requerimiento.

El cuerpo de la transacción es de la siguiente manera 
```json
//POST
{    
    "contractId": "3fa85f64-5717-4562-b3fc-2c963f66afa6", //UUID
    "status": "string", //ACTIVE,PENDING, VOIDED    
    "amount":"double",
    "signature": "string" // SHA1
}
```

El campo `signature` se refiere a una firma hash con el algoritmo SHA-1 ([Secure Hash Algorithm 1](https://www.w3.org/PICS/DSig/SHA1_1_0.html)) cuyo contenido es el cuerpo de la petición como se muestra a continuación:
```javascript
var signature = sha1(contractId + status + amount +secretKey)
```
Este campo se utiliza particularmente para verificar la autenticidad del emisor.

El campo `secretKey` es una llave secreta precompartida (Pre-Shared Key) entre las dos partes y en caso de no haber sido provista debe solicitarse al email juan.vallejo@ixion.com.ec.

La url para notificar debe ser provista por el aliado.



# Proceso de Pago

## Servicios Web Rest
El proceso de Pago se encuentra habilitado solo para nuestros canales aliados que venden nuestros productos Orangreen y para poder realizar las siguientes acciones, el usuario debe tener permisos específicos.
Estos permisos pueden ser solicitados a través del envío de un correo a juan.vallejo@ixion.com.ec con el asunto "Permisos de Botón de Pago".

Una vez contando con los permisos necesarios, ya se podrán las siguientes acciones que consta consta de 3 pasos:

1. Login: para poder acceder a los servicios web es necesario tener un access token. Este se puede obtener haciendo login hacia el endpoint de login. El endpoint se puede ver en el siguiente enlace: [Login](https://develop.orangreen.com.co/swagger-ui/#/Contracts/postCreateContractUsingPOST)

2. Creacion de contrato: La creación de contrato es necesaria para nosotros poder tener un registro en nuestro sistema de algunos parámetros necesarios como vigencia, tipo de contrato, etc. El endpoint se puede ver en el siguiente enlace: [Crear Contrato](https://develop.orangreen.com.co/swagger-ui/#/Contracts/postCreateContractUsingPOST)

3. Creación de transacción: La creación de transaccion nos permite saber los montos, los datos del pagador, a que contrato pertenece la transacción y el url de redireccíon a donde se le lleva al usuario después del pago. El url de redirección pertenece a PlaceToPay con el fin de permitir al usuario pagar con tarjeta de crédito o con PSE. El endpoint se puede ver en el siguiente enlace: [Crear Transacción](https://develop.orangreen.com.co/swagger-ui/#/Transactions/requestPaymentPlaceToPayUsingPOST)

4. Verificación de estado de la trasacción: Se puede verificar el estado de la transacción a travez mediante el campo de status.  El endpoint se puede ver en el siguiente enlace: [Consultar Transacción](https://develop.orangreen.com.co/swagger-ui/#/Transactions/getTransactionInformationUsingGET)


## Webhook
Cada vez que nosotros recibimos la notificación de PlaceToPay del nuevo estado de la transacción, ya sea pagada, rechazado o procesando. Nosotros vamos a notificar también a nuestro aliado el nuevo estado de la transacción. Hay 5 estados que se puede recibir:
* `CREATED`: La transacción apenas fue creado o todavía no se tiene una nueva actualización desde su creación.
* `PAID`: La transacción fue pagada desde el procesador de pagos.
* `CANCELLED`: La transacción fue cancelada desde el procesador de pagos.
* `PENDING`: La transacción todavía se encuentra en estado pendiente de aprobación del banco.
* `VOIDED`: La transacción fue anulada desde nuestra plataforma por algún requerimiento.

El cuerpo de la transacción es de la siguiente manera 
```json
//POST
{
    "extenalTransactionId":"string",
    "contractId": "3fa85f64-5717-4562-b3fc-2c963f66afa6", //UUID
    "status": "string", //CREATED, PAID, CANCELLED, PENDING, VOIDED
    "signature": "string" // SHA1
}
```

El campo `signature` se refiere a una firma hash con el algoritmo SHA-1 ([Secure Hash Algorithm 1](https://www.w3.org/PICS/DSig/SHA1_1_0.html)) cuyo contenido es el cuerpo de la petición como se muestra a continuación:
```javascript
var signature = sha1(extenalTransactionId + contractId + status + secretKey)
```
Este campo se utiliza particularmente para verificar la autenticidad del emisor.

El campo `secretKey` es una llave secreta precompartida (Pre-Shared Key) entre las dos partes y en caso de no haber sido provista debe solicitarse al email juan.vallejo@ixion.com.ec.

La url para notificar debe ser provista por el aliado.



# UMA notificación 
Hay 3 estados para las consultas médicas:
* `PREASSIGN` : El usuario pide una consulta esperando ser atendido. 
* `ATT` : El paciente “entra” a la consulta.
* `DONE` : Se cierra la consulta.

En el momento en el cual ocurre cada uno de estos posiblesestados, se genera un request a la siguiente URL: /api/appointment/uma/notification, con los siguientes datos.

* PREASSIGN:
  * dni (string): número de documento.
  * incidente_id: identificador de la consulta.
  * request_date (string): fecha_hora del pedido de atención.
  * status (string): estado de consulta (PREASSIGN)

* ATT:
  * dni (string): número de documento.
  * incidente_id: identificador de la consulta.
  * att_date (string): fecha_hora del comienzo de la atención.
  * status (string): estado de consulta (PREASSIGN)

* DONE:
  * dni (string): número de documento.
  * incidente_id: identificador de la consulta.
  * dt_cierre (string): fecha_hora del fin de la atención.
  * destino_final (string): 
  * diagnostico (string): diagnóstico de la atención médica.
  * status (string): estado de consulta (DONE).

El método que se utiliza es POST con el siguiente contenido.

```json 
//POST
{    
  "dni": "00000000",    
  "dt_cierre": "2020-10-20 17:06:18",     
  "incidente_id": "2020-11-25_08-54-46-110992", 
  "destino_final": "En domicilio con monitoreo",    
  "diagnostico": "INESP Confirmado COVID19 x epidemiol",    
  "status": "DONE",
  "signature": "e4f226c99d11914249ec29879c7ad2279f72fbcc20a5a41eec8b771878ff0b18"
}
``` 
El campo `signature` se refiere a una firma hash con el algoritmo SHA-256 ([Secure Hash Algorithm 256](http://www.iwar.org.uk/comsec/resources/cipher/sha256-384-512.pdf)) cuyo contenido es el cuerpo de la petición como se muestra a continuación:

```pseudocódigo
var signature = sha256(dni + Separator + notificationKey + Separator + status + Separator + destino_final + Separator + incidente_id)
```

Donde `Separator` equivale a `^|^` y el campo `notificationKey` se refiero a un PSK, que es una pre-shared key( clave previamente compartida) la cual se realiza internamente por correo.

Se puede ver una implementacion en C#

```C#
  var bodyHashing = Identification + Separator + notificationKey + Separator + Status + Separator + Diagnostico;
  var crypt = new System.Security.Cryptography.SHA256Managed();
  var hash = new System.Text.StringBuilder();
  byte[] crypto = crypt.ComputeHash(Encoding.UTF8.GetBytes(bodyHashing));
  foreach (byte theByte in crypto)
  {
      hash.Append(theByte.ToString("x2"));
  }
  return hash.ToString();
```
# Citas VALE
1. Register :para crear un usuario es necesario que se utilice el endpoint de registrar: [Crear Cuenta](https://develop.orangreen.com.co/swagger-ui/#/Users/registerUsingPOST)

2. Login: para poder acceder a los servicios web es necesario tener un access token. Este se puede obtener haciendo login hacia el endpoint de login. El endpoint se puede ver en el siguiente enlace: [Login](https://develop.orangreen.com.co/swagger-ui/#/Users/loginUsingPOST)

3. Obtener Planes Activos: Para obtener los planes activos de un cliene es necesario que se utilice el endpoint:[Obtener Productos](https://develop.orangreen.com.co/swagger-ui/#/Contracts/getProductContractUsingGET)

4. Creacion de cita: La creación de cita es necesaria para nosotros poder tener un registro en nuestro sistema de algunos parámetros necesarios como tipo de contacto,tipo de relación , tipo de cita, email, teléfono, etc. El endpoint se puede ver en el siguiente enlace: [Crear Cita](https://develop.orangreen.com.co/swagger-ui/#/Appointment/createAppointmentUsingPOST)

Hay 2 estados para tipo de contacto:
* `CALL` : El usuario pide ser llamado para obtener su cita. 
* `WHATSAPP` : El usuario pide ser contactado por whatsapp para obtener su cita.

Hay 3 estados para tipo de cita:
* `TELEMEDICINA` : cita tipo telemedicina. 
* `MEDICINA_PRESENCIAL` : cita tipo presencial.
* `ODONTOLOGIA` : cita tipo odontológica.

Hay 1 estado para tipo de relación:
* `OWNER` : titular. 

