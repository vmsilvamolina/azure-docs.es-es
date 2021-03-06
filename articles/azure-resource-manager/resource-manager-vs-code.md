---
title: Uso de VS Code con plantillas de Resource Manager | Microsoft Docs
description: "Muestra cómo configurar Visual Studio Code para crear plantillas Azure Resource Manager."
services: azure-resource-manager
documentationcenter: na
author: cmatskas
manager: timlt
editor: tysonn
ms.assetid: 78f2aa22-df1d-41bd-92ec-dabd1175db88
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/03/2017
ms.author: chmatsk;tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 10c7051c9b1218081d95cb10403006bfd95126ba
ms.openlocfilehash: 2ac1c2cce7a9e045990894b0bbaa045df3d48954


---
# <a name="working-with-azure-resource-manager-templates-in-visual-studio-code"></a>Trabajo con plantillas de Azure Resource Manager en Visual Studio Code
Las plantillas de Azure Resource Manager son archivos JSON que describen un recurso y dependencias relacionadas. A veces, estos archivos pueden ser grandes y complicados, por lo que es importante la compatibilidad con las herramientas. Visual Studio Code es un editor de código nuevo, ligero, de código abierto y multiplataforma. Permite crear y editar plantillas de Resource Manager mediante una [nueva extensión](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools). VS Code se ejecuta en todas partes y no requiere acceso a Internet cuando desea implementar las plantillas de Resource Manager en la suscripción de Azure.

