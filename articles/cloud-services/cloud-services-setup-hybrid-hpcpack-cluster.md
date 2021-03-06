---
title: "Configurar un clúster HPC híbrido con Microsoft HPC Pack | Microsoft Docs"
description: "Obtenga información acerca de cómo usar Microsoft HPC Pack y Azure para configurar un pequeño clúster híbrido de informática de alto rendimiento (HPC)"
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: b11f3e1b-b9e1-4b0d-8455-6607b88814e9
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: a0e80acad4e8db2177e699f90f7296c64b38e718
ms.openlocfilehash: da7102882b9dcf7db1285c0ec004ce1ad3859853


---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Configuración de un clúster híbrido de informática de alto rendimiento (HPC) con nodos de proceso de Azure a petición y Microsoft HPC Pack
Use Microsoft HPC Pack 2012 R2 y Azure para configurar un pequeño clúster híbrido de informática de alto rendimiento (HPC). El clúster constará de un nodo principal local (un equipo con el sistema operativo Windows Server y HPC Pack) y otros nodos de ejecución que implemente localmente como instancias de rol de trabajo en un servicio en la nube de Azure. A continuación, podrá ejecutar trabajos informáticos en el clúster híbrido.

![Clúster híbrido de HPC][Overview] 

Este tutorial muestra un enfoque, en ocasiones denominado clúster "ráfaga en la nube", para usar recursos informáticos escalables y bajo demanda en Azure a fin de ejecutar aplicaciones informáticas que consumen numerosos recursos.

