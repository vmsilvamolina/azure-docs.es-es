---
title: "Creación de una instancia de IoT Hub mediante la CLI de Azure (az-py) | Microsoft Docs"
description: "Creación de una instancia de Azure IoT Hub mediante la CLI de Azure 2.0 (az.py) multiplataforma."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 5ea7095e12b6194556d3cd0baa43ccfed1e087ee
ms.openlocfilehash: 4f512601cebcfce7bfac47ed440c96fcb2c91b5f
ms.lasthandoff: 02/27/2017


---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Creación de una instancia de IoT Hub mediante la CLI de Azure 2.0

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Introducción

Puede usar la CLI de Azure 2.0 (az.py) para crear y administrar las instancias de Azure IoT Hub mediante programación. En este artículo se muestra cómo utilizar la CLI de Azure 2.0 (az.py) para crear una instancia de IoT Hub.

Puede completar la tarea mediante una de las siguientes versiones de la CLI:

* [CLI de Azure (azure.js)](iot-hub-create-using-cli-nodejs.md). La CLI de los modelos de implementación clásico y de administración de recursos.
* CLI de Azure 2.0 (az.py): la CLI de última generación para el modelo de implementación con Resource Manager como se describe en este artículo.

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* [CLI de Azure 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Inicio de sesión y configuración de la cuenta de Azure

Inicie sesión en su cuenta de Azure y configure la CLI de Azure para trabajar con recursos de IoT Hub.

1. En el símbolo del sistema, ejecute el [comando de inicio de sesión][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Siga las instrucciones para realizar la autenticación mediante el código e inicie sesión en la cuenta de Azure a través de un explorador web.

2. Si tiene varias suscripciones de Azure, iniciar sesión en Azure le concede acceso a todas las cuentas de Azure asociadas con las credenciales. Use el siguiente [comando para mostrar las cuentas de Azure][lnk-az-account-command] que tiene disponibles para su uso:
    
    ```azurecli
    az account list 
    ```

    Use el siguiente comando para seleccionar la suscripción que desea usar para ejecutar los comandos que crearán la instancia de IoT Hub. Puede usar el nombre de la suscripción o el identificador de la salida del comando anterior:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Instale el _componente iot_ de la CLI de Azure. Ejecute el comando [siguiente para agregar el componente de IoT][lnk-az-addcomponent-command]:
    
    ```azurecli
    az component update --add iot
    ```

4. Registre el proveedor de IoT para poder implementar los recursos de IoT. Ejecute el comando [siguiente para registrar el proveedor de IoT][lnk-az-register-command]:
    
    ```azurecli
    az provider register -namespace Microsoft.Devices
    ```

## <a name="create-an-iot-hub"></a>Creación de un IoT Hub

Use la CLI de Azure para crear un grupo de recursos y, luego, agregue una instancia de IoT Hub.

1. Cuando crea una instancia de IoT Hub, debe crearla en un grupo de recursos. Use un grupo de recursos existente o ejecute el comando [siguiente para crear un grupo de recursos][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > El ejemplo anterior crea el grupo de recursos en la ubicación del oeste de EE. UU. Puede ejecutar el comando `az account list-locations -o table` para ver una lista de las ubicaciones disponibles.
    >
    >

2. Ejecute el comando [siguiente para crear una instancia de IoT Hub][lnk-az-iot-command] en el grupo de recursos:
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

> [!NOTE]
> El nombre de su instancia de IoT Hub debe única globalmente. El comando anterior crea una instancia de IoT Hub en el plan de tarifa S1 según el que factura. Para más información, consulte el artículo sobre los [precios de Azure IoT Hub][lnk-iot-pricing].
>
>

## <a name="remove-an-iot-hub"></a>Eliminación de una instancia de IoT Hub

Puede usar la CLI de Azure para [eliminar un recurso individual][lnk-az-resource-command], como una instancia de IoT Hub, o para eliminar un grupo de recursos y todos los recursos, incluida cualquier instancia de IoT Hub.

Ejecute el comando siguiente para eliminar una instancia de IoT Hub:

```azurecli
az resource delete --name {your iot hub name} --resource-group {your resource group name} --resource-type Microsoft.Devices/IotHubs
```

Ejecute el comando siguiente para eliminar un grupo de recursos y todos sus recursos:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo desarrollar para IoT Hub, consulte los siguientes artículos:

* [Guía para desarrolladores de IoT Hub][lnk-devguide]

Para explorar aún más las funcionalidades de Centro de IoT, consulte:

* [Uso de Azure Portal para administrar IoT Hub][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 

