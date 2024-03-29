![logo](tools/images/coderLogo.png)

# CURSO DE JAVA: API REST DE FACTURACIÓN
Proyecto Final para el curso de Java inicial de CODERHOUSE. Consistiendo el mismo en el desarrollo de una aplicación
que nos permita administrar las ventas de un comercio.

Sus características principales son:

### Facturacion:
* _createInvoice_: Crea una nueva factura para un cliente registrado y activo en la base de datos.
* _getInvoicesByClientId_: Obtiene una lista de facturas a partir del ID de un cliente.
* _getInvoiceByInvoiceId_: Obtiene una lista de facturas a partir del ID de una factura específica.


### Gestión de Clientes:
* _registerClient_: Agrega un nuevo cliente a la base de datos con su nombre, apellido y número de documento, y se le asigna automáticamente un número de ID. 
* _updateExistingClient_: Actualiza los datos de un cliente existente y activo en la base de datos, tomando como referencia su ID.
* _getAllClients_: Obtiene una lista con todos los clientes registrados en la base de datos.
* _getClientById_: Obtiene los datos de un cliente en específico a partir de su ID.
* _getClientByDocNumber_: Obtiene los datos de un cliente en específico a partir de su número de documento.

### Gestión de Productos:
* _addNewProduct_: Agrega un nuevo producto a la base de datos con su descripción, código, stock disponible y precio, y se le asigna automáticamente un número de ID.
* _updateExistingProduct_: Actualiza los datos de un producto existente y activo en la base de datos, tomando como referencia su ID.
* _getAllProducts_: Obtiene una lista con todos los productos registrados en la base de datos.
* _getProductById_: Obtiene las características de un producto en específico a partir de su ID.

---
# Funcionalidades

## Facturación / Billing
### 1. createInvoice: 

#### Endpoint
```
URL: /v1/billing
Input: billingRequest
Responses: 
    * 201 Created (Nueva factura creada)
    * 404 Not Found
```

#### Validaciones
* Recibe una solicitud POST con el cuerpo en formato JSON y cabecera Content-Type: application/json.
* Verifica que el cuerpo de la solicitud cumpla con las validaciones de la anotación @Valid en la clase PurchaseRequest.
* Se valida que el cliente exista y este activo en la base de datos antes de asignarlo a la factura
* Se valida que los detalles de la factura estén correctamente asociados a la factura en el método createInvoice() del servicio InvoiceService

### 2. getInvoicesByClientId:

#### Endpoint
```
URL: /v1/invoices/client/{id}
Input: clientId
Responses: 
    * 200 OK (Devuelve factura solicitada)
    * 404 Not Found
    * 400 Bad Request
```

#### Validaciones
* El parámetro clientId debe ser un número entero válido, de lo contrario se producirá una excepción con un mensaje de error y un código de estado HTTP 400 (Bad Request).
* Se valida que el cliente exista en la base de datos antes de buscar sus facturas. Si el cliente no existe, se lanzará una excepción con un mensaje de error y un código de estado HTTP 404 (Not Found).

### 3. getInvoicesByInvoiceId:

#### Endpoint
```
URL: /v1/invoices/{invoiceId}
Input: clientId
Responses: 
    * 200 OK (Devuelve factura solicitada)
    * 404 Not Found
```

#### Validaciones
* La validación de la existencia de la factura se realiza en el método del servicio mediante el método findById del repositorio. Si la factura no se encuentra en la base de datos, se lanza una excepción BillingException con un mensaje de error y un código de estado HTTP 404 (Not Found).

## Gestión de Clientes
### 1. registerClient: 

#### Endpoint
```
URL: /v1/client
Input: clientDTO
Responses: 
    * 201 Created (Nuevo cliente creado)
    * 400 Bad Request
```

#### Validaciones