En este tutorial se supone que no tiene experiencia previa en clústeres de proceso o HPC Pack 2012 R2. Únicamente está destinado a ayudarle a implementar un clúster de proceso híbrido rápidamente con fines de demostración. Para más información y conocer los pasos que se deben dar para implementar un clúster de HPC Pack híbrido a gran escala en un entorno de producción o para usar HPC Pack 2016, consulte las [instrucciones detalladas](http://go.microsoft.com/fwlink/p/?LinkID=200493). Para otros escenarios de HPC Pack, incluida la implementación automatizada de clústeres en máquinas virtuales de Azure, consulte [Opciones de clúster de HPC con Microsoft HPC Pack en Azure](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Requisitos previos
* **Suscripción de Azure** : si no tiene ninguna, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/) en un par de minutos.
* **Un equipo local que ejecute Windows Server 2012 R2 o Windows Server 2012**. Este equipo será el nodo principal del clúster de HPC. Si todavía no tiene Windows Server, puede descargar e instalar una [versión de evaluación](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * El equipo debe estar unido a un dominio de Active Directory. Para un escenario de prueba con una instalación nueva de Windows Server, puede agregar el rol de servidor Servicios de dominio de Active Directory y promocionar el equipo del nodo principal como controlador de dominio en un nuevo bosque de dominios (consulte la documentación de Windows Server).
  * Para admitir HPC Pack, el sistema operativo debe estar instalado en uno de estos idiomas: inglés, japonés o chino (simplificado).
  * Compruebe que estén instaladas las actualizaciones importantes y esenciales.
* **HPC Pack 2012 R2** - [Descargue](http://go.microsoft.com/fwlink/p/?linkid=328024) el paquete de instalación completo de la última versión gratis y copie los archivos en el equipo del nodo principal o en una ubicación de red. Elija los archivos de instalación que tengan el mismo idioma que su instalación de Windows Server.

    >[!NOTE]
    > Si desea usar HPC Pack 2016, en lugar de HPC Pack 2012 R2, se necesita configuración adicional. Consulte las [instrucciones detalladas](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Cuenta de dominio**. Esta cuenta se debe configurar con permisos de administrador local en el nodo principal para instalar HPC Pack.
* **Conectividad TCP en el puerto 443** desde el nodo principal a Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Instalación de HPC Pack en el nodo principal
En primer lugar, debe instalar Microsoft HPC Pack en un equipo local con un Windows Server que será el nodo principal del clúster.

1. Inicie sesión en el nodo principal usando una cuenta de dominio que tenga permisos de administrador local.
2. Inicie el asistente de instalación de HPC Pack ejecutando el archivo Setup.exe desde los archivos de instalación de HPC Pack.
3. En la pantalla de **instalación de HPC Pack 2012 R2**, haga clic en **Nueva instalación o agregar características a una instalación existente**.
   
    ![Instalación de HPC Pack 2012][install_hpc1]
4. En la página **Contrato de usuario de software de Microsoft**, haga clic en **Siguiente**.
5. En la página **Seleccionar tipo de instalación**, haga clic en **Crear un clúster de HPC creando un nodo principal** y, a continuación, haga clic en **Siguiente**.
   
    ![Selección del tipo de instalación][install_hpc2]
6. El asistente ejecuta varias pruebas de preinstalación. Haga clic en **Siguiente** on the **Installation Rules** si se pasan todas las pruebas. Si no, revise la información proporcionada y realice los cambios necesarios en su entorno. A continuación, ejecute las pruebas de nuevo o, si fuera necesario, vuelva a iniciar el asistente de instalación.
   
    ![Installation Rules][install_hpc3]
7. En la página **Configuración de la base de datos de HPC**, asegúrese de haber seleccionado **Nodo principal** en todas las bases de datos de HPC y, a continuación, haga clic en **Siguiente**.
   
    ![Configuración de la base de datos][install_hpc4]
8. Acepte las opciones seleccionadas de forma predeterminada en las páginas restantes del asistente. En la página **Instalar componentes** requeridos, haga clic en **Instalar**.
   
    ![Instalar][install_hpc6]
9. Cuando finalice la instalación, desactive **Iniciar Administrador de clústeres de HPC** y, a continuación, haga clic en **Finalizar**. (Se iniciará el Administrador de clústeres HPC en un paso posterior).
   
    ![Finalizar][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Preparación de la suscripción de Azure
Use el [Portal de Azure clásico](https://manage.windowsazure.com) para realizar los pasos siguientes con la suscripción de Azure. Estos pasos son necesarios para poder implementar más adelante los nodos de Azure desde el nodo principal local: En las siguientes secciones se encuentran los procedimientos detallados.

* Cargue un certificado de administración (necesario para las conexiones seguras entre el nodo principal y los servicios de Azure).
* Cree un servicio en la nube de Azure en el que se ejecutarán los nodos de Azure (instancias de rol de trabajo).
* Creación de una cuenta de almacenamiento de Azure
  
  > [!NOTE]
  > Anote también el Id. de la suscripción de Azure, ya que lo necesitará más adelante. Puede encontrarlo en el portal clásico haciendo clic en **Configuración** > **Suscripciones**.
  > 
  > 

### <a name="upload-the-default-management-certificate"></a>Cargar el certificado de administración predeterminado
HPC Pack instala un certificado autofirmado en el nodo principal, llamado Default Microsoft HPC Azure Management, que podrá cargar como certificado de administración de Azure. Este certificado se proporciona con fines de prueba e implementaciones de prueba de concepto.

1. En el equipo del nodo principal, inicie sesión en el [Portal de Azure clásico](https://manage.windowsazure.com).
2. Haga clic en **Configuración** > **Certificados de administración**.
3. En la barra de comandos, haga clic en **Cargar**.
   
    ![Configuración del certificado][upload_cert1]
4. Busque el archivo C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer en el nodo principal. A continuación, haga clic en el botón **Comprobar** .
   
    ![Carga del certificado][install_hpc10]

Aparecerá **Administración de HPC de Azure predeterminado** en la lista de certificados de administración.

### <a name="create-an-azure-cloud-service"></a>Crear un servicio en la nube de Azure
> [!NOTE]
> Para conseguir un mejor rendimiento, cree el servicio en la nube y (en un paso posterior) la cuenta de almacenamiento en la misma región geográfica.
> 
> 

1. En la barra de comandos del portal clásico, haga clic en **Nuevo**.
2. Haga clic en **Proceso** > **Servicio en la nube** > **Creación rápida**.
3. Escriba una dirección URL para el servicio en la nube y, a continuación, haga clic en **Crear servicio en la nube**.
   
    ![Creación de un servicio][createservice1]

### <a name="create-an-azure-storage-account"></a>Creación de una cuenta de almacenamiento de Azure
1. En la barra de comandos del portal clásico, haga clic en **Nuevo**.
2. Haga clic en **Data Services** > **Storage** > **Creación rápida**.
3. Escriba una dirección URL para la cuenta y, a continuación, haga clic en **Crear cuenta de almacenamiento**.
   
    ![Creación de almacenamiento][createstorage1]

## <a name="configure-the-head-node"></a>Configuración del nodo principal
Para usar el administrador de clústeres de HPC para implementar nodos de Azure y enviar trabajos, primero debe seguir los pasos necesarios para configurar el clúster.

1. En el nodo principal, inicie el administrador de clústeres de HPC. Si aparece el cuadro de diálogo **Select Head Node** (Seleccionar nodo principal), haga clic en **Local Computer** (Equipo local). Aparecerá la **lista de tareas pendientes de implementación** .
2. En **Tareas de implementación necesarias**, haga clic en **Configurar la red**.
   
    ![Configuración de la red][config_hpc2]
3. En el Asistente para configuración de red, seleccione **Todos los nodos solo en una red empresarial** (topología 5).
   
    ![Topología 5][config_hpc3]
   
   > [!NOTE]
   > Esta es la configuración más sencilla para fines de demostración, ya que el nodo principal solo necesita un solo adaptador de red para conectarse a Active Directory e Internet. Este tutorial no trata las situaciones de clúster que requieren redes adicionales.
   > 
   > 
4. Haga clic en **Siguiente** para aceptar los valores predeterminados de las páginas restantes del asistente. A continuación, en la pestaña **Revisar**, haga clic en **Configurar** para completar la configuración de red.
5. En la **lista de tareas pendientes de implementación**, haga clic en **Proporcionar credenciales de instalación**.
6. En el cuadro de diálogo **Credenciales de instalación** , escriba las credenciales de la cuenta de dominio que ha usado para instalar HPC Pack. y, a continuación, haga clic en **Aceptar**.
   
    ![Credenciales de instalación][config_hpc6]
   
   > [!NOTE]
   > Los servicios de HPC Pack solo usan credenciales de instalación para implementar nodos de ejecución unidos a un dominio. Los nodos de Azure que agrega en este tutorial no están unidos a un dominio.
   > 
   > 
7. En la **lista de tareas pendientes de implementación**, haga clic en **Configurar la nomenclatura de los nuevos nodos**.
8. En el cuadro de diálogo **Especificar serie de nomenclatura de nodos**, acepte la serie de nomenclatura predeterminada y haga clic en **Aceptar**.
   
    ![Nomenclatura de nodos][config_hpc8]
   
   > [!NOTE]
   > La serie de nomenclatura solo genera nombres para nodos de ejecución unidos a un dominio. Los nodos de trabajo de Azure reciben un nombre de forma automática.
   > 
   > 
9. En la **lista de tareas pendientes de implementación**, haga clic en **Crear una plantilla de nodo**. Usará una plantilla de nodo para agregar nodos de Azure al clúster.
10. En el asistente de creación de plantillas de nodo, siga estos pasos:
    
    a. En la página **Elegir el tipo de plantilla de nodo**, haga clic en la **plantilla de nodo de Azure** y después haga clic en **Siguiente**.
    
    ![Plantilla de nodo][config_hpc10]
    
    b. Haga clic en **Siguiente** para aceptar el nombre de plantilla predeterminado.
    
    c. En la página **Proporcionar información de suscripción** , especifique su identificador de suscripción de Azure (disponible en la información de cuenta de Azure). A continuación, en **Certificado de administración**, haga clic en **Examinar** y seleccione **Administración de HPC de Azure predeterminado.** A continuación, haga clic en **Siguiente**.
    
    ![Plantilla de nodo][config_hpc12]
    
    d. En la pagina **Proporcionar información del servicio** , seleccione el servicio en la nube y la cuenta de almacenamiento que ha creado en el paso anterior. A continuación, haga clic en **Siguiente**.
    
    ![Plantilla de nodo][config_hpc13]
    
    e. Haga clic en **Siguiente** para aceptar los valores predeterminados de las páginas restantes del asistente. A continuación, en la pestaña **Revisar**, haga clic en **Crear** para crear la plantilla de nodo.
    
    > [!NOTE]
    > De forma predeterminada, la plantilla de nodo de Azure incluye la configuración para que pueda iniciar (aprovisionar) y detener los nodos manualmente con el Administrador de clústeres HPC. También puede configurar una programación para iniciar y detener los nodos de Azure automáticamente.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Incorporación de nodos de Azure al clúster
Ahora usará la plantilla de nodo para agregar nodos de Azure al clúster. Al agregar los nodos al clúster, su información de configuración se almacena para que pueda iniciarlos (aprovisionarlos) en cualquier momento como instancias de rol en el servicio en la nube. Su suscripción solo se carga para los nodos de Azure después de que las instancias de rol se ejecuten en el servicio en la nube.

En este tutorial, agregará dos nodos pequeños.

1. En el Administrador de clústeres HPC, en **Administración de nodos** (llamada **Administración de recursos** en versiones recientes de HPC Pack), en el panel **Acciones**, haga clic en **Agregar nodo**.
   
    ![Agregar nodo][add_node1]
2. En el Asistente para agregar nodo, en la página **Select Deployment Method** (Seleccionar método de implementación), haga clic en **Add Windows Azure nodes** (Agregar nodos de Microsoft Azure) y, a continuación, en **Siguiente**.
   
    ![Agregar un nodo de Azure][add_node1_1]
3. En la página **Especificar nodos nuevos**, seleccione la plantilla de nodo de Azure que creó anteriormente (llamada de manera predeterminada **Default AzureNode Template**). A continuación, especifique **2** nodos de tamaño **pequeño** y, a continuación, haga clic en **Siguiente**.
   
    ![Especificar los nodos][add_node2]
   
    Para más información sobre los tamaños disponibles, consulte [Tamaños de los servicios en la nube](cloud-services-sizes-specs.md).
4. En la página **Finalización del asistente para agregar nodos**, haga clic en **Finalizar**.
   
     Ahora aparecerán dos nodos llamados **AzureCN-0001** y **AzureCN-0002** en el administrador de clústeres de HPC. Ambos tienen el estado **No implementado** .
   
    ![Nodos agregados][add_node3]

## <a name="start-the-azure-nodes"></a>Inicio de los nodos de Azure
Cuando quiera usar los recursos del clúster de Azure, use el administrador de clústeres de HPC para iniciar (aprovisionar) los nodos de Azure y conectarlos a la red.

1. En el Administrador de clústeres HPC, en **Administración de nodos** (llamada **Administración de recursos** en versiones recientes de HPC Pack), haga clic en uno o en ambos nodos y, luego, en el panel **Acciones**, haga clic en **Inicio**.
   
   ![Iniciar los nodos][add_node4]
2. En el cuadro de diálogo **Stop Windows Azure nodes** (Detener nodos de Microsoft Azure), haga clic en **Iniciar**.
   
    ![Iniciar los nodos][add_node5]
   
    Los nodos pasan al estado **Aprovisionamiento** . Consulte el registro de aprovisionamiento para realizar el seguimiento del progreso de aprovisionamiento.
   
    ![Aprovisionar nodos][add_node6]
3. Después de unos minutos, los nodos de Azure terminan de aprovisionarse y pasan al estado **Sin conexión** . Las instancias de rol se ejecutan en este estado, pero no aceptan los trabajos de clúster.
4. Para confirmar que las instancias de rol se están ejecutando, en el [portal clásico](https://manage.windowsazure.com), haga clic en **Servicios en la nube** > *nombre_del_servicio_en_la_nube* > **Instancias**.
   
    ![Instancias en ejecución][view_instances1]
   
    Verá que hay dos instancias de rol de trabajo ejecutándose en el servicio. HPC Pack también implementa automáticamente dos instancias **HpcProxy** (tamaño medio) para controlar la comunicación entre el nodo principal y Azure.
5. Para conectar los nodos de Azure y que ejecuten los trabajos de clúster, seleccione los nodos, haga clic con el botón secundario y, a continuación, haga clic en **Poner en línea**.
   
    ![Nodos desconectados][add_node7]
   
    El administrador de clústeres de HPC indica que los nodos están en estado **En línea** .

## <a name="run-a-command-across-the-cluster"></a>Ejecución de un comando en el clúster
Para comprobar la instalación, use el comando **clusrun** de HPC Pack para ejecutar un comando o una aplicación en uno o varios nodos del clúster. Por ejemplo, use **clusrun** para obtener la configuración IP de los nodos de Azure.

1. En el nodo principal, abra un símbolo del sistema.
2. Escriba el siguiente comando:
   
    `clusrun /nodes:azurecn* ipconfig`
3. Verá un resultado similar al siguiente.
   
    ![clusrun][clusrun1]

## <a name="run-a-test-job"></a>Ejecución de un trabajo de prueba
Ahora, envíe un trabajo de prueba que se ejecute en el clúster híbrido. Este ejemplo es un trabajo muy simple de barrido paramétrico (un tipo de cálculo intrínsecamente paralelo). En este ejemplo se ejecutan subtareas que agregan un entero a sí mismo con el comando **set /a** . Todos los nodos del clúster contribuyen a finalizar las subtareas para enteros del 1 al 100.

1. En el administrador de clústeres de HPC, en **Administración de trabajos**, en el panel **Acciones**, haga clic en **Nuevo trabajo de barrido paramétrico**.
   
    ![Nuevo trabajo][test_job1]
2. En el cuadro de diálogo **Nuevo trabajo de barrido paramétrico**, en la **línea de comandos**, escriba `set /a *+*` (sobrescribiendo la línea de comandos predeterminada que aparezca). Deje los valores predeterminados para el resto de la configuración y, a continuación, haga clic en **Enviar** para enviar el trabajo.
   
    ![Barrido paramétrico][param_sweep1]
3. Cuando termine el trabajo, haga doble clic en el trabajo **Mi tarea de barrido** .
4. Haga clic en **Ver tareas**y, a continuación, haga clic en una subtarea para ver la salida calculada de dicha subtarea.
   
    ![Resultados de la tarea][view_job361]
5. Para ver qué nodo ha realizado el cálculo en esa subtarea, haga clic en **Nodos asignados**. (Su clúster podría mostrar un nombre de nodo diferente).
   
    ![Resultados de la tarea][view_job362]

## <a name="stop-the-azure-nodes"></a>Detención de los nodos de Azure
Después de probar el clúster, detenga los nodos de Azure e impida que se produzcan cargas innecesarias en la cuenta. De este modo se detiene el servicio en la nube y se eliminan las instancias de rol de Azure.

1. En el Administrador de clústeres HPC, en **Administración de nodos** (llamada **Administración de recursos** en versiones recientes de HPC Pack), seleccione los dos nodos de Azure. A continuación, en el panel **Acciones**, haga clic en **Detener**.
   
    ![Detener los nodos][stop_node1]
2. En el cuadro de diálogo **Stop Windows Azure nodes** (Detener nodos de Microsoft Azure), haga clic en **Detener**.
   
    ![Detener los nodos][stop_node2]
3. Los nodos pasan al estado **En detención** . Después de unos minutos, el administrador de clústeres de HPC indica que los nodos tienen el estado **No implementado**.
   
    ![Nodos no implementados][stop_node4]
4. Para confirmar que las instancias de rol ya no se están ejecutando en Azure, en el [portal clásico](https://manage.windowsazure.com), haga clic en **Servicios en la nube** > *nombre_del_servicio_en_la_nube* > **Instancias**. No se implementarán instancias en el entorno de producción.
   
    ![Sin instancias][view_instances2]
   
    Con esto finaliza el tutorial.

## <a name="next-steps"></a>Pasos siguientes
* Explore la documentación correspondiente a [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Para configurar una implementación híbrida de clústeres HPC Pack a mayor escala, consulte [Irrupción en instancias de rol de trabajo de Azure con Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493).
* Para ver otras formas de crear un clúster HPC Pack en Azure como, por ejemplo, el uso de plantillas de Azure Resource Manager, consulte [Opciones de clúster HPC con Microsoft HPC Pack en Azure](../virtual-machines/virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Consulte [Big Compute en Azure: Recursos técnicos para informática de alto rendimiento (HPC) y computación por lotes](../batch/big-compute-resources.md) para más información sobre la gama de soluciones en la nube de Big Compute y HPC de Azure.

[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc2.png
[install_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc3.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[upload_cert1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/upload_cert1.png
[createstorage1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createstorage1.png
[createservice1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createservice1.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node5]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node5.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node2.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png



<!--HONumber=Feb17_HO1-->


