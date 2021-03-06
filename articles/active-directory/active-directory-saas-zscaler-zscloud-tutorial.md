---
title: "Tutorial: Integración de Azure Active Directory con Zscaler ZSCloud | Microsoft Docs"
description: "Aprenda cómo usar Zscaler ZSCloud con Azure Active Directory para habilitar el inicio de sesión único, el aprovisionamiento automatizado, etc."
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 411d5684-a780-410a-9383-59f92cf569b5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 7d5438776882d8ce3d93718d35efe9fa2562478f
ms.openlocfilehash: 455a32848d4f49071bb4d81574c41f65349078e1
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-zscloud"></a>Tutorial: Integración de Azure Active Directory con Zscaler ZSCloud
El objetivo de este tutorial es mostrar la integración de Azure y ZScaler ZSCloud.  
En la situación descrita en este tutorial se supone que ya cuenta con los elementos siguientes:

* Una suscripción de Azure válida
* Una suscripción habilitada para el inicio de sesión único en ZScaler ZSCloud

Después de completar este tutorial, los usuarios de Azure AD que ha asignado a ZScaler ZSCloud podrán realizar un inicio de sesión único en la aplicación en el sitio de la compañía de ZScaler ZSCloud (inicio de sesión iniciado por el proveedor del servicio) o con la [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md)

La situación descrita en este tutorial consta de los siguientes bloques de creación:

1. Habilitación de la integración de aplicaciones para ZScaler ZSCloud
2. Configuración del inicio de sesión único
3. Configuración de los valores de proxy
4. Configuración del aprovisionamiento de usuario
5. Asignación de usuarios

![Escenario](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800275.png "Escenario")

## <a name="enabling-the-application-integration-for-zscaler-zscloud"></a>Habilitación de la integración de aplicaciones para ZScaler ZSCloud
El objetivo de esta sección es describir cómo habilitar la integración de las aplicaciones para ZScaler ZSCloud.

### <a name="to-enable-the-application-integration-for-zscaler-zscloud-perform-the-following-steps"></a>Siga estos pasos para habilitar la integración de aplicaciones para ZScaler ZSCloud:
1. En el panel de navegación izquierdo del Portal de Azure clásico, haga clic en **Active Directory**.
   
   ![Active Directory](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700993.png "Active Directory")
2. En la lista **Directory** , seleccione el directorio cuya integración desee habilitar.
3. Para abrir la vista de aplicaciones, haga clic en **Applications** , en el menú superior de la vista de directorios.
   
   ![Aplicaciones](./media/active-directory-saas-zscaler-zscloud-tutorial/IC700994.png "Aplicaciones")
4. Haga clic en **Agregar** en la parte inferior de la página.
   
   ![Agregar aplicaciones](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749321.png "Agregar aplicaciones")
5. En el cuadro de diálogo **¿Qué desea hacer?**, haga clic en **Agregar una aplicación de la galería**.
   
   ![Agregar una aplicación de la galería](./media/active-directory-saas-zscaler-zscloud-tutorial/IC749322.png "Agregar una aplicación de la galería")
6. En el **cuadro de búsqueda**, escriba **ZScaler ZSCloud**.
   
   ![Galería de aplicaciones](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800276.png "Galería de aplicaciones")
7. En el panel de resultados, seleccione **ZScaler ZSCloud** y, después, haga clic en **Completar** para agregar la aplicación.
   
   ![ZScaler ZSCloud](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800277.png "ZScaler ZSCloud")

