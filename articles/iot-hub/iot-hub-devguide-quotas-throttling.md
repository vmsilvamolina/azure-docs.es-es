---
title: "Información de la limitación y las cuotas de IoT de Azure | Microsoft Docs"
description: "Guía del desarrollador: descripción de las cuotas que se aplican a IoT Hub y comportamiento esperado de limitación"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: ebfafd5ee9049b5049070ad111c95746b89e755f


---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referencia: Cuotas y limitación de IoT Hub

## <a name="quotas-and-throttling"></a>Cuotas y limitación
Cada suscripción de Azure puede tener como máximo 10 centros de IoT y al menos un centro gratuito.

Cada centro de IoT se aprovisiona con un determinado número de unidades en una SKU específica (para más información, consulte [Precios de Azure IoT Hub][lnk-pricing]). El valor de SKU y el número de unidades determinan la cuota diaria máxima de mensajes que puede enviar.

El valor de SKU también determina los valores de limitación que aplica el Centro de IoT a las operaciones.

## <a name="operation-throttles"></a>Limitaciones de operación
Las limitaciones de operación son las limitaciones de velocidad que se aplican en los intervalos de minutos y están diseñadas para evitar abusos. El Centro de IoT intenta evitar los errores de devolución siempre que sea posible, pero inicia la excepción de devolución si se infringe la limitación durante demasiado tiempo.

A continuación se muestra la lista de las limitaciones aplicadas. Los valores hacen referencia a un centro individual.

| Limitación | Gratuitos y centros S1 | Centros S2 | Centros S3 | 
| -------- | ------- | ------- | ------- |
| Operaciones de registro de identidad (crear, recuperar, enumerar, actualizar y eliminar) | 100/min/unidad | 100/min/unidad | 5000/min/unidad |
| Conexiones de dispositivos | Máximo de 100/s o 12/s/unidad <br/> Por ejemplo, dos unidades S1 equivalen a 2\*12 = 24/s, pero tendrá al menos 100/s en todas las unidades. Con nueve unidades S1 tiene 108/s (9\*12) en las unidades. | 120/s/unidad | 6000/s/unidad |
| Envíos de dispositivo a nube | Máximo de 100/s o 12/s/unidad <br/> Por ejemplo, dos unidades S1 equivalen a 2\*12 = 24/s, pero tendrá al menos 100/s en todas las unidades. Con nueve unidades S1 tiene 108/s (9\*12) en las unidades. | 120/s/unidad | 6000/s/unidad |
| Envíos de nube a dispositivo | 100/min/unidad | 100/min/unidad | 5000/min/unidad |
| Recepciones de nube a dispositivo <br/> (solo cuando el dispositivo usa HTTP)| 1000/min/unidad | 1000/min/unidad| 50000/min/unidad |
| Carga de archivos | 100 notificaciones de carga de archivos/min/unidad | 100 notificaciones de carga de archivos/min/unidad | 5000 notificaciones de carga de archivos/min/unidad |
| Métodos directos | 10/s/unidad | 30/s/unidad | 1500/s/unidad | 
| Lecturas de dispositivos gemelos | 10/s | Máximo de 10/s o 1/s/unidad | 50/s/unidad |
| Actualizaciones de dispositivos gemelos | 10/s | Máximo de 10/s o 1/s/unidad | 50/s/unidad |
| Operaciones de trabajos <br/> (crear, actualizar, enumerar, eliminar) | 100/min/unidad | 100/min/unidad | 5000/min/unidad |
| Resultado de operaciones por dispositivo de trabajos | 10/s | Máximo de 10/s o 1/s/unidad | 50/s/unidad |

Es importante aclarar que la limitación de las *conexiones de dispositivo* determina la velocidad a la que se pueden establecer nuevas conexiones de dispositivo con un Centro de IoT, no el número máximo de dispositivos conectados a la vez. La limitación depende del número de unidades aprovisionadas para el centro de IoT.

Por ejemplo, si compra una sola unidad S1, tendrá una limitación de 100 conexiones por segundo. Por lo tanto, para conectar 100 000 dispositivos, se tarda al menos 1000 segundos (aproximadamente 16 minutos). Sin embargo, puede tener el mismo número de dispositivos conectados al mismo tiempo que de dispositivos registrados en el registro de identidad.

Consulte la entrada de blog [IoT Hub throttling and you][lnk-throttle-blog] (Limitación de IoT Hub) para ver una explicación detallada del comportamiento de limitación de IoT Hub.

> [!NOTE]
> En cualquier momento, es posible aumentar las cuotas o las limitaciones si aumenta el número de unidades aprovisionadas en un Centro de IoT.
> 
> [!IMPORTANT]
> Las operaciones de registro de identidad están diseñadas para usarse en tiempo de ejecución en escenarios de administración y aprovisionamiento de dispositivos. La lectura o actualización de un gran número de identidades de dispositivo se realiza mediante [trabajos de importación y exportación][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Otros límites

IoT Hub exige otros límites en sus distintas funcionalidades.

| Operación | Límite |
| --------- | ----- |
| URI de carga de archivos | 10000 URI de SAS pueden estar fuera para una cuenta de almacenamiento al mismo tiempo. <br/> &10; URI/dispositivo de SAS puede estar fuera al mismo tiempo. |
| Trabajos | El historial de trabajos se conserva durante 30 días como máximo. <br/> El número máximo de trabajos simultáneos es: 1 para gratuitos y S1, 5 para S2 y 10 para S3. |
| Puntos de conexión adicionales | Los centros de SKU de pago pueden tener 10 puntos de conexión adicionales. Los centros de SKU gratis pueden tener un punto de conexión adicional. |
| Reglas de enrutamiento de mensajes | Los centros de SKU de pago pueden tener 100 reglas de enrutamiento. Los centros de SKU gratis pueden tener cinco reglas de enrutamiento. |

## <a name="next-steps"></a>Pasos siguientes
Otros temas de referencia en la Guía del desarrollador de IoT Hub son:

* [IoT Hub endpoints][lnk-devguide-endpoints] (Puntos de conexión de IoT Hub)
* [Lenguaje de consulta de IoT Hub para dispositivos gemelos y trabajos][lnk-devguide-query]
* [Compatibilidad con MQTT de IoT Hub][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Jan17_HO5-->


