---
title: "Configuración de la comprobación en dos pasos para mi cuenta profesional o educativa | Microsoft Docs"
description: "Si su empresa configura Azure Multi-Factor Authentication, deberá registrarse para la comprobación en dos pasos. Aprenda a configurar. "
services: multi-factor-authentication
keywords: "cómo usar azure directory, active directory en la nube, tutorial de active directory"
documentationcenter: 
author: kgremban
manager: femila
editor: pblachar
ms.assetid: 46f83a6a-dbdd-4375-8dc4-e7ea77c16357
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 4ff028f88e984f28bc0f4a228aabed1fabc90560
ms.openlocfilehash: 6dafec65b2e8091954cae39994d0013d9fd06558


---
# <a name="set-up-my-account-for-two-step-verification"></a>Configuración de mi cuenta para la comprobación en dos pasos
La comprobación en dos pasos es un paso adicional de seguridad que ayuda a proteger su cuenta para que el acceso para las demás personas sea más complicado. Si está leyendo este artículo, probablemente tiene un correo electrónico del administrador del trabajo o la escuela sobre Multi-Factor Authentication. O quizás ha intentado iniciar sesión y tiene un mensaje pidiéndole que configure la comprobación de seguridad adicional. Si es así, **no puede iniciar sesión hasta que haya completado el proceso de inscripción automática**.

Este artículo le ayudará a configurar su **cuenta profesional o educativa**. Si desea habilitar la comprobación en dos pasos para su propia cuenta personal de Microsoft, consulte [Acerca de la comprobación en dos pasos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).

## <a name="determine-how-you-will-use-multi-factor-authentication"></a>Decisión sobre cómo va a utilizar la autenticación multifactor
La comprobación en dos pasos funciona solicitándole dos unidades de identificación cuando inicie sesión. En primer lugar, le pedimos su nombre de usuario y la contraseña como de costumbre. A continuación, llamamos a un teléfono que sabemos que es suyo y usted confirma que el intento de inicio de sesión es legítimo.  

Para comenzar con el proceso de configuración, intente iniciar sesión en su cuenta como suele hacerlo. Si su administrador ha configurado la cuenta para una comprobación en dos pasos, se le solicitará que inicie el proceso de inscripción automática. Puede comenzar este proceso haciendo clic en **Configurar ahora**.

![Configuración](./media/multi-factor-authentication-end-user-first-time/first.png)

La primera pregunta en el proceso de inscripción es cómo desea ponerse en contacto con nosotros. Eche un vistazo a las opciones de la tabla y utilice los vínculos para ir a los pasos de configuración para cada método.

