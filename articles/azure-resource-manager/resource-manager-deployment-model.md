---
title: "La implementación de Resource Manager y la implementación clásica | Microsoft Docs"
description: "Describe las diferencias entre el modelo de implementación del Administrador de recursos y el modelo de implementación clásica (o de administración del servicio)."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 7ae0ffa3-c8da-4151-bdcc-8f4f69290fb4
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 4f541e34e7c0696e4074613c4ab0734a096c6d12
ms.openlocfilehash: d6ac6253caaf5af1f8a14a499653c8afcbb7a2d8


---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>La implementación de Azure Resource Manager frente a la implementación clásica: los modelos de implementación y el estado de los recursos
En este tema, hablamos sobre el modelo de implementación de Azure Resource Manager y el modelo de implementación clásica, el estado de los recursos y por qué los recursos se han implementado con uno u otro. El modelo de implementación de Resource Manager y el modelo de implementación clásica representan dos formas diferentes de implementar y administrar las soluciones de Azure. Usted trabaja con ellos a través de dos conjuntos de API distintos y los recursos implementados pueden contener diferencias importantes. Los dos modelos no son totalmente compatibles entre sí. En este tema se describen esas diferencias.

Para simplificar la implementación y administración de recursos, Microsoft recomienda que utilice Resource Manager para los nuevos recursos. Si es posible, Microsoft recomienda que vuelva a implementar los recursos existentes a través de Resource Manager.

