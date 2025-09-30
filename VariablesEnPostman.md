# Variables
Al igual que cualquier lenguaje de programación o plataforma, se pueden declara variables. El lenguaje base de posman es Javascript.

Las variables solo existen en la petición que se realice

# Scripts
Cuando se hace un request existen dos tiempos para ejecutar antes la request y despues de request.

![pre request](imagenes/Pre-request.png)

Por ejemplo
```
//tiempo en unix
//Variable local

const timestamp = (new Date()).getTime() 

// La varible se "persiste para poder usarla en otros request"
pm.collectionVariables.set("timestamp", timestamp)

//Mostramos el valor como en javascript
console.log(timestamp)
```

Para usar la variable en el request es con 

```
{{timestamp}}
```
Ya sea en en la url, headers o body

```
 "messageRQ": {
        "digitalService": "WUAR_Ingest",
        "WUID": "556620",
        "TRAID": {{timestamp}},
    ...
} 
```

# Funciones utiles para el request

## Generación de una cadena aleatorio de numero
Util para generar números telefonicos, CUIT, números de cuentas, códigos postales
```
/**
 * Generates a random string of numbers of a specified length.
 * @param {number} length - The desired length of the string.
 * @returns {string} - A string consisting of random numbers.
 */
function getStringOfNumber(length) {
    return Array.from({ length: length }, () => Math.floor(Math.random() * 10)).join('');
}

console.log( getStringOfNumber(11) ) // "20879369250"
```

## Generación de una cadena aletoria de letras
```
/**
* Generates a random string of characters of a specified length.
* @param {number} length - The desired length of the string.
* @returns {string} - A string consisting of random characteres.
*/
function getStringOfCharacters(length) {
  const caracteres = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
  //const caracteres = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz";
  let resultado = "";

  for (let i = 0; i < length; i++) {
    const indice = Math.floor(Math.random() * caracteres.length);
    resultado += caracteres.charAt(indice);
  }

  return resultado;
}

console.log( getStringOfCharacters(3) ) //ARU
```

## Fecha actual valida para headerRQ.timestamp

```
const fechaActual = new Date();
const fechaUTC = fechaActual.toISOString();
pm.collectionVariables.set("fechaUtc", fechaUTC)

console.log(fechaUTC) //
```
Por ejemplo
```
{
    "headerRQ": {        
        "timestamp": "{{fechaUtc}}"
    },
    "securityRQ": {        
        "channelId": "BACKOFFICE"
    },
    "messageRQ": {
    }
}
```

# Funciones ramdom de uso frecuente
```
{
  "guid": "{{$guid}}",
  "timestamp": "{{$timestamp}}",
  "isoTimestamp": "{{$isoTimestamp}}",
  "randomUUID": "{{$randomUUID}}",
  "randomFirstName": "{{$randomFirstName}}",
  "randomLastName": "{{$randomLastName}}",
  "randomFullName": "{{$randomFullName}}",
  "randomNamePrefix": "{{$randomNamePrefix}}",
  "randomNameSuffix": "{{$randomNameSuffix}}",
  "randomJobArea": "{{$randomJobArea}}",
  "randomJobDescriptor": "{{$randomJobDescriptor}}",
  "randomJobTitle": "{{$randomJobTitle}}",
  "randomJobType": "{{$randomJobType}}",
  "randomPhoneNumber": "{{$randomPhoneNumber}}",
  "randomPhoneNumberExt": "{{$randomPhoneNumberExt}}",
  "randomCity": "{{$randomCity}}",
  "randomStreetName": "{{$randomStreetName}}",
  "randomStreetAddress": "{{$randomStreetAddress}}",
  "randomCountry": "{{$randomCountry}}",
  "randomCountryCode": "{{$randomCountryCode}}",
  "randomEmail": "{{$randomEmail}}",
  "randomUrl": "{{$randomUrl}}",
  "randomBankAccount": "{{$randomBankAccount}}",
  "randomPhrase": "{{$randomPhrase}}"
}
```

```
{
    "guid": "5e609c06-25df-4d74-9ba6-a14ae58e3956",
    "timestamp": "1758923416",
    "isoTimestamp": "2025-09-26T21:50:16.343Z",
    "randomUUID": "d0059b6a-5778-4c17-a62f-cd0134e8bb03",
    "randomFirstName": "Johnpaul",
    "randomLastName": "Mante",
    "randomFullName": "Mathew Torp",
    "randomNamePrefix": "Miss",
    "randomNameSuffix": "DVM",
    "randomJobArea": "Interactions",
    "randomJobDescriptor": "Legacy",
    "randomJobTitle": "Human Quality Officer",
    "randomJobType": "Manager",
    "randomPhoneNumber": "256-578-8548",
    "randomPhoneNumberExt": "3-675-594-9365",
    "randomCity": "Starkview",
    "randomStreetName": "Witting Garden",
    "randomStreetAddress": "91212 Rutherford Key",
    "randomCountry": "Mali",
    "randomCountryCode": "CF",
    "randomEmail": "Marcella.Fadel74@hotmail.com",
    "randomUrl": "https://esther.biz",
    "randomBankAccount": "86702364",
    "randomPhrase": "Try to hack the SAS application, maybe it will connect the 1080p array!"
}
```

