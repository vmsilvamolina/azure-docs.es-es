---
title: "Errores comunes durante la migración del modelo clásico a Azure Resource Manager | Microsoft Docs"
description: "En este artículo se catalogan los errores y las soluciones más comunes durante la migración de recursos de IaaS de la administración de servicios de Azure a la pila de Azure Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: singhkays
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5bc03a1b-eb1c-438c-83d9-f0e9d61f1b6a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/13/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: daa311fcfd1ef06cf36e9443150fc967f0f39708
ms.openlocfilehash: 052505260c0998c8528146c4985400126d094f0d


---
# <a name="common-errors-during-classic-to-azure-resource-manager-migration"></a>Errores comunes durante la migración del modelo clásico a Azure Resource Manager
En este artículo se catalogan los errores y las soluciones más comunes durante la migración de recursos de IaaS del modelo de implementación clásica a la pila de Azure Resource Manager.

## <a name="list-of-errors"></a>Lista de errores
| Cadena de error | Mitigación |
| --- | --- |
| Error interno del servidor |En algunos casos, se trata de un error transitorio que desaparece con un reintento. Si continúa, [póngase en contacto con el soporte técnico de Azure](../azure-supportability/how-to-create-azure-support-request.md) ya que será necesario investigar los registros de la plataforma. <br><br> **NOTA:** Cuando la incidencia ya esté en manos del equipo de soporte técnico, no intente ninguna migración por su cuenta ya que podría tener consecuencias imprevistas sobre su entorno. |
| No se admite la migración para la implementación {nombre de implementación} en HostedService {nombre del servicio hospedado} porque es una implementación PaaS (web/trabajo). |Esto sucede cuando una implementación contiene un rol web o de trabajo. Dado que solo se admite la migración de máquinas virtuales, quite el rol web o de trabajo de la implementación y vuelva a intentar la migración. |
| Error de implementación de plantilla {nombre de plantilla}. CorrelationId={guid} |En el back-end del servicio de migración, usamos plantillas de Azure Resource Manager para crear recursos en la pila de Azure Resource Manager. Puesto que las plantillas son idempotentes, lo normal es que pueda reintentar la operación de migración para solucionar este error. Si este error continúa, [póngase en contacto con el soporte técnico de Azure](../azure-supportability/how-to-create-azure-support-request.md) y proporcióneles el CorrelationId. <br><br> **NOTA:** Cuando la incidencia ya esté en manos del equipo de soporte técnico, no intente ninguna migración por su cuenta ya que podría tener consecuencias imprevistas sobre su entorno. |
| La red virtual {nombre de la red virtual} no existe. |Esto puede ocurrir si ha creado la red virtual en el nuevo portal de Azure. El nombre de la red virtual real sigue el patrón "Grupo * <VNET name>". |
| La máquina virtual {nombre de la máquina virtual} de HostedService {nombre del servicio hospedado} contiene la extensión {nombre de la extensión} que no se admite en Azure Resource Manager. Se recomienda desinstalarla de la máquina virtual antes de continuar con la migración. |Las extensiones XML como BGInfo 1.* no se admiten en Azure Resource Manager. Por lo tanto, no se pueden migrar estas extensiones. Si estas extensiones se dejan instaladas en la máquina virtual, se desinstalan automáticamente antes de completar la migración. |
| La máquina virtual {nombre de la máquina virtual} de HostedService {nombre del servicio hospedado} contiene la extensión VMSnapshot/VMSnapshotLinux que actualmente no se admite para migración. Desinstálela de la máquina virtual y vuelva a agregarla mediante Azure Resource Manager después de completar la migración |Este es el escenario donde la máquina virtual está configurada para Azure Backup. Puesto que este no es actualmente un escenario admitido, siga la solución alternativa en https://aka.ms/vmbackupmigration. |
| La máquina virtual {nombre de la máquina virtual} en HostedService {nombre del servicio hospedado} contiene la extensión {nombre de la extensión} cuyo estado no está notificando la máquina virtual. Por lo tanto, esta máquina virtual no se puede migrar. Asegúrese de que se notifica el estado de la extensión o desinstale la extensión de la máquina virtual y vuelva a intentar la migración. <br><br> La máquina virtual {nombre de la máquina virtual} en HostedService {nombre del servicio hospedado} contiene la extensión {nombre de la extensión} que notifica el estado del controlador: {estado del controlador}. Por lo tanto, la VM no se puede migra. Asegúrese de que el estado del controlador de la extensión que se notifica sea {estado del controlador} o desinstálelo de la máquina virtual y vuelva a intentar la migración. <br><br> El agente de máquina virtual de la máquina virtual {nombre de la máquina virtual} en HostedService {nombre del servicio hospedado} está notificando el estado general del agente como no preparado. Por lo tanto si tiene una extensión que es migrable, la máquina virtual no se puede migrar. Asegúrese de que el agente de máquina virtual está notificando el estado general del agente como listo. Consulte https://aka.ms/classiciaasmigrationfaqs. |Las extensiones del agente invitado de Azure y de la máquina virtual necesitan acceso de salida a Internet a la cuenta de almacenamiento de la máquina virtual para rellenar su estado. Entre las causas comunes del error de estado se incluyen: <li> Un grupo de seguridad de red que bloquea el acceso de salida a Internet. <li> Si la red virtual tiene servidores DNS locales y se pierde la conectividad con DNS. <br><br> Si continúa viendo un estado no admitido, puede desinstalar las extensiones para omitir esta comprobación y seguir adelante con la migración. |
| No se admite la migración para la implementación {nombre de implementación} en HostedService {nombre del servicio hospedado} porque tiene varios conjuntos de disponibilidad. |Actualmente, solo se pueden migrar los servicios hospedados que tengan como mucho un conjunto de disponibilidad. Para solucionar este problema, mueva los conjuntos de disponibilidad adicionales y las máquinas virtuales de esos conjuntos de disponibilidad a otro servicio hospedado. |
| No se admite la migración para la implementación {nombre de implementación} en HostedService {nombre del servicio hospedado} porque tiene máquinas virtuales que no forman parte del conjunto de disponibilidad aunque HostedService contiene uno. |En este escenario la solución consiste en mover todas las máquinas virtuales a un único conjunto de disponibilidad o quitar todas las máquinas virtuales del conjunto de disponibilidad en el servicio hospedado. |
| La cuenta de almacenamiento/HostedService/red virtual {nombre de la red virtual} está en proceso de ser migrada y por tanto no se puede cambiar. |Este error se produce cuando la operación para preparar la migración se ha completado en el recurso y se desencadena una operación que haría un cambio en él. Debido al bloqueo en el plano de administración después de la operación de preparación, todos los cambios en el recurso se bloquean. Para desbloquear el plano de administración, puede ejecutar la operación de confirmación de la migración para completar la migración o la operación de anulación de la migración para revertir a la operación de preparación. |
| No se permite la migración para HostedService {nombre del servicio hospedado} porque tiene una máquina virtual {nombre de la máquina virtual} en estado: RoleStateUnknown. La migración solo se permite cuando la máquina virtual tiene uno de los siguientes estados: En ejecución, Detenido, Detenido (desasignado). |La máquina virtual podría estar experimentando una transición de estado que normalmente sucede durante una operación de actualización en HostedService, como un reinicio, la instalación de una extensión, etc. Se recomienda completar la operación de actualización en HostedService antes de intentar la migración. |
| La implementación {nombre-implementación} en HostedService {nombre-servicio-hospedado} contiene una máquina virtual {nombre-máquina-virtual} con el disco de datos {nombre-disco-datos} cuyos bytes de tamaño de blob físico {tamaño-blob-vhd-que-realiza-copia-seguridad-disco-datos} no coinciden con los del tamaño lógico del disco de datos de la máquina virtual {tamaño-disco-datos-especificado-en-api-máquina-virtual}. La migración continuará sin especificar un tamaño para el disco de datos de la máquina virtual de Azure Resource Manager. Si desea corregir el tamaño del disco de datos antes de continuar con la migración, visite https://aka.ms/vmdiskresize. | Este error se produce si ha cambiado el tamaño del blob de disco duro virtual sin actualizar el tamaño del modelo de la API de la máquina virtual. [A continuación](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes), se describen los pasos de mitigación detallados.|

## <a name="detailed-mitigations"></a>Mitigaciones detalladas

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>Máquina virtual con disco de datos cuyos bytes de tamaño de blob físico no coinciden con los del tamaño lógico del disco de datos de la máquina virtual.

Esto se produce cuando el tamaño lógico del disco de datos pueda desincronizarse con el tamaño real del blob de disco duro virtual. Esto se puede comprobar fácilmente utilizando los comandos siguientes:

#### <a name="verifying-the-issue"></a>Comprobación del problema

```PowerShell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzureStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>Mitigación del problema

```PowerShell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

## <a name="next-steps"></a>Pasos siguientes
En esta lista de artículos sobre migración se explica el proceso.

* [Migración compatible con la plataforma de recursos de IaaS del modelo clásico al de Azure Resource Manager](virtual-machines-windows-migration-classic-resource-manager.md)
* [Migración de recursos de IaaS de la implementación clásica a Resource Manager con Azure PowerShell](virtual-machines-windows-ps-migration-classic-resource-manager.md)
* [Migración de recursos de IaaS de la implementación clásica a Azure Resource Manager con la CLI de Azure](virtual-machines-linux-cli-migration-classic-resource-manager.md)




<!--HONumber=Feb17_HO3-->


