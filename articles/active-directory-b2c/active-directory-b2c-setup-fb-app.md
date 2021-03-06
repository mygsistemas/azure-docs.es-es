---
title: "Azure Active Directory B2C: configuración de Facebook | Microsoft Docs"
description: "Proporcione a los consumidores la posibilidad de registro e inicio de sesión con cuentas de Facebook en las aplicaciones protegidas por Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: 
author: swkrish
manager: mbaldwin
editor: bryanla
ms.assetid: b875f235-a1d2-4abb-b9f0-b89beac38a32
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/06/2016
ms.author: swkrish
translationtype: Human Translation
ms.sourcegitcommit: e37c48d6c92a8a2cd480458abdff0a3a1ca9338f
ms.openlocfilehash: c7a3d6f50e65aa79c4f325cd5a1ecbcdccefbad8


---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-facebook-accounts"></a>Azure Active Directory B2C: provisión de registro e inicio de sesión a los consumidores con cuentas de Facebook
## <a name="create-a-facebook-application"></a>Creación de una aplicación de Facebook
Para usar Facebook como proveedor de identidades en Azure Active Directory (Azure AD) B2C, primero debe crear una aplicación de Facebook y suministrarle los parámetros correctos. Necesita una cuenta de Facebook para ello. Si no tiene una, puede obtenerla en [https://www.facebook.com/](https://www.facebook.com/).

1. Vaya al sitio web [Facebook for developers](https://developers.facebook.com/) e inicie sesión con las credenciales de su cuenta de Facebook.
2. Si aún no lo ha hecho, debe registrarse como desarrollador de Facebook. Para ello, haga clic en **Register** (Registrarte) (en la esquina superior derecha de la página), acepte las directivas de Facebook y complete los pasos de registro.
3. Haga clic en **My Apps** (Mis aplicaciones) y luego en **Add a new App** (Agregar una nueva aplicación). Elija **Website** (Sitio web) como plataforma y luego haga clic en **Skip and Create App ID** (Omitir y crear un id. de aplicación).
   
    ![Facebook - Agregar una nueva aplicación](./media/active-directory-b2c-setup-fb-app/fb-add-new-app.png)
   
    ![Facebook - Agregar una nueva aplicación - Sitio web](./media/active-directory-b2c-setup-fb-app/fb-add-new-app-website.png)
   
    ![Facebook - Crear identificador de aplicación](./media/active-directory-b2c-setup-fb-app/fb-new-app-skip.png)
4. En el formulario, especifique un **nombre para mostrar**, un **correo electrónico de contacto** válido, una **categoría adecuada** y haga clic en **Create App ID** (Crear id. de aplicación). Debe aceptar las políticas de la plataforma de Facebook y realizar una comprobación de seguridad en línea.
   
    ![Facebook - Crear un nuevo identificador de aplicación](./media/active-directory-b2c-setup-fb-app/fb-create-app-id.png)
5. En la barra de navegación de la izquierda, haga clic en **Settings** (Configuración).
6. Haga clic en **+Add Platform** (+Agregar plataforma) y, luego, seleccione **Website** (Sitio web).
   
    ![Facebook - Configuración](./media/active-directory-b2c-setup-fb-app/fb-settings.png)
   
    ![Facebook - Configuración - Sitio web](./media/active-directory-b2c-setup-fb-app/fb-website.png)
7. Escriba [https://login.microsoftonline.com/](https://login.microsoftonline.com/) en el campo **Site URL** (URL del sitio) y, luego, haga clic en **Save Changes** (Guardar cambios).
   
    ![Facebook - Dirección URL del sitio](./media/active-directory-b2c-setup-fb-app/fb-site-url.png)
8. Copie el valor de **App ID** (Id. de aplicación). Haga clic en **Show** (Mostrar) y copie el valor de **App Secret** (Secreto de la aplicación). Necesitará ambos para configurar Facebook como proveedor de identidades de su inquilino. El **secreto de la aplicación** es una credencial de seguridad importante.
   
    ![Facebook - Id. de aplicación y el secreto de la aplicación](./media/active-directory-b2c-setup-fb-app/fb-app-id-app-secret.png)
9. Haga clic en **+ Add Product** (+ Agregar producto) en la barra de navegación izquierda y, luego, en el botón **Get Started** (Comenzar) situado junto a **Facebook Login** (Inicio de sesión de Facebook).
   
    ![Facebook - Inicio de sesión de Facebook](./media/active-directory-b2c-setup-fb-app/fb-login.png)
10. Escriba `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` en el campo **Valid OAuth redirect URIs** (URI de redirección de OAuth válido) de la sección **Client OAuth Settings** (Configuración de OAuth de cliente). Reemplace **{tenant}** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com). Haga clic en **Save Changes** (Guardar cambios) en la parte inferior de la página.
    
    ![Facebook - URI de redireccionamiento de OAuth](./media/active-directory-b2c-setup-fb-app/fb-oauth-redirect-uri.png)
11. Para que la aplicación Facebook se puede usar con Azure AD B2C, es necesario ponerla a disposición del público. Para ello, haga clic en **App Review** (Revisión de la aplicación) en la barra navegación izquierda, mueva el conmutador de la parte superior de la página a **YES** (SÍ) y haga clic en **Confirm** (Confirmar).
    
    ![Facebook - Público de la aplicación](./media/active-directory-b2c-setup-fb-app/fb-app-public.png)

## <a name="configure-facebook-as-an-identity-provider-in-your-tenant"></a>Configuración de Facebook como proveedor de identidades del inquilino
1. Siga estos pasos para [ir a la hoja de características de B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) del Portal de Azure.
2. En la hoja de características B2C, haga clic en **Proveedores de identidades**.
3. Haga clic en **+Agregar** en la parte superior de la hoja.
4. Proporcione un **Nombre** descriptivo para la configuración del proveedor de identidades. Por ejemplo, "FB".
5. Haga clic en **Identity provider type** (Tipo de proveedor de identidades), seleccione **Facebook** y haga clic en **OK** (Aceptar).
6. Haga clic en **Set up this identity provider** (Configurar este proveedor de identidades), y escriba el id. y el secreto de la aplicación (de la aplicación de Facebook que creó anteriormente) en los campos **Client ID** y **Client secret** respectivamente.
7. Haga clic en **OK** (Aceptar) y, luego, en **Create** (Crear) para guardar la configuración de Facebook.




<!--HONumber=Jan17_HO1-->