Si no está familiarizado con Resource Manager, quizás quiera revisar primero la terminología definida en la [Información general de Azure Resource Manager](resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Historia de los modelos de implementación
Originalmente, Azure solo proporcionaba el modelo de implementación clásica. En este modelo, cada recurso existía de forma independiente; no había manera de agrupar los recursos relacionados. En su lugar, debía realizarse un seguimiento manual de los recursos que componían la solución o aplicación, y acordarse de administrarlos de manera coordinada. Para implementar una solución, tenía que crear cada recurso individualmente mediante el portal clásico o crear un script que implementara todos los recursos en el orden correcto. Para eliminar una solución, tenía que eliminar cada recurso individualmente. No se podía aplicar ni actualizar fácilmente las directivas de control de acceso para los recursos relacionados. Por último, no podía aplicar etiquetas a los recursos para etiquetarlos con términos que le ayudaran a supervisar los recursos y administrar la facturación.

En 2014, Azure presentó Resource Manager, que incorpora el concepto de un grupo de recursos. Un grupo de recursos es un contenedor para los recursos que comparten un ciclo de vida común. El modelo de implementación de Resource Manager ofrece varias ventajas:

* Puede implementar, administrar y supervisar todos los servicios para su solución como grupo, en lugar de controlar estos servicios individualmente.
* Puede implementar la solución repetidamente a lo largo del ciclo de vida y tener la seguridad de que los recursos se implementan de forma coherente.
* Puede aplicar control de acceso a todos los recursos del grupo de recursos y las directivas se aplican automáticamente cuando se agregan nuevos recursos al grupo de recursos.
* Puede aplicar etiquetas a los recursos para organizar de manera lógica todos los recursos de la suscripción.
* Puede usar la notación de objetos JavaScript (JSON) para definir la infraestructura de la solución. El archivo JSON se conoce como una plantilla de Resource Manager.
* Puede definir las dependencias entre recursos de modo que se implementen en el orden correcto.

Al agregarse el Administrador de recursos, todos los recursos se agregaron retroactivamente a los grupos de recursos predeterminados. Si ahora crea un recurso a través de la implementación clásica, el recurso se crea automáticamente dentro de un grupo de recursos predeterminado para el servicio, aunque no se especifique dicho grupo de recursos durante la implementación. Sin embargo, solo el hecho de existir dentro de un grupo de recursos no significa que el recurso se haya convertido al modelo del Administrador de recursos. En la sección siguiente veremos cómo cada servicio controla los dos modelos de implementación. 

## <a name="understand-support-for-the-models"></a>Descripción de la compatibilidad para los modelos
Al decidir qué modelo de implementación se usará para los recursos, hay tres escenarios a tener en cuenta:

1. El servicio admite Resource Manager y proporciona un solo tipo.
2. El servicio admite Resource Manager pero proporciona dos tipos: uno para Resource Manager y otro para el modelo clásico. Este escenario se aplica solo a las máquinas virtuales, las cuentas de almacenamiento y las redes virtuales.
3. El servicio no admite Resource Manager.

Para averiguar si un servicio admite Resource Manager, consulte [Proveedores compatibles de Resource Manager](resource-manager-supported-services.md).

Si el servicio que desea usar no admite Resource Manager, debe continuar usando la implementación clásica.

Si el servicio admite Resource Manager y **no es** una máquina virtual, cuenta de almacenamiento o red virtual, puede usar Resource Manager sin problemas.

En el caso de las máquinas virtuales, las cuentas de almacenamiento y las redes virtuales, si el recurso se creó mediante la implementación clásica, debe continuar trabajando en él mediante las operaciones clásicas. Si la máquina virtual, la cuenta de almacenamiento o la red virtual se crearon con la implementación de Resource Manager, debe continuar usando operaciones de Resource Manager. Esta distinción puede resultar confusa cuando la suscripción contiene una combinación de los recursos creados mediante Resource Manager y la implementación clásica. Esta combinación de recursos puede crear resultados inesperados porque los recursos no son compatibles con las mismas operaciones.

En algunos casos, un comando de Resource Manager puede recuperar información sobre un recurso creado mediante la implementación clásica, o puede realizar tareas administrativas tales como mover un recurso clásico a otro grupo de recursos. Sin embargo, estos casos no deben dar la impresión de que el tipo es compatible con las operaciones de Resource Manager. Por ejemplo, supongamos que tiene un grupo de recursos que contiene una máquina virtual creada con la implementación clásica. Si ejecuta el siguiente comando de PowerShell para Resource Manager:

```powershell
Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

Devuelve la máquina virtual:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

Sin embargo, el cmdlet **Get-AzureRmVM** de Resource Manager solo devuelve las máquinas virtuales implementadas mediante Resource Manager. El siguiente comando no devuelve la máquina virtual creada mediante la implementación clásica.

```powershell
Get-AzureRmVM -ResourceGroupName ExampleGroup
```

Solo los recursos creados a través del Administrador de recursos son compatibles con las etiquetas. No puede aplicar etiquetas a los recursos clásicos.

## <a name="resource-manager-characteristics"></a>Características del Administrador de recursos
Para ayudarle a comprender los dos modelos, revisemos las características de los tipos de Resource Manager:

* Creado mediante el [Portal de Azure](https://portal.azure.com/).
  
     ![Portal de Azure](./media/resource-manager-deployment-model/portal.png)
  
     Para los recursos de Proceso, Almacenamiento y redes, puede usar el Administrador de recursos o la implementación clásica. Select **Administrador de recursos**.
  
     ![Implementación de Resource Manager](./media/resource-manager-deployment-model/select-resource-manager.png)
* Creado con la versión para Resource Manager de los cmdlets de Azure PowerShell. Estos comandos tienen el formato *Verb-AzureRmNoun*.

  ```powershell
  New-AzureRmResourceGroupDeployment
  ```

* Creado mediante la [API de REST de Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) para las operaciones REST.
* Creado mediante los comandos de la CLI de Azure ejecutados en modo **arm** .
  
  ```azurecli
  azure config mode arm
  azure group deployment create
  ```

* El tipo de recurso no incluye **(clásico)** en el nombre. La imagen siguiente muestra el tipo como **cuenta de almacenamiento**.
  
    ![aplicación web](./media/resource-manager-deployment-model/resource-manager-type.png)

## <a name="classic-deployment-characteristics"></a>Características de implementación clásica
También puede conocer el modelo de implementación clásica como modelo de administración de servicios.

Los recursos creados en el modelo de implementación clásica comparten las siguientes características:

* Creado mediante el [Portal de Azure](https://manage.windowsazure.com)
  
     ![Portal de Azure](./media/resource-manager-deployment-model/classic-portal.png)
  
     O bien, el Portal de Azure y el usuario deben especificar la implementación **clásica** (para Proceso, Almacenamiento y Redes).
  
     ![Implementación clásica](./media/resource-manager-deployment-model/select-classic.png)
* Creado mediante la versión para Service Management de los cdmlets de Azure PowerShell. Estos nombres de comandos tienen el formato *Verb-AzureNoun*.

  ```powershell
  New-AzureVM
  ```

* Creado mediante la [API de REST de Service Management](https://msdn.microsoft.com/library/azure/ee460799.aspx) para las operaciones REST.
* Creado mediante los comandos de la CLI de Azure ejecutados en modo **asm** .

  ```azurecli
  azure config mode asm
  azure vm create
  ```
   
* El tipo de recurso incluye **(clásico)** en el nombre. La imagen siguiente muestra el tipo como **cuenta de almacenamiento (clásica)**.
  
    ![tipo clásico](./media/resource-manager-deployment-model/classic-type.png)

Puede usar Azure Portal para administrar los recursos creados a través de la implementación clásica.

## <a name="changes-for-compute-network-and-storage"></a>Cambios de proceso, red y almacenamiento
En el siguiente diagrama se muestran los recursos de proceso, red y almacenamiento implementados a través de Resource Manager.

![Arquitectura de Resource Manager](./media/resource-manager-deployment-model/arm_arch3.png)

Tenga en cuenta las siguientes relaciones entre los recursos:

* Todos los recursos existen dentro de un grupo de recursos.
* La máquina virtual depende de una cuenta de almacenamiento específica definida en el proveedor de recursos de almacenamiento para almacenar sus discos en Blob Storage (obligatorio).
* La máquina virtual hace referencia a una NIC específica definida en el proveedor de recursos de red (obligatorio) y un conjunto de disponibilidad definido en el proveedor de recursos de proceso (opcional).
* La NIC hace referencia a la dirección IP asignada a la máquina virtual (obligatoria), la subred de la red virtual para la máquina virtual (obligatoria) y a un grupo de seguridad de red (opcional).
* La subred dentro de una red virtual hace referencia a un grupo de seguridad de red (opcional).
* La instancia de equilibrador de carga hace referencia al grupo de backend de direcciones IP que incluye la NIC de una máquina virtual (opcional) y hace referencia a una dirección IP pública o privada del equilibrador de carga (opcional).

Aquí se encuentran los componentes y sus relaciones para la implementación clásica:

![arquitectura clásica](./media/resource-manager-deployment-model/arm_arch1.png)

La solución clásica para hospedar una máquina virtual incluye:

* Un servicio de nube requerido que actúa como contenedor para hospedar máquinas virtuales (cálculo). Las máquinas virtuales se proporcionan automáticamente con una tarjeta de interfaz de red (NIC) y una dirección IP asignada por Azure. Además, el servicio de nube contiene una instancia de equilibrador de carga externa, una dirección IP pública y extremos predeterminados para permitir un escritorio remoto y tráfico de PowerShell remoto para máquinas virtuales basadas en Windows y tráfico de Secure Shell (SSH) para máquinas virtuales basadas en Linux.
* Una cuenta de almacenamiento necesaria que almacena discos duros virtuales para una máquina virtual, incluido el sistema operativo, los discos de datos temporales y adicionales (almacenamiento).
* Una red virtual opcional que actúa como un contenedor adicional, en el que se puede crear una estructura de subredes y designar la subred en la que se encuentra la máquina virtual (red).

En la siguiente tabla se describen los cambios en la interacción de los proveedores de recursos de Proceso, Red y Almacenamiento:

| Elemento | Clásico | Resource Manager |
| --- | --- | --- |
| Servicio en la nube para máquinas virtuales |El servicio en la nube es un contenedor para las máquinas virtuales que exige la disponibilidad de la plataforma y el equilibrio de carga. |El servicio en la nube ya no es un objeto necesario para crear una máquina virtual usando el nuevo modelo. |
| Redes virtuales |Una red virtual es opcional para la máquina virtual. En caso de incluirse, la red virtual no puede implementarse con Resource Manager. |La máquina virtual requiere una red virtual que se ha implementado con Resource Manager. |
| Cuentas de almacenamiento |La máquina virtual requiere una cuenta de almacenamiento que almacena los discos duros virtuales para los discos de datos del sistema operativo, temporales y adicionales. |La máquina virtual requiere una cuenta de almacenamiento para almacenar sus discos en Blob Storage. |
| Conjuntos de disponibilidad |La disponibilidad en la plataforma se ha indicado mediante la configuración del mismo "AvailabilitySetName" en las máquinas virtuales. El número máximo de dominios con error era de 2. |Un conjunto de disponibilidad es un recurso expuesto por el proveedor de Microsoft.Compute. Las máquinas virtuales que requieren alta disponibilidad deben incluirse en el conjunto de disponibilidad. Ahora, el recuento máximo de dominios con error es de 3. |
| Grupos de afinidad |Los grupos de afinidad eran necesarios para crear redes virtuales. Sin embargo, con la introducción de las redes virtuales regionales, ya no era necesario. |Para simplificar, no existe el concepto de grupos de afinidad en las API expuestas a través del Administrador de recursos de Azure. |
| Equilibrio de carga |La creación de un servicio en la nube proporciona un equilibrador de carga implícito para las máquinas virtuales implementadas. |El equilibrador de carga es un recurso expuesto por el proveedor de Microsoft.Network. La interfaz de red principal de las máquinas virtuales cuya carga se debe equilibrar debe hacer referencia al equilibrador de carga. Los equilibradores de carga pueden ser internos o externos. Una instancia de equilibrador de carga hace referencia al grupo de backend de direcciones IP que incluye la NIC de una máquina virtual (opcional) y hace referencia a una dirección IP pública o privada del equilibrador de carga (opcional). [Más información.](../virtual-network/resource-groups-networking.md) |
| Dirección IP virtual |Cloud Services obtiene una VIP (dirección IP virtual) predeterminada cuando se agrega una máquina virtual a un servicio en la nube. La dirección IP virtual es la dirección asociada al equilibrador de carga implícito. |La dirección IP pública es un recurso expuesto por el proveedor de Microsoft.Network. La dirección IP pública puede ser estática (reservada) o dinámica. Las direcciones IP públicas dinámicas pueden asignarse a un equilibrador de carga. Las direcciones IP públicas se pueden proteger mediante grupos de seguridad. |
| Dirección IP reservada |Puede reservar una dirección IP en Azure y asociarlo a un servicio en la nube para asegurarse de que la dirección IP es permanente. |La dirección IP pública puede crearse en modo "Estático" y ofrece la misma capacidad que una "dirección IP reservada". Las direcciones IP públicas estáticas solo puede asignarse a un equilibrador de carga ahora. |
| Dirección IP pública (PIP) por máquina virtual |Las direcciones IP públicas también se pueden asociar directamente a una VM. |La dirección IP pública es un recurso expuesto por el proveedor de Microsoft.Network. La dirección IP pública puede ser estática (reservada) o dinámica. Sin embargo, solo se pueden asignar direcciones IP públicas dinámica a una interfaz de red para obtener una dirección IP pública por máquina virtual ahora. |
| Extremos |Es necesario configurar los extremos de entrada en una máquina virtual para abrir la conectividad para determinados puertos. Uno de los modos comunes de conectarse a las máquinas virtuales es mediante la configuración de extremos de entrada. |Las reglas de entrada NAT se puede configurar en equilibradores de carga para lograr la misma capacidad de habilitar los extremos en puertos específicos para conectarse a las máquinas virtuales. |
| Nombre DNS |Un servicio en la nube obtendría un nombre de DNS único global implícito. Por ejemplo: `mycoffeeshop.cloudapp.net`. |Los nombres DNS son parámetros opcionales que se pueden especificar en un recurso de dirección IP pública. El nombre de dominio completo tiene el siguiente formato: `<domainlabel>.<region>.cloudapp.azure.com`. |
| Interfaces de red |La interfaz de red principal y secundaria y sus propiedades se definieron como configuración de red de una máquina virtual. |La interfaz de red es un recurso expuesto por el proveedor de Microsoft.Network. El ciclo de vida de la interfaz de red no está asociado a una máquina virtual. Hace referencia a la dirección IP asignada a la máquina virtual (obligatoria), la subred de la red virtual para la máquina virtual (obligatoria) y a un grupo de seguridad de red (opcional). |

Para obtener información sobre cómo conectar redes virtuales de diferentes modelos de implementación, consulte [Conexión a redes virtuales a partir de diferentes modelos de implementación del portal](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migración de implementaciones clásicas a Resource Manager
Si está listo para migrar los recursos de la implementación clásica a la implementación de Resource Manager, consulte:

1. [Profundización técnica en la migración compatible con la plataforma de la implementación clásica a la de Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
2. [Migración compatible con la plataforma de recursos de IaaS del modelo clásico a Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
3. [Migración de recursos de IaaS de la implementación clásica a la de Resource Manager con Azure PowerShell](../virtual-machines/virtual-machines-windows-ps-migration-classic-resource-manager.md)
4. [Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure](../virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Preguntas frecuentes
**¿Puedo crear una máquina virtual mediante Azure Resource Manager para implementar en una red virtual creada mediante la implementación clásica?**

ya que no es compatible. No se puede usar Azure Resource Manager para implementar una máquina virtual en una red virtual que se creó con la implementación clásica.

**¿Puedo crear una máquina virtual mediante Azure Resource Manager desde una imagen de usuario que se creó utilizando las API de administración de servicios de Azure?**

ya que no es compatible. Sin embargo, puede copiar los archivos VHD de una cuenta de almacenamiento que se creó mediante las API de administración de servicios y agregarlos a una cuenta nueva creada mediante Azure Resource Manager.

**¿Cuál es el impacto en la cuota de mi suscripción?**

Las cuotas de las máquinas virtuales, redes virtuales y cuentas de almacenamiento creadas mediante Azure Resource Manager son independientes de otras cuotas. Cada suscripción obtiene cuotas para crear los recursos mediante las nuevas API. Puede leer más acerca de las cuotas adicionales [aquí](../azure-subscription-service-limits.md).

**¿Puedo continuar y usar mis scripts automatizados para aprovisionar máquinas virtuales, redes virtuales y cuentas de almacenamiento a través de las API de Resource Manager?**

Toda la automatización y los scripts que ha creado continúan funcionando para las máquinas virtuales existentes, las redes virtuales se crean en el modo de administración de servicios de Azure. Sin embargo, los scripts deben actualizarse para utilizar el nuevo esquema para crear los mismos recursos a través del modo de Resource Manager.

**¿Dónde puedo encontrar ejemplos de plantillas del Administrador de recursos de Azure?**

Puede encontrar un conjunto completo de plantillas de inicio en [Plantillas de inicio rápido del Administrador de recursos de Azure](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Pasos siguientes
* Para ver un tutorial sobre la creación de la plantilla que define una máquina virtual, una cuenta de almacenamiento y una red virtual, consulte [Tutorial de la plantilla de Resource Manager](resource-manager-template-walkthrough.md).
* Para ver los comandos para implementar una plantilla, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](resource-group-template-deploy.md).




<!--HONumber=Nov16_HO3-->


