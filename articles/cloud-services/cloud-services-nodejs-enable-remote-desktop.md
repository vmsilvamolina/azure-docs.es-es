---
title: Habilite el Escritorio remoto para el acceso remoto de (Node.js)
description: "Vea cómo habilitar el acceso mediante Escritorio remoto para las máquinas virtuales que hospedan su aplicación Node.js de Azure."
services: cloud-services
documentationcenter: nodejs
author: rmcmurray
manager: erikre
editor: 
ms.assetid: a0141904-c9bc-478d-82af-5bceaca5cf6a
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/22/2016
ms.author: robmcm
translationtype: Human Translation
ms.sourcegitcommit: c1551b250ace3aa6775932c441fcfe28431f8f57
ms.openlocfilehash: 09878cccc847040c59cbf285f40ac6a1a310c993


---
# <a name="enabling-remote-desktop-in-azure"></a>Habilitación de Escritorio remoto en Azure
Escritorio remoto le permite tener acceso al escritorio de una instancia de rol que se ejecuta en Azure. Puede usar una conexión a Escritorio remoto para configurar la máquina virtual o solucionar problemas con la aplicación.

> [!NOTE]
> Este artículo se aplica a las aplicaciones Node.js hospedadas como un servicio de nube de Azure.
> 
> 

## <a name="prerequisites"></a>Requisitos previos
* Instale y configure [Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Implemente una aplicación Node.js en un servicio de nube de Azure Para más información, consulte [Compilación e implementación de una aplicación Node.js en un servicio en la nube de Azure](cloud-services-nodejs-develop-deploy-app.md).

## <a name="step-1-use-azure-powershell-to-configure-the-service-for-remote-desktop-access"></a>Paso 1: usar PowerShell de Azure para configurar el servicio para el acceso de Escritorio remoto
Para utilizar Escritorio remoto, necesita actualizar la definición y configuración de servicio de Azure y con un nombre de usuario, contraseña y certificado. 

Ejecute los siguientes pasos desde el equipo que contenga los archivos de origen de la aplicación.

1. Ejecute **Windows PowerShell** como administrador. (En el **menú Inicio** o la **pantalla Inicio**, busque **Windows PowerShell**).
2. Navegue al directorio que contenga los archivos de configuración del servicio (.cscfg) y de definición del servicio (.csdef).
3. Escriba el siguiente cmdlet de PowerShell:
   
        Enable-AzureServiceProjectRemoteDesktop
4. En el símbolo del sistema, escriba un nombre de usuario y una contraseña.
   
    ![enable-azureserviceprojectremotedesktop][enable-rdp]
5. Para publicar los cambios, escriba el siguiente cmdlet de PowerShell:
   
       Publish-AzureServiceProject
   
   ![publish-azureserviceproject][publish-project]

## <a name="step-2-connect-to-the-role-instance"></a>Paso 2: conectarse a la instancia de rol
Después de publicar la definición del servicio de actualización, puede conectarse a la instancia de rol.

1. En el [Portal de Azure clásico], seleccione **Servicios en la nube** y, a continuación, seleccione el servicio.
   
   ![Portal de Azure clásico][cloud-services]
2. Haga clic en **Instancias** y, a continuación, haga clic en **Producción** o **Ensayo** para ver las instancias del servicio. Seleccione una instancia y, a continuación, haga clic en **Conectar** en la parte inferior de la página.
   
   ![La página Instancias][3]
3. Al hacer clic en **Conectar**, el explorador web le pide que guarde un archivo .rdp. Abra dicho archivo. (Por ejemplo, si usa Internet Explorer, haga clic en **Abrir**.)
   
   ![solicitud para abrir o guardar el archivo .rdp][4]
4. Al abrir el archivo, aparece el siguiente indicador de seguridad:
   
   ![Indicador de seguridad de Windows][5]
5. Haga clic en **Conectar**y, a continuación, aparecerá un indicador de seguridad para que escriba las credenciales de acceso a la instancia. Escriba la contraseña creada en el [paso 1][paso 1: configuración del servicio para el acceso de Escritorio remoto mediante Azure PowerShell] y haga clic en **Aceptar**.
   
   ![indicador de nombre de usuario/contraseña][6]

Cuando se ha establecido la conexión, la conexión a Escritorio remoto muestra el escritorio de la instancia en Azure. 

![Sesión de Escritorio remoto][7]

## <a name="step-3-configure-the-service-to-disable-remote-desktop-access"></a>Paso 3: configurar el servicio para deshabilitar el acceso al Escritorio remoto
Cuando ya no necesite conexiones de Escritorio remoto a las instancias de rol en la nube, deshabilite el acceso de Escritorio remoto mediante [Azure PowerShell].

1. Escriba el siguiente cmdlet de PowerShell:
   
       Disable-AzureServiceProjectRemoteDesktop
2. Para publicar los cambios, escriba el siguiente cmdlet de PowerShell:
   
       Publish-AzureServiceProject

## <a name="additional-resources"></a>Recursos adicionales
* [Acceso remoto a las instancias de rol de Azure] 
* [Uso de Escritorio de remoto con los roles de Azure]
* [Centro para desarrolladores de Node.js](/develop/nodejs/)

[Azure PowerShell]: http://go.microsoft.com/?linkid=9790229&clcid=0x409

[Portal de Azure clásico]: http://manage.windowsazure.com
[publish-project]: ./media/cloud-services-nodejs-enable-remote-desktop/publish-rdp.png
[enable-rdp]: ./media/cloud-services-nodejs-enable-remote-desktop/enable-rdp.png
[cloud-services]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-services-remote.png
[3]: ./media/cloud-services-nodejs-enable-remote-desktop/cloud-service-instance.png
[4]: ./media/cloud-services-nodejs-enable-remote-desktop/rdp-open.png
[5]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-12.png
[6]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-13.png
[7]: ./media/cloud-services-nodejs-enable-remote-desktop/remote-desktop-14.png

[Acceso remoto a las instancias de rol de Azure]: http://msdn.microsoft.com/library/windowsazure/hh124107.aspx
[Uso de Escritorio de remoto con los roles de Azure]: http://msdn.microsoft.com/library/windowsazure/gg443832.aspx



<!--HONumber=Dec16_HO2-->


