---
title: Conectores de la interfaz de usuario de Synchronization Service Manager de Azure AD | Microsoft Docs
description: "Conozca la pestaña Conectores de Synchronization Service Manager para Azure AD Connect."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: bd2240678fed44db748ae062bdf91e457159b4a2
ms.lasthandoff: 03/04/2017

---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Uso de conectores con Sync Service Manager de Azure AD Connect

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

La pestaña Conectores se usa para administrar todos los sistemas a los que está conectado el motor de sincronización.

## <a name="connector-actions"></a>Acciones del conector
| Acción | Comentario |
| --- | --- |
| Crear |No usar. Para conectarse a los bosques de AD adicionales, use el Asistente para instalación. |
| Propiedades |Se usa para el filtrado por dominio y unidad organizativa. |
| [Eliminar](#delete) |Se usa para eliminar los datos en el espacio del conector o eliminar la conexión a un bosque. |
| [Configurar perfiles de ejecución](#configure-run-profiles) |A excepción del filtrado de dominio, no es necesario configurar ninguna otra opción. Puede utilizar esta acción para ver los perfiles de ejecución ya configurados. |
| Ejecute |Se usa para iniciar una ejecución única de un perfil. |
| Detención |Detiene un conector que esté ejecutando un perfil. |
| Exportar conector |No usar. |
| Importar conector |No usar. |
| Actualizar conector |No usar. |
| Actualizar esquema |Actualiza el esquema en caché. Es preferible usar la opción en el Asistente para la instalación, ya que también actualizará las reglas de sincronización. |
| [Espacio del conector de búsqueda](#search-connector-space) |Se usa para buscar objetos y realizar un [seguimiento de un objeto y sus datos a través del sistema](#follow-an-object-and-its-data-through-the-system). |

### <a name="delete"></a>Eliminar
La acción de eliminación se usa para dos objetivos diferentes.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

Con la opción **Delete connector space only** (Eliminar solo el espacio del conector) se eliminarán todos los datos, pero se mantendrá la configuración.

Con la opción **Delete Connector and connector space** (Eliminar el conector y el espacio del conector) se eliminarán los datos y la configuración. Esta opción se usa cuando no se desea volver a conectarse a un bosque.

Ambas sincronizarán todos los objetos y actualizarán los objetos del metaverso. Se trata de una operación de larga duración.

### <a name="configure-run-profiles"></a>Configurar perfiles de ejecución
Esta opción permite ver los perfiles de ejecución configurados para un conector.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>Espacio del conector de búsqueda
La acción del espacio del conector de búsqueda es útil para buscar objetos y solucionar problemas de datos.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

Empiece seleccionando un **ámbito**. Puede buscar según los datos (RDN, DN, delimitador, subárbol) o el estado del objeto (todas las demás opciones).  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
Por ejemplo, si hace una búsqueda de un subárbol, obtiene todos los objetos de una unidad organizativa.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
En esta cuadrícula, puede seleccionar un objeto, elegir las **propiedades** y [realizar un seguimiento](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) desde el espacio del conector de origen, a través del metaverso, y hasta el espacio del conector de destino.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la configuración de la [Sincronización de Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

