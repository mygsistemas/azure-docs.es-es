---
title: "Introducción a los informes de Azure Active Directory | Microsoft Docs"
description: Enumera los distintos informes disponibles en los informes de Azure Active Directory.
services: active-directory
documentationcenter: 
author: dhanyahk
manager: femila
editor: 
ms.assetid: 7ac99919-8df5-4424-9298-fc7c025ba949
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/07/2016
ms.author: dhanyahk
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6ce0e0ce9004e1b331328fca5830f01b6ce6af6c


---
# <a name="getting-started-with-azure-active-directory-reporting"></a>Introducción a los informes de Azure Active Directory
## <a name="what-it-is"></a>¿Qué es?
Azure Active Directory (Azure AD) incluye informes de seguridad, actividad y auditoría para el directorio. Esta es una lista de los informes incluidos:

### <a name="security-reports"></a>Informes de seguridad
* Inicios de sesión desde orígenes desconocidos
* Inicios de sesión tras varios errores
* Inicios de sesión desde varias ubicaciones geográficas
* Inicios de sesión desde direcciones IP con actividad sospechosa
* Actividad de inicio de sesión irregular
* Inicios de sesión desde dispositivos posiblemente infectados
* Usuarios con actividad de inicio de sesión anómala

### <a name="activity-reports"></a>Informes de actividad
* Uso de la aplicación: resumen
* Uso de la aplicación: detallado
* Panel de la aplicación
* Errores de aprovisionamiento de cuentas
* Dispositivos de usuario individuales
* Actividad de usuario individual
* Informe de actividad de grupos
* Informe de actividad de registro de restablecimiento de contraseña
* Actividad de restablecimiento de contraseña

### <a name="audit-reports"></a>Informes de auditoría
* Informe de auditoría de directorio

> [!TIP]
> Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="how-it-works"></a>Cómo funciona
### <a name="reporting-pipeline"></a>Canalización de informes
La canalización de informes consta de tres pasos principales. Cada vez que un usuario inicia sesión o se realiza una autenticación, ocurre lo siguiente:

* En primer lugar, el usuario se autentica (correctamente o no) y el resultado se almacena en las bases de datos de servicio de Azure Active Directory.
* En intervalos regulares, se procesan todos los inicios de sesión recientes. En este momento, la seguridad y los algoritmos de actividades anómalas buscan todos los inicios de sesión recientes en busca de actividad sospechosa.
* Después del procesamiento, los informes se escriben, se almacenan en caché y se ofrecen en el Portal de Azure clásico.

### <a name="report-generation-times"></a>Tiempos de generación de informes
Debido al gran volumen de autenticaciones e inicios de sesión procesados por la plataforma de Azure AD, los inicios de sesión más recientes tienen una antigüedad media de una hora. En casos excepcionales, se puede tardar hasta 8 horas en procesar los inicios de sesión más recientes.

Puede encontrar el inicio de sesión procesado más reciente examinando el texto de ayuda en la parte superior de cada informe.

![Texto de ayuda en la parte superior de cada informe](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [!TIP]
> Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="getting-started"></a>Introducción
### <a name="sign-into-the-azure-classic-portal"></a>Iniciar sesión en el Portal de Azure clásico
En primer lugar, tendrá que iniciar sesión en el [Portal de Azure clásico](https://manage.windowsazure.com) como administrador global o de cumplimiento. También debe ser administrador de servicios de suscripción de Azure o coadministrador, o usar la suscripción de Azure de acceso a Azure AD.

### <a name="navigate-to-reports"></a>Ir a los informes
Para ver los informes, diríjase a la pestaña Informes en la parte superior del directorio.

Si es la primera vez que visualiza los informes, deberá aceptar un cuadro de diálogo para poder verlos. Esto es para garantizar que los administradores de la organización permiten ver estos datos, que pueden considerarse información privada en algunos países.

![Cuadro de diálogo](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Exploración de cada informe
Vaya a cada informe para ver los datos recopilados y los inicios de sesión procesados. Puede encontrar una [lista de todos los informes aquí](active-directory-reporting-guide.md).

![Todos los informes](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Descarga de los informes como CSV
Cada informe se puede descargar como un archivo CSV (valores separados por comas). Puede utilizar estos archivos en Excel, PowerBI o programas de análisis de terceros para seguir analizando los datos.

Para descargar cualquier informe como CSV, vaya hasta el informe y haga clic en "Descargar" en la parte inferior.

![Botón Descargar](./media/active-directory-reporting-getting-started/downloadButton.png)

> [!TIP]
> Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="next-steps"></a>Pasos siguientes
### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Personalización de las alertas para la actividad de inicio de sesión anómala
Vaya a la pestaña "Configurar" del directorio.

Desplácese a la sección "Notificaciones".

Habilite o deshabilite la sección "Notificaciones de correo electrónico de inicios de sesión anómalos".

![Sección "Notificaciones"](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Integración con la API de informes de Azure AD
Consulte [Introducción a la API de informes](active-directory-reporting-api-getting-started.md).

### <a name="engage-multifactor-authentication-on-users"></a>Uso de Multi-Factor Authentication en usuarios
Seleccione un usuario en un informe.

Haga clic en el botón "Habilitar MFA" en la parte inferior de la pantalla.

![Botón Multi-Factor Authentication  en la parte inferior de la pantalla](./media/active-directory-reporting-getting-started/mfaButton.png)

> [!TIP]
> Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).
> 
> 

## <a name="learn-more"></a>Más información
### <a name="audit-events"></a>Eventos de auditoría
Obtenga información acerca de qué eventos se auditan en el directorio en [Eventos de auditoría de informes de Azure Active Directory](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>Integración de la API
Consulte [Introducción a la API de generación de informes de Azure Active Directory](active-directory-reporting-api-getting-started.md) y la [documentación de referencia de la API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### <a name="get-in-touch"></a>Ponerse en contacto
Envíe un correo electrónico a [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) para trasmitir sus comentarios, solicitar ayuda o plantear las preguntas que tenga.

> [!TIP]
> Para obtener más documentación sobre informes de Azure AD, vea [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md).
> 
> 




<!--HONumber=Nov16_HO2-->


