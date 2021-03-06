---
title: "Obtención de datos de Azure Analysis Services | Microsoft Docs"
description: Aprenda a conectarse y obtener datos de un servidor de Analysis Services en Azure.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: b37f70a0-9166-4173-932d-935d769539d1
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 6b65689c75247af81e916730f125ff39bb49489f


---
# <a name="get-data-from-azure-analysis-services"></a>Obtención de datos de Azure Analysis Services
Una vez que se ha creado un servidor en Azure y se ha implementado un modelo tabular, los usuarios de su organización pueden conectarse y comenzar a explorar los datos.

Azure Analysis Services admite conexiones de cliente mediante [modelos de objetos actualizado](#client-libraries); TOM, AMO, Adomd.Net o MSOLAP, para conectarse mediante xmla al servidor. Por ejemplo, Power BI, Power BI Desktop, Excel o cualquier aplicación cliente de terceros que admita los modelos de objetos.

## <a name="server-name"></a>Nombre de servidor
Al crear un servidor de Analysis Services en Azure, especifique un nombre único y la región donde se creará el servidor. Al especificar el nombre del servidor en una conexión, el esquema de nombres del servidor es:

```
<protocol>://<region>/<servername>
```
 Donde protocol es la cadena **asazure**, region es el URI de la región donde se creó el servidor (por ejemplo, para oeste de EE. UU., westus.asazure.windows.net) y servername es el nombre de su servidor único dentro de la región.

## <a name="get-the-server-name"></a>Obtención del nombre del servidor
Antes de establecer la conexión, es necesario obtener el nombre del servidor. En **Azure Portal** > servidor > **Información general** > **Nombre de servidor**, copie el nombre del servidor completo. Si otros usuarios de su organización también se conectan a este servidor, deseará compartir dicho nombre con ellos. Al especificar un nombre de servidor, se debe usar la ruta de acceso completa.

![Obtención del nombre del servidor en Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

## <a name="connect-in-power-bi-desktop"></a>Conexión en Power BI Desktop
> [!NOTE]
> Esta característica se encuentra en versión preliminar.
> 
> 

1. En [Power BI Desktop](https://powerbi.microsoft.com/desktop/), haga clic en **Obtener datos** > **Bases de datos** > **Azure Analysis Services**.
2. En **Servidor**, pegue el nombre del servidor desde el Portapapeles.
3. En **Base de datos**, si conoce el nombre de la base de datos de modelo tabular o la perspectiva a la que desea conectarse, péguelo aquí. De lo contrario, puede dejar este campo en blanco. Puede seleccionar una base de datos o una perspectiva en la pantalla siguiente.
4. Deje seleccionada la opción **Conectar en directo** predeterminada y presione **Conectar**. Si se le pide que especifique una cuenta, escriba la cuenta de su organización.
5. En **Navegador**, expanda el servidor, seleccione el modelo o la perspectiva a la que desea conectarse y haga clic en **Conectar**. Un solo clic en un modelo o perspectiva muestra todos los objetos de esa vista.

## <a name="connect-in-power-bi"></a>Conexión en Power BI
1. Cree un archivo de Power BI Desktop que tenga una conexión en directo con el modelo del servidor.
2. En [Power BI](https://powerbi.microsoft.com), haga clic en **Obtener datos** > **archivos**. Localice y seleccione el archivo.

## <a name="connect-in-excel"></a>Conexión en Excel
Se admite la conexión al servidor de Azure Analysis Services en Excel mediante Obtener datos en Excel 2016 o Power Query en versiones anteriores. Es obligatorio un [proveedor MSOLAP.7](https://aka.ms/msolap). No se admite la conexión mediante el Asistente para importar tablas en Power Pivot.

1. En Excel 2016, en la cinta de opciones **Datos**, haga clic en **Obtener datos externos** > **De otros orígenes** > **Desde Analysis Services**.
2. En el Asistente para la conexión de datos, en **Nombre del servidor**, pegue el nombre del servidor desde el Portapapeles. A continuación, en **Credenciales de inicio de sesión**, seleccione **	Utilizar el nombre de usuario y la contraseña siguientes** y escriba el nombre de usuario de la organización, por ejemplo nancy@adventureworks.com,, y la contraseña.
   
    ![Conexión en el inicio de sesión de Excel](./media/analysis-services-connect/aas-connect-excel-logon.png)
3. En **Seleccionar base de datos y tabla**, seleccione la base de datos y el modelo o la perspectiva y haga clic en **Finalizar**.
   
    ![Conexión en el modelo de selección de Excel](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>Cadena de conexión
Al conectarse a Azure Analysis Services mediante el modelo de objetos tabular, utilice los siguientes formatos de cadena de conexión:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticación integrada de Azure Active Directory
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
La autenticación integrada selecciona la memoria caché de credenciales de Azure Active Directory, si está disponible. De lo contrario, se muestra la ventana de inicio de sesión de Azure.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticación de Azure Active Directory con el nombre de usuario y la contraseña
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="client-libraries"></a>Bibliotecas de clientes
Al conectarse a Azure Analysis Services desde Excel u otras interfaces, como AsCmd, TOM o ADOMD.NET, debe instalar las bibliotecas de clientes de proveedor más recientes. Obtenga la versión más reciente:  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>

## <a name="next-steps"></a>Pasos siguientes
[Administración del servidor](analysis-services-manage.md)




<!--HONumber=Nov16_HO3-->


