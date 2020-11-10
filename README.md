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


# Proceso de Pago

## Servicios Web Rest
El proceso de Pago se encuentra habilitado solo para nuestros canales aliados que venden nuestros productos Orangreen y para poder realizar las siguientes acciones, el usuario debe tener permisos específicos.
Estos permisos pueden ser solicitados a travez del envío de un correo a juan.vallejo@ixion.com.ec con el asunto "Permisos de Botón de Pago".

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