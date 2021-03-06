---
title: "Azure AD Connect: actualización desde una versión anterior | Microsoft Docs"
description: "Se explican los diferentes métodos para realizar la actualización a la versión más reciente de Azure Active Directory Connect, entre los que se incluyen una actualización local y una migración oscilante."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 085706dacdcb0cd5a4169ccac4dc7fd8b8ddb6e0
ms.lasthandoff: 03/03/2017


---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: actualización de una versión anterior a la versión más reciente
En este tema se describen los distintos métodos que se puede utilizar para actualizar la instalación de Azure Active Directory (Azure AD) Connect a la versión más reciente. Le recomendamos mantenerse al día con las versiones de Azure AD Connect. Los pasos de la sección [Migración oscilante](#swing-migration) también se utilizan al realizar un cambio de configuración considerable.

Si quiere actualizar desde DirSync, consulte en su lugar [Azure AD Connect: actualización de Windows Azure Active Directory Sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

Hay algunas estrategias distintas que se pueden usar para actualizar Azure AD Connect.

| Método | Description |
| --- | --- |
| [Actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md) |Este es el método más sencillo para los clientes con una instalación rápida. |
| [Actualización local](#in-place-upgrade) |Si tiene un solo servidor, puede actualizar la instalación local en el mismo servidor. |
| [Migración oscilante](#swing-migration) |Con dos servidores, puede preparar uno de los servidores con la nueva versión o configuración y cambiar el servidor activo cuando esté listo. |

Para obtener información acerca de los permisos, consulte los [permisos requeridos para realizar una actualización](active-directory-aadconnect-accounts-permissions.md#upgrade).

> [!NOTE]
> Después de que haya habilitado el nuevo servidor de Azure AD Connect para que empiece a sincronizar los cambios en Azure AD, no debe volver a usar DirSync o Sincronización de Azure AD. No se admite el cambio a una versión anterior de Azure AD Connect de los clientes heredados, entre los que se incluyen DirSync y Sincronización de Azure AD, y puede causar problemas, como la pérdida de datos en Azure AD.

## <a name="in-place-upgrade"></a>Actualización local
Una actualización local sirve para migrar de Azure AD Sync o Azure AD Connect. No sirve para mover de DirSync ni para una solución con Forefront Identity Manager (FIM) + Azure AD Connector.

Este método es adecuado cuando tiene un único servidor y menos de unos 100 000 objetos. Si hay cambios en las reglas de sincronización de fábrica, después de la actualización, se producen una importación y una sincronización completas. Este método garantiza que la nueva configuración se aplica a todos los objetos existentes en el sistema. Esta ejecución puede tardar varias horas, según el número de objetos que se encuentren en el ámbito del motor de sincronización. La sincronización delta normal programada (que se realiza cada 30 minutos de forma predeterminada) se suspende, pero continúa la sincronización de contraseñas. La actualización local se podría realizar durante un fin de semana. Si no hay ningún cambio en la configuración de fábrica con la nueva versión de Azure AD Connect, en su lugar se inicia una importación y sincronización delta normales.  
![Actualización local](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Si se realizaron cambios en las reglas de sincronización originales, estas volverán a su configuración predeterminada tras la actualización. Para asegurarse de que la configuración se mantiene entre una actualización y la siguiente, asegúrese de que los cambios se realizan como se describe en [Azure AD Connect Sync: procedimientos recomendados de cambio de la configuración predeterminada](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Migración oscilante
Si tiene una implementación compleja o muchos objetos, puede que no resulte práctico realizar una actualización local en el sistema activo. En el caso de algunos clientes, este proceso podría tardar varios días y durante este tiempo no se procesará ningún cambio delta. Este método también se puede utilizar cuando se planea realizar cambios sustanciales en la configuración y se desea probarlos antes de insertarlos en la nube.

El método recomendado para estos escenarios es usar una migración oscilante. Se necesitan (al menos) dos servidores, uno activo y otro provisional. El primero (que se muestra con líneas azules continuas en la siguiente imagen) es el responsable de la carga de producción activa. El segundo (que se muestra con líneas discontinuas de color púrpura) está preparado con la nueva versión o configuración. Cuando esté totalmente preparado, se activa el servidor. El servidor activo anterior, que ahora tiene la versión o configuración anteriores instaladas, se convierte en el servidor provisional y se actualiza.

Los dos servidores pueden usar versiones diferentes. Por ejemplo, el servidor activo que planea retirar puede utilizar Sincronización de Azure AD y el nuevo servidor de ensayo puede usar Azure AD Connect. Si usa la migración oscilante para desarrollar una configuración nueva, es aconsejable tener las mismas versiones en ambos servidores.  
![Servidor provisional](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> Algunos clientes prefieren tener tres o cuatro servidores para este escenario. Cuando se actualiza el servidor de ensayo, deja de tenerse un servidor de copia de seguridad para la [recuperación ante desastres](active-directory-aadconnectsync-operations.md#disaster-recovery). Con tres o cuatro servidores, se puede preparar un conjunto de servidores principales o en espera con la nueva versión, lo que garantiza que siempre hay un servidor provisional listo para tomar el control.

Estos pasos también sirven para migrar de Azure AD Sync o de una solución con FIM + Azure AD Connector. Estos pasos no sirven para DirSync, pero el mismo método de migración oscilante (también llamada implementación paralela) con los pasos para DirSync se encuentra en [Azure AD Connect: actualización de Azure Active Directory Sync (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="use-a-swing-migration-to-upgrade"></a>Uso de una migración oscilante para realizar la actualización
1. Si usa Azure AD Connect en ambos servidores y planea realizar solo un cambio en la configuración, asegúrese de que tanto el servidor activo como el provisional utilizan la misma versión. Esto facilitará la posterior comparación para detectar diferencias. Si va a realizar la actualización desde Sincronización de Azure AD, los servidores tienen versiones diferentes. Si va a realizar la actualización desde una versión anterior de Azure AD Connect, es aconsejable que comience por los dos servidores que tengan la misma versión, pero no es obligatorio.
2. Si ha realizado una configuración personalizada y el servidor provisional no la tiene, siga los pasos que se indican en [Movimiento de una configuración personalizada del servidor activo al servidor provisional](#move-custom-configuration-from-active-to-staging-server).
3. Si va a realizar la actualización desde una versión anterior de Azure AD Connect, actualice el servidor provisional a la versión más reciente. Si va a migrar desde Sincronización de Azure AD, instale Azure AD Connect en el servidor provisional.
4. Permita que el motor de sincronización ejecute la importación y la sincronización completas en el servidor provisional.
5. Compruebe que la nueva configuración no ha provocado cambios inesperados, para lo que puede seguir los pasos de que se indican en "Verificar" en [Comprobación de la configuración de un servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Si algo no funciona según lo esperado, corríjalo, ejecute la importación y la sincronización, y compruebe los datos hasta que estén a su gusto, siguiendo los pasos anteriores.
6. Cambie el servidor de ensayo para que sea el servidor activo. Este es el paso final: "Cambio de servidor activo" en [Comprobación de la configuración de un servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Si va a actualizar Azure AD Connect, actualice el servidor que está actualmente en modo provisional a la versión más reciente. Siga los mismos pasos que antes para actualizar los datos y la configuración. Si va a actualizar desde Azure AD Sync, puede desactivar y retirar el servidor anterior.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>Movimiento de una configuración personalizada del servidor activo al servidor provisional
Si ha realizado cambios en la configuración del servidor activo, debe asegurarse de que los mismos cambios se aplican en el servidor provisional. Para ayudar a solucionar este cambio, puede usar el [documentador de configuración de Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

Puede mover las reglas de la sincronización personalizada que ha creado mediante PowerShell. Si realiza otros cambios, debe aplicarlos de la misma manera en ambos sistemas, y no puede migrarlos. El [documentador de configuración](https://github.com/Microsoft/AADConnectConfigDocumenter) puede ayudar a comparar los dos sistemas para asegurarse de que son idénticos. La herramienta también puede ayudar a automatizar los pasos descritos en esta sección.

Debe configurar los siguientes de la misma forma en ambos servidores:

* Conexión a los mismos bosques
* Filtrado por dominio y unidad organizativa
* Las mismas características opcionales, como la sincronización de contraseña y la escritura diferida de contraseñas

**Movimiento las reglas de sincronización personalizadas**  
Para mover reglas de sincronización personalizadas, siga estos pasos:

1. Abra el **Editor de reglas de sincronización** en el servidor activo.
2. Seleccione una regla personalizada. Haga clic en **Exportar**. Se abre una ventana del Bloc de notas. Guarde el archivo temporal con una extensión PS1. Lo convierte en un script de PowerShell. Copie el archivo PS1 en el servidor provisional.  
   ![Exportación de reglas de sincronización](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. El GUID del Conector del servidor provisional es diferente, por lo que debe cambiarse. Para obtener el GUID, inicie el **Editor de reglas de sincronización**, seleccione una de las reglas originales que represente el mismo sistema conectado y haga clic en **Exportar**. Reemplace el GUID del archivo PS1 por el GUID del servidor de ensayo.
4. En un símbolo del sistema de PowerShell, ejecute el archivo PS1. Esta acción crea la regla de sincronización personalizada en el servidor provisional.
5. Repita esta operación para todas las reglas personalizadas.

## <a name="next-steps"></a>Pasos siguientes
Más información acerca de la [integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

