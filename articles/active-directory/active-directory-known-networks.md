---
title: "Redes conocidas en el Portal de Azure clásico | Microsoft Docs"
description: "Mediante la configuración de redes conocidas, puede evitar que direcciones IP que son propiedad de su organización estén incluidas en los informes &quot;Inicios de sesión desde varias ubicaciones geográficas&quot; e &quot;Inicios de sesión desde direcciones IP con actividad sospechosa&quot;."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: f56e042a-78d5-4ea3-be33-94004f2a0fc3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: c8f41a5f9dcb6a069850c59e18dfdcfa92daa333
ms.openlocfilehash: f14ced0c578fc54a48dd027aedfc24ad3478aef4


---
# <a name="known-networks"></a>Redes conocidas

> [!div class="op_single_selector"]
> * [Portal de Azure clásico](active-directory-known-networks.md)
> * [Azure Portal](active-directory-known-networks-azure-portal.md)
> 
> 


Puede usar los informes de acceso y uso de Active Directory de Azure para proporcionar visibilidad sobre la integridad y la seguridad del directorio de su organización. Con esta información, un administrador de directorios puede determinar mejor dónde puede haber posibles riesgos de seguridad de modo que pueda planear adecuadamente la mitigación de estos riesgos.

Es posible que los informes "*Inicios de sesión desde varias ubicaciones geográficas*" e "*Inicios de sesión desde direcciones IP con actividad sospechosa*" marquen incorrectamente las direcciones IP que en realidad son propiedad de su organización. 

Por ejemplo, esto puede ocurrir cuando: 

* Un usuario de su oficina de Boston que ha iniciado sesión de forma remota en su centro de datos en San Francisco desencadena el informe "Inicios de sesión desde varias ubicaciones geográficas". 
* Un usuario de su organización que intenta iniciar sesión varias veces con una contraseña incorrecta desencadena el informe "Inicios de sesión desde direcciones IP con actividad sospechosa". 

Para evitar estos casos de generación de informes de seguridad engañosos, debe agregar intervalos de direcciones IP conocidas a la lista de direcciones IP públicas de su organización.    

### <a name="to-add-your-organizations-public-ip-address-ranges-perform-the-following-steps"></a>Para ello, realice los pasos siguientes:

1. Inicie sesión en el [Portal de administración de Azure](https://manage.windowsazure.com).

2. En el panel izquierdo, haga clic en **Active Directory**. 

    ![Redes conocidas](./media/active-directory-known-networks/known-netwoks-01.png)

3. En la pestaña **Directorio** , seleccione su directorio.

4. En el menú de la parte superior, haga clic en **Configurar**. 

    ![Redes conocidas](./media/active-directory-known-networks/known-netwoks-02.png)

5. En la pestaña Configurar, vaya a **los intervalos de direcciones IP públicos de las organizaciones** 

    ![Redes conocidas](./media/active-directory-known-networks/known-netwoks-03.png)

6. Haga clic en **Agregar intervalos de direcciones IP conocidas**.

7. Agregue los intervalos de direcciones en el cuadro de diálogo que aparece y, luego, haga clic en el botón de comprobación cuando haya terminado. 

    ![Redes conocidas](./media/active-directory-known-networks/known-netwoks-04.png)

**Recursos adicionales:**

* [Visualización de los informes de acceso y uso](active-directory-view-access-usage-reports.md)
* [Inicios de sesión desde direcciones IP con actividad sospechosa](active-directory-reporting-sign-ins-from-ip-addresses-with-suspicious-activity.md)
* [Inicios de sesión desde varias ubicaciones geográficas](active-directory-reporting-sign-ins-from-multiple-geographies.md)




<!--HONumber=Jan17_HO3-->