## <a name="configuring-single-sign-on"></a>Configuración del inicio de sesión único
El objetivo de esta sección es describir cómo se habilita la autenticación de los usuarios en ZScaler ZSCloud con su cuenta de Azure AD usando el protocolo SAML basado en la federación.  
Como parte de este procedimiento, es necesario cargar un certificado codificado en base&64; en su inquilino de ZScaler ZSCloud.  
Si no está familiarizado con este procedimiento, consulte [Conversión de un certificado binario en un archivo de texto](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>Siga estos pasos para configurar el inicio de sesión único:
1. En el Portal de Azure clásico, en la página de integración de aplicaciones de **ZScaler ZSCloud**, haga clic en **Configurar inicio de sesión único** para abrir el diálogo **Configurar inicio de sesión único**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800278.png "Configurar inicio de sesión único")
2. En la página **¿Cómo quiere que los usuarios inicien sesión en ZScaler ZSCloud?**, seleccione **Inicio de sesión único de Microsoft Azure AD** y, después, haga clic en **Siguiente**.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800279.png "Configurar inicio de sesión único")
3. En la página **Configurar URL de aplicación**, en el cuadro de texto **URL de inicio de sesión de ZScaler ZSCloud**, escriba la dirección URL usada por los usuarios para iniciar sesión en su aplicación ZScaler ZSCloud y, luego, haga clic en **Siguiente**.
   
   ![Configurar dirección URL de la aplicación](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800280.png "Configurar dirección URL de la aplicación")
   
   > [!NOTE]
   > Para obtener el valor real para su entorno de su equipo de soporte de ZScaler ZSCloud si lo necesita.
   > 
   > 
4. En la página **Configuración de inicio de sesión único en ZScaler ZSCloud**, para descargar el certificado, haga clic en **Descargar certificado** y, luego, guarde el archivo de certificado en el equipo.
   
   ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800281.png "Configurar inicio de sesión único")
5. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de ZScaler ZSCloud como administrador.
6. En el menú de la parte superior, haga clic en **Administration**(Administración).
   
   ![Administración](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800206.png "Administración")
7. En **Manage Administrators & Roles (Administrar administradores y roles)** haga clic en **Manage Users & Authentication (Administrar usuarios y autenticación)**.
   
   ![Administración de usuarios y autenticación](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800207.png "Administración de usuarios y autenticación")
8. En la sección **Choose Authentication Options for your Organization** (Elegir opciones de autenticación para su organización), lleve a cabo los pasos siguientes:
   
   ![Autenticación](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800208.png "Autenticación")
   
   1. Seleccione **Authenticate using SAML Single Sign-On**(Autenticarse mediante el inicio de sesión único SAML).
   2. Haga clic en **Configure SAML Single Sign-On Parameters**(Configurar parámetros de inicio de sesión único SAML).
9. En la página de diálogo **Configure SAML Single Sign-On Parameters** (Configurar parámetros de inicio de sesión único SAML), realice estos pasos y luego haga clic en **Listo**:
   
   ![Inicio de sesión único](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800209.png "Inicio de sesión único")
   
   1. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ZScaler ZSCloud**, copie el valor de **URL de solicitud de autenticación** y péguelo en el cuadro de texto **URL of the SAML Portal to which users are sent for authentication (URL del portal de SAML al que se envían los usuarios para autenticación)**.
   2. En el cuadro de texto **Attribute containing Login Name (Atributo que contiene el nombre de inicio de sesión)**, escriba **NameID**.
   3. Para cargar el certificado descargado, haga clic en **pem de Zscaler**.
   4. Seleccione **Habilitar aprovisionamiento automático de SAML**.
10. En la página del cuadro de diálogo **Configurar autenticación de usuario** , realice los pasos siguientes:
    
    ![Administración](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800210.png "Administración")
    
    1. Haga clic en **Guardar**.
    2. Haga clic en **Activar ahora**.
11. En el Portal de Azure clásico, en la página de diálogo **Configurar inicio de sesión único en ZScaler ZSCloud**, seleccione la confirmación de configuración de inicio de sesión único y haga clic en **Completar**.
    
    ![Configurar inicio de sesión único](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800282.png "Configurar inicio de sesión único")