Si todavía no dispone de VS Code, puede instalarlo en [https://code.visualstudio.com/](https://code.visualstudio.com/).

## <a name="install-the-resource-manager-extension"></a>Instalación de la extensión de Resource Manager
Para trabajar con las plantillas JSON en VS Code, debe instalar una extensión. En los pasos siguientes se descarga e instala la compatibilidad de idioma para las plantillas JSON de Resource Manager:

1. Ejecutar VS Code 
2. Abrir Apertura rápida (Ctrl+P) 
3. Ejecute el siguiente comando: 
   
        ext install msazurermtools.azurerm-vscode-tools
4. Reinicie VS Code cuando se le solicite para habilitar la extensión. 
   
   ¡Terminado!

## <a name="set-up-resource-manager-snippets"></a>Configuración de los fragmentos de código de Resource Manager
En los pasos anteriores se instala la compatibilidad con las herramientas, pero ahora debemos configurar VS Code para utilizar fragmentos de plantilla JSON.

1. Copie el contenido del archivo del repositorio [azure-xplat-arm-tooling](https://raw.githubusercontent.com/Azure/azure-xplat-arm-tooling/master/VSCode/armsnippets.json) en el Portapapeles.
2. Ejecutar VS Code 
3. En VS Code, puede abrir el archivo de fragmentos de código JSON navegando a **Archivo** -> **Preferencias** -> **User Snippets (Fragmentos de código del usuario)** -> **JSON**. O bien, seleccione **F1**y escriba **preferencias** hasta que pueda seleccionar **Preferencias: Fragmentos de código**.
   
    ![fragmentos de código de preferencia](./media/resource-manager-vs-code/preferences-snippets.png)
   
    En las opciones, seleccione **JSON**.
   
    ![seleccionar json](./media/resource-manager-vs-code/select-json.png)
4. Pegue el contenido del archivo en el paso 1 en el archivo de fragmentos de código de usuario antes de la última "}". 
5. Asegúrese de que JSON parezca correcto y no haya ningún subrayado ondulado en ningún sitio. 
6. Guarde y cierre el archivo de fragmentos de código de usuario.

Esto es todo lo que se necesita para empezar a usar los fragmentos de código de Resource Manager. A continuación, probaremos esta configuración.

## <a name="work-with-template-in-vs-code"></a>Trabajo con la plantilla en VS Code
La manera más fácil de empezar a trabajar con una plantilla es tomar una de las plantillas de Inicio rápido, disponible en [Github](https://github.com/Azure/azure-quickstart-templates) , o bien utilizar una propia. Puede [exportar fácilmente una plantilla](resource-manager-export-template.md) de cualquiera de los grupos de recursos a través del portal. 

1. Si exporta una plantilla desde un grupo de recursos, abra los archivos extraídos en VS Code.
   
    ![mostrar archivos](./media/resource-manager-vs-code/show-files.png)
2. Abra el archivo template.json para que pueda modificarlo y agregar algunos recursos adicionales. Después de `"resources": [`, presione ENTRAR para iniciar una nueva línea. Si escribe **arm**, verá una lista de opciones. Estas opciones son los fragmentos de código de la plantilla que ha instalado. 
   
    ![mostrar fragmentos de código](./media/resource-manager-vs-code/type-snippets.png)
3. Seleccione el fragmento de código que desee. En este artículo, he elegido **arm ip** para crear una nueva dirección IP pública. Incluya una coma después del corchete de cierre `}` del recurso recién creado para asegurarse de que la sintaxis de la plantilla es válida.
   
     ![agregar coma](./media/resource-manager-vs-code/add-comma.png)
4. VS Code tiene IntelliSense integrado. A medida que modifica las plantillas, VS Code sugiere los valores disponibles. Por ejemplo, para agregar una sección de variables a la plantilla, agregue `""` (dos comillas dobles) y seleccione **Ctrl+Barra espaciadora** entre las comillas. Se le presentarán opciones, como **variables**.
   
    ![agregar variables](./media/resource-manager-vs-code/add-variables.png)
5. IntelliSense también puede sugerir valores o funciones disponibles. Para establecer una propiedad en un valor de parámetro, cree una expresión con `"[]"` y **Ctrl+Barra espaciadora**. Puede empezar a escribir el nombre de una función. Seleccione **Tabulador** cuando haya encontrado la función que desee.
   
    ![agregar parámetro](./media/resource-manager-vs-code/select-parameters.png)
6. Seleccione de nuevo **CTRL+BARRA ESPACIADORA** dentro de la función para ver una lista de los parámetros disponibles dentro de la plantilla.
   
    ![agregar parámetro](./media/resource-manager-vs-code/select-avail-parameters.png)
7. Si tiene cualquier problema de validación de esquema en la plantilla, verá los conocidos subrayados ondulados del editor. Puede ver la lista de errores y advertencias escribiendo **Ctrl + Mayús + M** o seleccionando los glifos en la barra de estado en la parte inferior izquierda.
   
    ![errors](./media/resource-manager-vs-code/errors.png)
   
    La validación de la plantilla puede ayudar a detectar problemas de sintaxis; sin embargo, también puede ver los errores que puede ignorar. En algunos casos, el editor está comparando la plantilla con un esquema que no está actualizado y, por tanto, notifica un error aunque sepa que es correcta. Por ejemplo, suponga que una función se ha agregado recientemente a Resource Manager, pero el esquema no se ha actualizado. El editor notifica un error a pesar de que la función funciona correctamente durante la implementación.
   
    ![mensaje de error](./media/resource-manager-vs-code/unrecognized-function.png)

## <a name="deploy-your-new-resources"></a>Implementación de los nuevos recursos
Cuando la plantilla está lista, puede implementar los nuevos recursos con las instrucciones siguientes: 

### <a name="windows"></a>Windows
1. Abrir un símbolo del sistema de PowerShell 
2. Para iniciar sesión, escriba: 
   
  ```powershell
  Login-AzureRmAccount
  ```

3. Si tiene varias suscripciones, obtenga una lista de ellas con:

  ```powershell 
  Get-AzureRmSubscription
  ```
   
    Y seleccione la que desea usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionId <Subscription Id>
  ```

4. Actualizar los parámetros en el archivo parameters.json
5. Ejecutar deploy.ps1 para implementar la plantilla en Azure

### <a name="osxlinux"></a>OSX y Linux
1. Abrir una ventana del terminal 
2. Para iniciar sesión, escriba:

  ```azurecli
  azure login
  ```

3. Si tiene varias suscripciones, seleccione la correcta con:

  ```azurecli
  azure account set <subscriptionNameOrId> 
  ```

4. Actualice los parámetros en el archivo parameters.json.
5. Para implementar la plantilla, ejecute:

  ```azurecli 
  azure group deployment create -f <PathToTemplate>
  ``` 

## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre las plantillas, consulte [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para más información acerca de las funciones de plantilla, consulte [Funciones de plantilla de Azure Resource Manager](resource-group-template-functions.md).
* Para obtener más ejemplos de cómo trabajar con Visual Studio Code, consulte [Build cloud apps with Visual Studio Code](https://github.com/Microsoft/HealthClinic.biz/wiki/Build-cloud-apps-with-Visual-Studio-Code) (Compilación de aplicaciones en la nube con Visual Studio Code) en la [demostración](https://blogs.msdn.microsoft.com/visualstudio/2015/12/08/connectdemos-2015-healthclinic-biz/) [HealthClinic.biz](https://github.com/Microsoft/HealthClinic.biz) realizada en Connect 2015. Para ver más guías rápidas de la demostración de HealthClinic.biz, consulte las [guías rápidas de las herramientas de desarrollador de Azure](https://github.com/Microsoft/HealthClinic.biz/wiki/Azure-Developer-Tools-Quickstarts).




<!--HONumber=Jan17_HO1-->


