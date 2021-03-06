---
title: Azure MFA y AD FS | Microsoft Docs
description: "En esta página de Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA y AD FS"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 44fbba68-6cf9-46c1-a9df-736580b68ae3
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dcf67cfd5f4d44188f119ca40b227b32c684e1f7


---
# <a name="getting-started-with-azure-multifactor-authentication-and-active-directory-federation-services"></a>Introducción a Azure Multi-Factor Authentication y a los Servicios de federación de Active Directory
<center>![Nube](./media/multi-factor-authentication-get-started-adfs/adfs.png)</center>

Si su organización ha federado su Active Directory local con Azure Active Directory mediante AD FS, tiene a su disposición dos opciones para usar Azure Multi-Factor Authentication.

* Recursos de nube seguros a través del uso de Azure Multi-Factor Authentication o los Servicios de federación de Active Directory
* Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication

En la tabla siguiente se resume la experiencia de verificación entre la protección de recursos con Azure Multi-Factor Authentication y AD FS

| Experiencia de verificación: aplicaciones basadas en explorador | Experiencia de verificación: aplicaciones no basadas en explorador |
|:--- |:--- |:--- |
| Protección de recursos de Azure AD con Azure Multi-Factor Authentication |<li>El primer paso de comprobación se realiza localmente utilizando AD FS.</li> <li>El segundo factor es un método basado en teléfono que se lleva a cabo utilizando la autenticación en la nube.</li> |
| Protección de los recursos de Azure AD mediante los servicios de federación de Active Directory |<li>El primer paso de comprobación se realiza localmente utilizando AD FS.</li><li>El segundo paso es realiza localmente respondiendo a la notificación.</li> |

Advertencias sobre las contraseñas de aplicación para usuarios federados:

* Las contraseña de aplicación se comprueban mediante autenticación en la nube y, por lo tanto, omiten las federaciones. La federación solo se usa activamente al configurar una contraseña de aplicación.
* La configuración del control de acceso de cliente local no se cumple con las contraseñas de aplicación.
* Se pierde la capacidad de registro de autenticación local para las contraseñas de aplicación.
* La deshabilitación o eliminación de la cuenta puede tardar hasta 3 horas en la sincronización de directorios, retrasando la deshabilitación o eliminación de las contraseñas de aplicación en la identidad de nube.

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre cómo configurar Azure Multi-Factor Authentication o Servidor Azure Multi-Factor Authentication con AD FS, consulte los siguientes artículos:

* [Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS](multi-factor-authentication-get-started-adfs-cloud.md)
* [Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md)
* [Protección de recursos en la nube y locales mediante Servidor Azure Multi-Factor Authentication con AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md)




<!--HONumber=Nov16_HO2-->


