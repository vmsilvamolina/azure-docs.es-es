---
title: 'Paso 6: Acceso al servicio web Machine Learning | Microsoft Docs'
description: "Paso 6 del tutorial Desarrollo de una solución predictiva: acceso a un servicio web activo de Azure Machine Learning."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 6a65c89a-40ab-4673-8dd8-8eee0a150e3b
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/16/2016
ms.author: garye
translationtype: Human Translation
ms.sourcegitcommit: 1029c7e4c6a17ad2a290ff0783fc88692555b255
ms.openlocfilehash: 89bec0e3af42b03ef21e3e8c4059f6aad86db4c0
ms.lasthandoff: 03/02/2017


---
# <a name="walkthrough-step-6-access-the-azure-machine-learning-web-service"></a>Paso 6 del tutorial: Acceso al servicio web de Aprendizaje automático de Azure

Este es el último paso del tutorial [Desarrollo de una solución de análisis predictiva para la evaluación del riesgo de crédito en Aprendizaje automático de Azure](machine-learning-walkthrough-develop-predictive-solution.md)

1. [Creación de un área de trabajo de Aprendizaje automático](machine-learning-walkthrough-1-create-ml-workspace.md)
2. [Carga de los datos existentes](machine-learning-walkthrough-2-upload-data.md)
3. [Crear un experimento nuevo](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Entrenamiento y evaluación de los modelos](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Implementación del servicio web](machine-learning-walkthrough-5-publish-web-service.md)
6. **Acceso al servicio web**

- - -
En el paso anterior de este tutorial hemos implementado un servicio web que usa nuestro modelo de predicción del riesgo de crédito. Ahora los usuarios pueden enviar datos al servicio y recibir resultados. 

El servicio web es un servicio web de Azure que puede recibir y devolver datos con las API de REST de una de estas dos maneras:  

* **Solicitud/respuesta** : el usuario envía una o varias filas de datos de crédito al servicio mediante un protocolo HTTP, y el servicio responde con uno o más conjuntos de resultados.
* **Ejecución de lotes** : el usuario almacena una o varias filas de datos de crédito en un blob de Azure y luego envía la ubicación del blob al servicio. El servicio puntúa todas las filas de datos en el blob de entrada, almacena los resultados en otro blog y devuelve la dirección URL del contenedor.  

La forma más fácil y rápida de acceder al servicio web clásico es a través de la [aplicación web del servicio de solicitud-respuesta de Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/) o la [plantilla de aplicación web del servicio de ejecución de lotes de Azure ML](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/).

Estas plantillas de aplicación web pueden compilar una aplicación web personalizada que reconoce los datos de entrada del servicio web y lo que devolverá. Todo lo que necesita hacer es conceder acceso al servicio web y a los datos, y la plantilla se encarga del resto.

Para obtener más información sobre el uso de plantillas de aplicación web, consulte [Consumo de un servicio web de Azure Machine Learning con una plantilla de aplicación web](machine-learning-consume-web-service-with-web-app-template.md).

También puede desarrollar una aplicación personalizada para acceder al servicio web con código de inicio proporcionado en los lenguajes de programación R, C# y Python.

Puede encontrar todos los detalles en [Cómo consumir un servicio web de Azure Machine Learning publicado a partir de un experimento de Machine Learning](machine-learning-consume-web-services.md).