| Método de contacto | Descripción |
| --- | --- |
| [Aplicación móvil](#use-a-mobile-app-as-the-contact-method) |- **Recibir notificaciones de comprobación.** Esta opción inserta una notificación en la aplicación del autenticador en su smartphone o tableta. Vea la notificación y, si es legítima, seleccione **Autenticar** en la aplicación. Puede que su trabajo o escuela requiera que escriba un PIN para autenticarse.<br>- **Usar el código de comprobación.** En este modo, la aplicación del autenticador genera un código de comprobación que se actualiza cada 30 segundos. Escriba el código de comprobación más reciente en la interfaz de inicio de sesión.<br>La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073). |
| [Llamada de teléfono móvil o texto](#use-your-mobile-phone-as-the-contact-method) |- **Llamada de teléfono** realiza una llamada de voz automatizada al número de teléfono que proporcione. El usuario responde a la llamada y pulsa # en el teclado del teléfono para autenticarse.<br>- **Mensaje de texto** envía un mensaje de texto que contiene un código de comprobación. Según la solicitud en el texto, responda al mensaje de texto o escriba el código de comprobación proporcionado en la interfaz de inicio de sesión. |
| [Llamada de teléfono de la oficina](#use-your-office-phone-as-the-contact-method) |Realiza una llamada de voz automatizada al número de teléfono que proporcione. El usuario responde a la llamada y pulsa # en el teclado del teléfono para autenticarse. |

## <a name="use-a-mobile-app-as-the-contact-method"></a>Usar una aplicación móvil como método de contacto
Con este método se requiere que instale una aplicación de autenticador en el teléfono o tableta. Los pasos de este artículo se basan en la aplicación Microsoft Authenticator, que está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

1. Seleccione **Aplicación móvil** en la lista desplegable.
2. Seleccione **Recibir notificaciones de comprobación** o **Usar código de comprobación** y, luego, seleccione **Configurar**.

    ![Pantalla Comprobación de seguridad adicional](./media/multi-factor-authentication-end-user-first-time/mobileapp.png)
3. En el teléfono o tableta, abra la aplicación y seleccione **+** para agregar una cuenta. (En dispositivos Android, seleccione los tres puntos y, luego **Agregar cuenta**).
4. Especifique que quiere agregar una cuenta profesional o educativa. Se abre el analizador de código QR en su teléfono. Si la cámara no funciona correctamente, puede seleccionar escribir la información de su compañía manualmente. Para más información, consulte [Incorporación manual de una cuenta](#add-an-account-manually).  
5. Digitalice la imagen de código QR que apareció con la pantalla para configurar la aplicación móvil.  Seleccione **Listo** para cerrar la pantalla de código QR.  

    ![Pantalla de código QR](./media/multi-factor-authentication-end-user-first-time/scan2.png)
6. Cuando termine la activación por teléfono, seleccione **Contact me**(Contacto).  Con este paso se envía una notificación o un código de verificación al teléfono. Seleccione **Comprobar**.  
7. Si su empresa requiere un PIN para aprobar la comprobación de inicio de sesión, escríbalo.

    ![Cuadro para escribir un NIP](./media/multi-factor-authentication-end-user-first-time/scan3.png)
8. Cuando haya terminado de escribir el PIN, seleccione **Cerrar**. A estas alturas, la verificación debería haberse realizado correctamente.
9. Lo recomendable es introducir el número de teléfono móvil por si perdiera el acceso a la aplicación móvil. Especifique el país en la lista desplegable y escriba su número de teléfono móvil en la casilla junto al país. Seleccione **Siguiente**.
10. En este momento, deberá configurar contraseñas de aplicación para las aplicaciones sin explorador como Outlook 2010 o anterior, o la aplicación de correo electrónico nativa en dispositivos de Apple. Esto es porque algunas aplicaciones no admiten la comprobación en dos pasos. Si no utiliza estas aplicaciones, haga clic en **Hecho** y omita el resto de los pasos.
11. Si está usando estas aplicaciones, copie la contraseña de aplicación proporcionada y péguela en la aplicación en lugar de la contraseña normal. Puede utilizar la misma contraseña de aplicación para varias aplicaciones. Para más información, [ayuda con contraseñas de aplicación].
12. Haga clic en **Done**(Listo).

### <a name="add-an-account-manually"></a>Incorporación manual de una cuenta
Si desea agregar una cuenta a la aplicación móvil manualmente, en lugar de usar el lector QR, siga estos pasos.

1. Seleccione el botón **Especificar cuenta manualmente** .  
2. Especifique el código y la URL que se ofrece en la misma página que le muestra el código de barras. Esta información aparece en los cuadros **Código** y **Dirección URL** en la aplicación móvil.

    ![Configuración](./media/multi-factor-authentication-end-user-first-time/barcode2.png)
3. Cuando termine la activación, seleccione **Contact me**(Contacto). Con este paso se envía una notificación o un código de verificación al teléfono. Seleccione **Comprobar**.

## <a name="use-your-mobile-phone-as-the-contact-method"></a>Usar el teléfono móvil como método de contacto
1. Seleccione **Teléfono de autenticación** en la lista desplegable.  

    ![Configuración](./media/multi-factor-authentication-end-user-first-time/phone.png)  
2. Seleccione el país en la lista desplegable y escriba su número de teléfono móvil.
3. Seleccione el modo que desea usar con su teléfono móvil: texto o llamada.
4. Seleccione **Contactar** para comprobar el número de teléfono. Según el modo seleccionado, le escribiremos o le llamaremos. Siga las instrucciones proporcionadas en la pantalla y, luego, seleccione **Comprobar**.
5. En este momento, deberá configurar contraseñas de aplicación para las aplicaciones sin explorador como Outlook 2010 o anterior, o la aplicación de correo electrónico nativa en dispositivos de Apple. Esto es porque algunas aplicaciones no admiten la comprobación en dos pasos. Si no utiliza estas aplicaciones, haga clic en **Hecho** y omita el resto de los pasos.
6. Si está usando estas aplicaciones, copie la contraseña de aplicación proporcionada y péguela en la aplicación en lugar de la contraseña normal. Puede utilizar la misma contraseña de aplicación para varias aplicaciones. Para más información, [ayuda con contraseñas de aplicación].
7. Haga clic en **Done**(Listo).

## <a name="use-your-office-phone-as-the-contact-method"></a>Usar el teléfono de la oficina como método de contacto
1. Seleccione **Teléfono de la oficina** en la lista desplegable  

    ![Configuración](./media/multi-factor-authentication-end-user-first-time/office.png)  
2. El cuadro de número de teléfono se rellena automáticamente con la información de contacto de su empresa. Si el falta el número o es incorrecto, consulte al administrador para realizar cambios.
3. Seleccione **Contactar** para comprobar el número de teléfono y le llamaremos. Siga las instrucciones proporcionadas en la pantalla y, luego, seleccione **Comprobar**.
4. En este momento, deberá configurar contraseñas de aplicación para las aplicaciones sin explorador como Outlook 2010 o anterior, o la aplicación de correo electrónico nativa en dispositivos de Apple. Esto es porque algunas aplicaciones no admiten la comprobación en dos pasos. Si no utiliza estas aplicaciones, haga clic en **Hecho** y omita el resto de los pasos.
5. Si está usando estas aplicaciones, copie la contraseña de aplicación proporcionada y péguela en la aplicación en lugar de la contraseña normal. Puede utilizar la misma contraseña de aplicación para varias aplicaciones. Para más información, consulte [¿Cuáles son las contraseñas de aplicación?](multi-factor-authentication-end-user-app-passwords.md)
6. Haga clic en **Done**(Listo).

## <a name="next-steps"></a>Pasos siguientes
* Cambiar las opciones preferidas y [administrar la configuración de la comprobación en dos pasos](multi-factor-authentication-end-user-manage-settings.md)
* Configure las [contraseñas de aplicación](multi-factor-authentication-end-user-app-passwords.md) para las aplicaciones de dispositivos nativos que no admiten la comprobación en dos pasos.
* Consulte la [aplicación Microsoft Authenticator](microsoft-authenticator-app-how-to.md) para una autenticación segura rápida incluso cuando no tiene el servicio de celda.



<!--HONumber=Dec16_HO4-->


