---
title: "Adición de la acción HTTP a Logic Apps | Microsoft Docs"
description: "Información general de la acción HTTP con propiedades"
services: 
documentationcenter: 
author: jeffhollan
manager: erikre
editor: 
tags: connectors
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 522624ccb14d295359ff5585e1b46b618b45c305


---
# <a name="get-started-with-the-http-action"></a>Introducción a la acción HTTP
Con la acción HTTP, puede ampliar los flujos de trabajo de su organización y comunicarse con cualquier punto de conexión a través de HTTP.

Puede:

* Cree flujos de trabajo de aplicaciones lógicas que se activen (desencadenador) cuando un sitio web que administre deje de funcionar.
* Comuníquese con cualquier punto de conexión por HTTP para ampliar los flujos de trabajo a otros servicios.

Para empezar a usar la acción HTTP en una aplicación lógica, consulte [Creación de una nueva aplicación lógica mediante la conexión de servicios de SaaS](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="use-the-http-trigger"></a>Uso del desencadenador HTTP
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. [Más información sobre los desencadenadores](connectors-overview.md).

Esta es una secuencia de ejemplo de cómo configurar un desencadenador HTTP en el diseñador de aplicaciones lógicas.

1. Agregue el desencadenador HTTP a la aplicación lógica.
2. Rellene los parámetros para el punto de conexión HTTP que desee sondear.
3. Modifique el intervalo de periodicidad de la frecuencia con que se debe sondear.
4. Ahora se activa la aplicación lógica con cualquier contenido que se devuelva durante cada comprobación.

![Desencadenador HTTP](./media/connectors-native-http/using-trigger.png)

### <a name="how-the-http-trigger-works"></a>Funcionamiento del desencadenador HTTP
El desencadenador HTTP realizará una llamada a un punto de conexión HTTP en un intervalo periódico. De forma predeterminada, cualquier código de respuesta HTTP inferior a 300 hará que una aplicación lógica se ejecute. Puede agregar una condición en la vista código que se evaluará después de la llamada HTTP para determinar si se debe activar la aplicación lógica. Este es un ejemplo de un desencadenador HTTP que se activará cada vez que el código de estado devuelto sea mayor o igual que `400`.

```javascript
"Http":
{
    "conditions": [
        {
            "expression": "@greaterOrEquals(triggerOutputs()['statusCode'], 400)"
        }
    ],
    "inputs": {
        "method": "GET",
        "uri": "https://blogs.msdn.microsoft.com/logicapps/",
        "headers": {
            "accept-language": "en"
        }
    },
    "recurrence": {
        "frequency": "Second",
        "interval": 15
    },
    "type": "Http"
}
```

Los detalles completos acerca de los parámetros de desencadenador HTTP están disponibles en [MSDN](https://msdn.microsoft.com/library/azure/mt643939.aspx#HTTP-trigger).

## <a name="use-the-http-action"></a>Uso de la acción HTTP
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información sobre las acciones](connectors-overview.md).

1. Seleccione el botón **Nuevo paso** .
2. Elija **Agregar una acción**.
3. En el cuadro de búsqueda de acciones, escriba **http** para mostrar la acción HTTP.
   
    ![Selección de la acción de HTTP](./media/connectors-native-http/using-action-1.png)
4. Agregue cualquier parámetro necesario para la llamada HTTP.
   
    ![Finalización de la acción de HTTP](./media/connectors-native-http/using-action-2.png)
5. Haga clic en la esquina superior izquierda de la barra de herramientas para guardar. La aplicación lógica guardará y publicará (activará).

## <a name="http-trigger"></a>Desencadenador HTTP
Aquí se muestran los detalles del desencadenador que admite este conector. El conector HTTP tiene un desencadenador.

| Desencadenador | Description |
| --- | --- |
| http |Realiza una llamada HTTP y devuelve el contenido de la respuesta. |

## <a name="http-action"></a>Acción HTTP
Aquí se muestran los detalles de la acción que admite este conector. El conector HTTP tiene una acción posible.

| Acción | Description |
| --- | --- |
| http |Realiza una llamada HTTP y devuelve el contenido de la respuesta. |

## <a name="http-details"></a>Detalles HTTP
Las tablas siguientes describen los campos de entrada obligatorios y opcionales para la acción y los detalles de salida correspondientes asociados a su uso.

#### <a name="http-request"></a>Solicitud HTTP
Los siguientes son los campos de entrada para la acción que realiza una solicitud de salida HTTP.
Un * significa que es un campo obligatorio.

| Nombre para mostrar | Nombre de propiedad | Description |
| --- | --- | --- |
| Método* |estático |El verbo HTTP que se usará |
| URI* |uri |El identificador URI de la solicitud HTTP |
| Encabezados |Encabezados |Objeto JSON de los encabezados HTTP que incluir |
| Cuerpo |Cuerpo |Cuerpo de la solicitud HTTP |
| Autenticación |Autenticación |Detalles en la sección [Autenticación](#authentication) |

<br>

#### <a name="output-details"></a>Detalles de salida
Los detalles de la salida de la respuesta HTTP son los siguientes.

| Nombre de propiedad | Tipo de datos | Description |
| --- | --- | --- |
| encabezados |objeto |Encabezados de respuesta |
| Cuerpo |objeto |Objeto de respuesta |
| Código de estado |int |Código de estado HTTP |

## <a name="authentication"></a>Autenticación
La característica Logic Apps del Servicio de aplicaciones de Azure le permite utilizar diferentes tipos de autenticación en los puntos de conexión HTTP. Esta autenticación se puede usar con los conectores **HTTP**, **[HTTP + Swagger](connectors-native-http-swagger.md)** y **[HTTP Webhook](connectors-native-webhook.md)**. Los siguientes tipos de autenticación pueden configurarse:

* [Autenticación básica](#basic-authentication)
* [Autenticación de certificados de clientes](#client-certificate-authentication)
* [Autenticación de OAuth de Azure Active Directory (Azure AD)](#azure-active-directory-oauth-authentication)

#### <a name="basic-authentication"></a>Autenticación básica
Se requiere el siguiente objeto de autenticación para la autenticación básica.
Un * significa que es un campo obligatorio.

| Nombre de propiedad | Tipo de datos | Description |
| --- | --- | --- |
| Type* |type |Tipo de autenticación (debe ser `Basic` para la autenticación básica) |
| Username* |nombre de usuario |Nombre de usuario que se va a autenticar |
| Password* |contraseña |Contraseña para autenticar. |

> [!TIP]
> Si quiere usar una contraseña que no se pueda recuperar de la definición, use un parámetro `securestring` y la [función de definición de flujo de trabajo](http://aka.ms/logicappdocs) `@parameters()`.
> 
> 

Por tanto, debe crear un objeto similar al siguiente en el campo de autenticación:

```javascript
{
    "type": "Basic",
    "username": "user",
    "password": "test"
}
```

#### <a name="client-certificate-authentication"></a>Autenticación de certificados de clientes
Se requiere el siguiente objeto de autenticación para la autenticación de certificados de clientes. Un * significa que es un campo obligatorio.

| Nombre de propiedad | Tipo de datos | Description |
| --- | --- | --- |
| Type* |type |El tipo de autenticación (debe ser `ClientCertificate` para los certificados de cliente SSL) |
| PFX* |pfx |El contenido codificado en base 64 del archivo de intercambio de información personal (PFX) |
| Password* |contraseña |La contraseña para acceder al archivo PFX |

> [!TIP]
> Puede usar un parámetro `securestring` y la [función de definición de flujo de trabajo](http://aka.ms/logicappdocs) `@parameters()` para usar un parámetro que no se pueda leer en la definición después de guardar la aplicación lógica.
> 
> 

Por ejemplo:

```javascript
{
    "type": "ClientCertificate",
    "pfx": "aGVsbG8g...d29ybGQ=",
    "password": "@parameters('myPassword')"
}
```

#### <a name="azure-ad-oauth-authentication"></a>Autenticación de OAuth de Azure AD
Se requiere el siguiente objeto de autenticación para la autenticación de OAuth de Azure AD. Un * significa que es un campo obligatorio.

| Nombre de propiedad | Tipo de datos | Description |
| --- | --- | --- |
| Type* |type |El tipo de autenticación (debe ser `ActiveDirectoryOAuth` para la autenticación de OAuth de Azure AD) |
| Tenant* |tenant |Identificador del inquilino de Azure AD. |
| Audience* |audience |Establézcala en `https://management.core.windows.net/` |
| Client ID* |clientId |Identificador de cliente para la aplicación de Azure AD |
| Secret* |secret |El secreto del cliente que solicita el token |

> [!TIP]
> Puede usar un parámetro `securestring` y la [función de definición de flujo de trabajo](http://aka.ms/logicappdocs) `@parameters()` para usar un parámetro que no se pueda leer en la definición después de guardarse.
> 
> 

Por ejemplo:

```javascript
{
    "type": "ActiveDirectoryOAuth",
    "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "audience": "https://management.core.windows.net/",
    "clientId": "34750e0b-72d1-4e4f-bbbe-664f6d04d411",
    "secret": "hcqgkYc9ebgNLA5c+GDg7xl9ZJMD88TmTJiJBgZ8dFo="
}
```

## <a name="next-steps"></a>Pasos siguientes
Ahora, pruebe la plataforma y [cree una aplicación lógica](../app-service-logic/app-service-logic-create-a-logic-app.md). Puede explorar los demás conectores disponibles en Logic Apps consultando nuestra [lista de API](apis-list.md).




<!--HONumber=Nov16_HO3-->


