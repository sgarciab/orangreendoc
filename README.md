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

El proceso de Pago se encuentra habilitado solo para nuestros canales aliados y para poder realizar los siguientes el usuario debe tener permisos específicos.
Estos permisos pueden ser solicitados a travez del envío de un correo a juan.vallejo@ixion.com.ec.

Una vez contando con los permisos necesarios, ya se podrán realizar  el proceso consta de X pasos:

1. Creacion de contrato: La creación de contrato es necesaria para nosotros poder tener un registro en nuestro sistema de algunos parámetros necesarios como vigencia, tipo de contrato, etc. El endpoint se puede ver en el siguiente enlace: [Crear Contrato](https://develop.orangreen.com.co/swagger-ui/#/Contracts/postCreateContractUsingPOST)

2. Creación de transacción: La creación de transaccion nos permite saber los montos, los datos del pagador, a que contrato pertenece la transacción y el url de redireccíon a donde se le lleva al usuario después del pago. El endpoint se puede ver en el siguiente enlace: [Crear Transacción](https://develop.orangreen.com.co/swagger-ui/#/Transactions/requestPaymentPlaceToPayUsingPOST)

3. Verificación de estado de la trasacción: Se puede verificar el estado de la transacción a travez mediante el campo de status.  El endpoint se puede ver en el siguiente enlace: [Consultar Transacción](https://develop.orangreen.com.co/swagger-ui/#/Transactions/getTransactionInformationUsingGET)

