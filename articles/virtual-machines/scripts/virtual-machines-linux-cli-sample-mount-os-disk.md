---
title: 'Ejemplo de script de la CLI de Azure: Montaje del disco de sistema operativo | Microsoft Docs'
description: 'Ejemplo de script de la CLI de Azure: Montaje del disco de sistema operativo'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/27/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e5362b32a0f93433c77c27a6fc116960c45404f9
ms.openlocfilehash: 73bd1f9b018e49ad8a536b80275cbf0faa030b74
ms.lasthandoff: 03/01/2017

---

# <a name="troubleshoot-a-vms-operating-system-disk"></a>Solución de problemas de un disco de sistema operativo de máquina virtual

Este script monta el disco del sistema operativo de una máquina virtual problemática o que ha generado un error, como un disco de datos en una segunda máquina virtual. Esto puede ser útil para solucionar problemas de disco o de recuperación de datos. 

Antes de ejecutar este script, asegúrese de que se haya creado una conexión con Azure mediante el comando `az login`. Además, se necesitará una máquina virtual existente. Actualice el nombre y el grupo de recursos de la máquina virtual existente en el ejemplo de script.

Este ejemplo funciona en un shell de Bash. Para ver las opciones de ejecución de scripts de la CLI de Azure en un cliente Windows, consulte [Using the Azure CLI on Windows](../virtual-machines-windows-cli-options.md) (Uso de la CLI de Azure en Windows).

## <a name="sample-script"></a>Script de ejemplo

[!code-azurecli[main](../../../cli_scripts/virtual-machine/mount-os-disk/mount-os-disk.sh "Creación rápida de una máquina virtual")]

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una máquina virtual y todos los recursos relacionados. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [az vm show](https://docs.microsoft.com/cli/azure/vm#show) | Devuelve una lista de máquinas virtuales. En este caso, la opción de consulta se utiliza para devolver el disco de sistema operativo de la máquina virtual. Este valor se agrega, posteriormente, a un nombre de variable "uri". |
| [az vm delete](https://docs.microsoft.com/cli/azure/vm#delete) | Elimina una máquina virtual. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Crea una máquina virtual.  |
| [az vm disk attach](https://docs.microsoft.com/cli/azure/vm/disk#attach) | Conecta un disco a una máquina virtual. |
| [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm#list-ip-addresses) | Devuelve las direcciones IP de una máquina virtual. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](https://docs.microsoft.com/cli/azure/overview).

Encontrará más ejemplos de scripts de la CLI de máquina virtual en la [documentación sobre máquinas virtuales Linux de Azure](../virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