* Lanzará un mensaje de error y un código de estado HTTP 400 (Bad Request) en los siguientes casos:
    * Si no se recibe un cuerpo de solicitud válido en formato JSON.
    * Si los datos enviados en el cuerpo de la solicitud no cumplen con las validaciones definidas en el DTO.
    * Si los datos enviados en el cuerpo de la solicitud son nulos o vacíos.
    * Si el número de documento enviado en el cuerpo de la solicitud ya existe en la base de datos, ya que está definido como campo único en la entidad Client.
    * Si se intenta crear un nuevo cliente con un ID ya existente en la base de datos, lo que no debería suceder, ya que se está utilizando la estrategia de generación automática de ids.

### 2. updateExistingClient:

#### Endpoint
```
URL: /v1/client/{clientId}
Input: clientDTO
Responses: 
    * 200 OK (Cliente actualizado)
    * 404 Not Found
    * 400 Bad Request
   
```

#### Validaciones
* Lanzará un mensaje de error y un código de estado HTTP 400 (Bad Request):
  * Si no se recibe un cuerpo de solicitud válido en formato JSON.
  * Si los datos enviados en el cuerpo de la solicitud no cumplen con las validaciones definidas en clientDTO.
  * Si el número de documento enviado en el cuerpo de la solicitud ya existe en la base de datos y pertenece a otro cliente, ya que está definido como campo único en la entidad Client.

*   Lanzará un mensaje de error y un código de estado HTTP 404 (Not Found) si el cliente con el número de id especificado en la URL no existe en la base de datos.

### 3. getAllClients:

#### Endpoint
```
URL: /v1/client

Responses: 
    * 200 OK (Traer lista de clientes)
    
```

#### Validaciones
* El metodo getAllClients() garantiza que solo se devuelvan los clientes que existen en la base de datos y sean correctos segun las validaciones que se aplican en la entidad.

### 4. getClientById:

#### Endpoint
```
URL: /v1/client/{clientId}
Input: clientId
Responses: 
    * 200 OK (Devolver cliente especifico de la base de datos)
    * 404 Not Found
    * 400 Bad Request
```

#### Validaciones
* La anotación @NotNull garantiza que el valor del parámetro no sea nulo.
* Si el ID ingresado es menor o igual a cero, se lanza una excepción con un mensaje de error y un estado HTTP 400 (Bad Request).
* Si cliente no se encuentra en el repositorio, se lanza una excepción con un mensaje de error y un estado HTTP 404 (Not Found).

### 5. getClientByDocNumber:

#### Endpoint
```
URL: /v1/client/dni/{docNumber}
Input: docNumber
Responses: 
    * 200 OK (Devolver cliente especifico de la base de datos)
    * 404 Not Found
    * 400 Bad Request
```

#### Validaciones
* Con la anotación @Size se especifica que el docNumber debe tener una longitud mínima de 2 y una máxima de 11 caracteres.
* En la anotación @Pattern se especifica que el docNumber debe contener únicamente números del 0 al 9.
* Si el objeto Client no es encontrado, se lanza una excepción con un mensaje de error y un estado HTTP 404 (Not Found).

## Gestión de Productos
### 1. addNewProduct:

#### Endpoint
```
URL: /v1/product
Input: ProductDTO
Responses: 
    * 201 Created (Nuevo producto creado)
    * 400 Bad Request
```

#### Validaciones
* Si el DTO del producto que se recibe en la solicitud no cumple con las siguientes validaciones definidas en ProductDTO, se lanza una excepción con un mensaje de error y un estado HTTP 400(Bad Request):
    * El campo descripción debe tener una longitud mínima de 2, máxima de 150 caracteres y no debe estar en uso en la base de datos.
    * El código del producto debe tener una longitud mínima de 2 y máxima de 50 caracteres, y debe contener solo dígitos numéricos.
    * El stock de be ser un número entero positivo.
    * El precio debe ser un número decimal positivo.

### 2. updateExistingProduct:

