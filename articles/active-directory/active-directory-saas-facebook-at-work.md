---
title: 'Tutorial: integración de Azure Active Directory con Facebook at Work | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Facebook at Work.
services: active-directory
documentationcenter: ''
author: asmalser-msft
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2016
ms.author: asmalser

---
# <a name="tutorial:-azure-active-directory-integration-with-facebook-at-work"></a>Tutorial: integración de Azure Active Directory con Facebook at Work
El objetivo de este tutorial es mostrar cómo integrar Facebook at Work con Azure Active Directory (Azure AD).

La integración de Facebook at Work con Azure AD ofrece las siguientes ventajas: 

* Puede controlar en Azure AD quién tiene acceso a Facebook at Work. 
* Puede aprovisionar automáticamente cuentas para los usuarios a los que concedió acceso a Facebook at Work
* Puede permitir que los usuarios inicien sesión automáticamente en Facebook at Work (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central. 

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Requisitos previos
Para configurar la integración de Azure AD con CS Stars, necesita los siguientes elementos:

* Una suscripción de Azure AD
* Facebook at Work con inicio de sesión único habilitado

Para probar los pasos de este tutorial, debe seguir estas recomendaciones:

* No debe usar el entorno de producción, a menos que sea necesario.
* Si no dispone de un entorno de prueba de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/). 

## <a name="adding-facebook-at-work-from-the-gallery"></a>Adición de Facebook at Work desde la galería
Para configurar la integración de Facebook at Work en Azure AD, deberá agregar Facebook at Work desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Facebook at Work desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo del **Portal de Azure clásico**, haga clic en **Active Directory**. 
   
    ![Active Directory][1]
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
    ![Applications][2]
4. Haga clic en **Agregar** en la parte inferior de la página.
   
    ![Aplicaciones][3]
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
    ![Aplicaciones][4]
6. En el cuadro de búsqueda, escriba **Facebook at Work**.
7. En el panel de resultados, seleccione **Facebook at Work** y haga clic en **Completar** para agregar la aplicación.

### <a name="configuring-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD
El objetivo de esta sección es describir cómo habilitar usuarios para que se autentiquen en Facebook at Work con su cuenta de Azure AD a través de la federación basada en el protocolo SAML.

**Para configurar el inicio de sesión único de Azure AD con Facebook at Work, realice los pasos siguientes:**

1. Después de agregar Facebook at Work al Portal de Azure clásico, haga clic en **Configurar inicio de sesión único**.
2. En la pantalla **Configurar dirección URL de la aplicación** , escriba la dirección URL donde los usuarios iniciarán sesión en su aplicación Facebook at Work. Se trata de su Facebook en la dirección URL del inquilino de trabajo (ejemplo: https://example.facebook.com/). Una vez que termine, haga clic en **Siguiente**.
3. En otra ventana del explorador web, inicie sesión en el sitio de la compañía de Facebook at Work como administrador.
4. Siga las instrucciones de la dirección URL siguiente para configurar Facebook at Work para usar Azure AD como proveedor de identidades: [https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/authentication/cloud-providers)
5. Una vez completado, vuelva a las ventanas del explorador que muestran el Portal de Azure clásico, haga clic en la casilla para confirmar que completó el procedimiento y, después, en **Siguiente** y **Completar**.

## <a name="automatically-provisioning-users-to-facebook-at-work"></a>Aprovisionamiento automático de usuarios en Facebook at Work
Azure AD admite la capacidad de sincronizar automáticamente los detalles de la cuenta de usuarios asignados a Facebook at Work. Esta sincronización automática permite a Facebook at Work obtener los datos que necesita para autorizar a los usuarios a acceder, antes de que intenten iniciar sesión por primera vez. También desaprovisiona usuarios de Facebook at Work cuando se revocó el acceso en Azure AD.

El aprovisionamiento automático puede configurarse haciendo clic en **Configurar aprovisionamiento de cuentas** en la ventana del Portal de Azure clásico.

Para obtener más detalles sobre cómo configurar el aprovisionamiento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

## <a name="assigning-users-to-facebook-at-work"></a>Asignación de usuarios en Facebook at Work
Para que los usuarios de AAD aprovisionados puedan ver Facebook at Work en su panel de acceso, deben tener acceso asignado en el Portal de Azure clásico.

**Para asignar usuarios en Facebook at Work:**

1. En la página de inicio de Facebook at Work en el Portal de Azure clásico, haga clic en **Asignar cuentas**.
2. En el menú **Mostrar** , seleccione si desea asignar un usuario o un grupo en Facebook at Work y haga clic en el botón de marca de verificación.
3. En la lista resultante, seleccione los usuarios o el grupo al que desea asignar Facebook at Work.
4. En el pie de página, haga clic en el botón **Asignar** .

## <a name="additional-resources"></a>Recursos adicionales
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md)
* [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cs-stars-tutorial/tutorial_general_04.png







<!--HONumber=Oct16_HO2-->