## <a name="configuring-proxy-settings"></a>Configuración de los valores de proxy
### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Para definir la configuración de proxy en Internet Explorer
1. Inicie **Internet Explorer**.
2. Seleccione **Opciones de Internet** en el menú **Herramientas** para abrir el diálogo **Opciones de Internet**.
   
   ![Opciones de Internet](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769492.png "Opciones de Internet")
3. Haga clic en la pestaña **Conexiones** .
   
   ![Conexiones](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769493.png "Conexiones")
4. Haga clic en **Configuración de LAN** para abrir el diálogo **Configuración de LAN**.
5. En la sección del servidor proxy, lleve a cabo estos pasos:
   
   ![Servidor proxy](./media/active-directory-saas-zscaler-zscloud-tutorial/IC769494.png "Servidor proxy")
   
   1. Seleccione Usar un servidor proxy para la LAN.
   2. En el cuadro de texto Dirección, escriba **gateway.zscalerone.net**.
   3. En el cuadro de texto Puerto, escriba **80**.
   4. Seleccione **No usar servidor proxy para direcciones locales**.
   5. Haga clic en **Aceptar** para cerrar el diálogo **Configuración de red de área local (LAN)**.
6. Haga clic en **Aceptar** para cerrar el diálogo **Opciones de Internet**.

## <a name="configuring-user-provisioning"></a>Configuración del aprovisionamiento de usuario
Para permitir que los usuarios de Azure AD inicien sesión en ZScaler ZSCloud, deben aprovisionarse a ZScaler ZSCloud.  
En el caso de ZScaler ZSCloud, el aprovisionamiento es una tarea manual.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Siga estos pasos para configurar el aprovisionamiento de usuario:
1. Inicie sesión en su inquilino de **Zscaler** .
2. Haga clic en **Administración**.
   
   ![Administración](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781035.png "Administración")
3. Haga clic en **User Management**(Administración de usuarios).
   
   ![Agregar](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Agregar")
4. En la pestaña **Usuarios**, haga clic en **Agregar**.
   
   ![Agregar](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781037.png "Agregar")
5. En la sección Agregar usuario, lleve a cabo estos pasos:
   
   ![Agregar usuario](./media/active-directory-saas-zscaler-zscloud-tutorial/IC781038.png "Agregar usuario")
   
   1. Escriba el **Id. de usuario**, el **Nombre para mostrar del usuario**, la **Contraseña**, **Confirmar contraseña** y después seleccione **Grupos** y el **Departamento** de una cuenta de AAD válida que quiera aprovisionar.
   2. Haga clic en **Guardar**.

> [!NOTE]
> Puede usar cualquier otra API o herramienta de creación de cuentas de usuario de ZScaler ZSCloud ofrecida por ZScaler ZSCloud para aprovisionar cuentas de usuario de AAD.
> 
> 

## <a name="assigning-users"></a>Asignación de usuarios
Para probar la configuración, debe conceder acceso a los usuarios de Azure AD a los que quiere permitir el uso de su aplicación.

### <a name="to-assign-users-to-zscaler-zscloud-perform-the-following-steps"></a>Para asignar usuarios a ZScaler ZSCloud, lleve a cabo los siguientes pasos:
1. En el Portal de Azure clásico, cree una cuenta de prueba.
2. En la página de integración de aplicaciones de **ZScaler ZSCloud**, haga clic en **Asignar usuarios**.
   
   ![Asignar usuarios](./media/active-directory-saas-zscaler-zscloud-tutorial/IC800283.png "Asignar usuarios")
3. Seleccione su usuario de prueba, haga clic en **Asignar** y en **Sí** para confirmar la asignación.
   
   ![Sí](./media/active-directory-saas-zscaler-zscloud-tutorial/IC767830.png "Sí")

Si desea probar la configuración de inicio de sesión único, abra el Panel de acceso. Para obtener más información sobre el Panel de acceso, vea [Introducción al Panel de acceso](active-directory-saas-access-panel-introduction.md).