Para ver la lista completa en:
https://learning.postman.com/docs/tests-and-scripts/write-scripts/variables-list/

Para verlas en accion ejecute un eco en postman
```
https://postman-echo.com/post
```
## Cargar los randoms en una variable

```
city = pm.variables.replaceIn("{{$randomCity}}")
address1 = pm.variables.replaceIn("{{$randomStreetAddress}}")

//Ahora podemos set en la coleccion de varibles
pm.collectionVariables.set("city", city)
pm.collectionVariables.set("address1", address1)
```
Esto es recomenblae usarlo en el pre-script para insertar los valores en el body.

# Scripts de post request

Esta sección se usa gneralmente para evaluar los resultados, por ejemplo lo mas simple son evaluar el tipo de estatus, esto se realiza en la pestañ de scritps en la sección de Post-re

![image](imagenes/PostRequest00.png)

```
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```
Esta y otra funciones de uso frecuente estan precargadas en code
**</>**

![image](imagenes/PostRequest.png)

Tambien puede usar el Postbot, es un pequeño asistente, coloque una redacción en ingles, con palabras claves de lo que quiere evaluar, asi como los campos que desea evaluar.


El resultado de nuestra pruebas lo puede ver en la sección de **Response** en el apartado **Test Results**, como puede ver en la siguiente imagen.

![image](imagenes/PostRequestResponse.png)

Para hacer evaluación sobre el **Response body** use el siguiente segmento de código

```
const json = pm.response.json 
```

Por ejemplo una request nos da lo siguiente:

```
{
    "headerRS": {
        "msgId": "6c44fa22-1cda-4f10-833e-a92d102cb298",
        "msgIdOrg": "0d:8d:d2:a1:b3:7c",
        "timestamp": "2025-09-30T01:29:10Z"
    },
    "statusRS": {
        "code": "0",
        "description": ""
    },
    "messageRS": {
        "partyId": "64098612-5727-4208-8f25-1f8c973e86f5",
        "BPartnerId": "6112f81c-f117-4e77-bb29-e70572b7cd8e",
        "accountId": "14639122-c158-4bb9-912a-f753174bb153",
        "errorCode": "0",
        "errorDesc": "OK"
    }
}
```

Para persistir los datos en variables de colección

```
const json = pm.response.json();
// Set collection variables from the response
const partyId = json.messageRS.partyId
const bPartnerId = json.messageRS.BPartnerId
const accountId = json.messageRS.accountId
//Set in collections
pm.collectionVariables.set("partyId", partyId);
pm.collectionVariables.set("BPartnerId", bPartnerId);
pm.collectionVariables.set("accountId", accountId);
//Set in test
pm.test("Status Code is 200", function () {
    pm.response.to.have.status(200);
 })

pm.test("partyId: " + partyId, function () { })
pm.test("bPartnerId: " + bPartnerId, function () { })
pm.test("accountId: " + accountId, function () { })
```

Los test los veremos asi:

![test](./imagenes/TestResult01.png)

Uno de los más comunes de rescatar es el token del login

```
curl --location 'https://base.dev.wu.2innovateit.com/adaptorOAS/auth/login' \
--header 'X-Requested-With: XMLHttpRequest' \
--header 'pCHANNEL: BACKOFFICE' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--header 'Accept: */*' \
--header 'Authorization: Basic dmljdG9yLm1hcnRpbmV6OldVMjAyNQ==' \
--data-urlencode 'grant_type=client_credentials' \
--data-urlencode 'userDomain=OWN'
```
En el apartado de Post-res
```
pm.test('Token Actualizado', function() {
    if(pm.response.to.have.status(200)){
        var json = pm.response.json();
        var token = json.access_token;
        // actualizamos token
        //console.log(token);
        //Guardamos en la colección de variables     
        pm.collectionVariables.set("FrameToken", token);
        //Guardamos tambien en en entorno
        pm.environment.set("FrameToken", token);

        //Aqui se guardo en dos tipos de variables, con que se guarde en uno es suficiente, regurlarmente se guarda en las variables de colección
    }
});
```

Para usarlo en la request lo colocamos en **Auth** en **Baerer Token** colocamos la variable con doble llave **{{FrameToken}}**

![token](imagenes/UsoDelToken.png)

# Evaluar dentro de una lista