#### Endpoint
```
URL: /v1/product/{productId}
Input: ProductDTO
Responses: 
    * 200 0K (Producto Actualizado)
    * 404 Not Found
    * 400 Bad Request
```

#### Validaciones
* Si los datos proporcionados en el cuerpo de la solicitud no cumplen con las restricciones de validación definidas en la entidad y DTO de producto, se lanzará una respuesta con el código HTTP 400 (Bad Request) y se indicará en el cuerpo de la respuesta
* Si el producto no se encuentra en la base de datos, se lanzará una respuesta con el código 404 (Not Found) y se indicará en el cuerpo de la respuesta que el producto no ha sido encontrado.

### 3. getAllProducts:

#### Endpoint
```
URL: /v1/product
Responses: 
    * 200 0K (Traer lista de productos)
```

#### Validaciones
* El metodo getAllProducts() garantiza que solo se devuelvan los productos que existen en la base de datos y sean correctos segun las validaciones que se aplican en la entidad.

### 4. getProductById:

#### Endpoint
```
URL: /v1/product/{productId}
Input: productId
Responses: 
    * 200 0K (Devolver producto especifico de la base de datos)
    * 404 Not Found
```

#### Validaciones
* En caso de que no se encuentre un producto con el número de id proporcionado, se lanza una excepción y devuelve una respuesta con el estado HTTP 404 (Not Found) y un mensaje de error indicando que el producto no fue encontrado.
---

## Tests



![logo](tools/images/PostmanLogo.png)
###  [Descargar colección de Postman](https://drive.google.com/file/d/1SOMRTzkjbiD36269F9PxBp5MIV0snGlg/view?usp=share_link)



---
# Configuraciones del proyecto

### Especificaciones:
* Java 11
```
openjdk version "11.0.18" 2023-01-17
OpenJDK Runtime Environment (build 11.0.18+10-post-Ubuntu-0ubuntu122.04)
OpenJDK 64-Bit Server VM (build 11.0.18+10-post-Ubuntu-0ubuntu122.04, mixed mode, sharing)
```
* Maven
```
Apache Maven 3.6.3
Maven home: /usr/share/maven
Java version: 11.0.18, vendor: Ubuntu, runtime: /usr/lib/jvm/java-11-openjdk-amd64
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "5.15.0-70-generic", arch: "amd64", family: "unix"
```
* Spring Boot 2.5.14
```
  .   ____          _            __ _ _
  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
  ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
  =========|_|==============|___/=/_/_/_/
  :: Spring Boot ::               (v2.5.14)
```


### Dependencias
* [spring-boot-starter-data-jpa](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-jpa): para acceder a la base de datos utilizando JPA
* [spring-boot-starter-web](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-web): para implementar una arquitectura en capas de beans.
* [spring-boot-starter-data-jpa](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-data-jpa): base de datos en memoria para pruebas.
* [com.h2database:h2](https://mvnrepository.com/artifact/com.h2database/h2): base de datos en memoria para pruebas.
* [spring-boot-starter-test](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-test): para realizar pruebas unitarias en el futuro.
* [spring-boot-starter-validation](https://mvnrepository.com/artifact/org.springframework.boot/spring-boot-starter-validation): para las anotaciones de validación de entrada.
* [javax.validation](https://mvnrepository.com/artifact/javax.validation/validation-api): para utilizar la API de validación.
* [springfox-boot-starter](https://mvnrepository.com/artifact/io.springfox/springfox-boot-starter): para la documentación de la API con Swagger.
* [project-lombok](https://mvnrepository.com/artifact/org.projectlombok/lombok): para anotaciones de utilidad.
* [org.mapstruct](https://mvnrepository.com/artifact/org.mapstruct/mapstruct): para mapear entre Java Beans.

###  Documentación de API:
* [Swagger UI](http://localhost:8080/swagger-ui/index.html) (Solo disponible mientras se está ejecutando la aplicación)

---

## 

# sprintboot_facturacion
