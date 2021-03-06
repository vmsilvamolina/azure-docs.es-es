---
title: Opciones de Azure Storage para R Server en HDInsight | Microsoft Docs
description: "Obtenga información sobre las distintas opciones de almacenamiento disponibles para los usuarios con R Server en HDInsight."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 2df17cddf629cb72b7fa4d590dfaa69311c96aa4
ms.openlocfilehash: 3e47a7e0382009a07b885a28c6e8d90f9bff9cfb
ms.lasthandoff: 01/10/2017


---
# <a name="azure-storage-options-for-r-server-on-hdinsight"></a>Opciones de Azure Storage para R Server en HDInsight
Microsoft R Server en HDInsight tiene acceso a Azure Blob Storage y a [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/) como forma de conservar datos, código, objetos de resultado de análisis, etc.

Cuando se crea un clúster de Hadoop en HDInsight, se especifica una cuenta de Azure Storage o Data Lake Store. Un contenedor de almacenamiento específico de esa cuenta conserva el sistema de archivos para el clúster creado (por ejemplo, el Sistema de archivos distribuido de Hadoop). Con vistas al rendimiento, el clúster de HDInsight se crea en el mismo centro de datos que la cuenta de almacenamiento principal especificada. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md "Uso de almacenamiento de blobs de Azure con HDInsight").   

## <a name="use-multiple-azure-blob-storage-accounts"></a>Uso de varias cuentas de almacenamiento de blobs de Azure
Si es necesario, se puede acceder a varias cuentas de almacenamiento o contenedores de Azure con el clúster de HDI. Para ello, tiene que especificar las cuentas de almacenamiento adicionales en la interfaz de usuario durante la creación del clúster y seguir estos pasos para poder usarlas en R.  

1. Cree un clúster de HDInsight con el nombre de cuenta de almacenamiento **storage1** y un contenedor predeterminado denominado **container1**.
2. Especifique una cuenta de almacenamiento adicional con el nombre **storage2**.  
3. Copie el archivo mycsv.csv en el directorio /share y realice un análisis en ese archivo.  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. En código R, establezca el nodo de nombre en **default** e indique el directorio y el archivo que se van a procesar.  

        myNameNode <- "default"
        myPort <- 0

    Ubicación de los datos:  

        bigDataDirRoot <- "/share"  

    Definición del contexto de proceso de Spark:

        mySparkCluster <- RxSpark(consoleOutput=TRUE)

    Establecimiento del contexto de proceso:

        rxSetComputeContext(mySparkCluster)

    Definición del Sistema de archivos distribuido de Hadoop (HDFS):

        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    Especificación del archivo de entrada en HDFS:

        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Todas las referencias de archivos y directorios apuntan a la cuenta de almacenamiento wasbs://container1@storage1.blob.core.windows.net. Esta es la **cuenta de almacenamiento predeterminada** asociada al clúster de HDInsight.

Ahora supongamos que quiere procesar un archivo llamado mySpecial.csv que se encuentra en el directorio /private de **container2** en **storage2**.

En el código R, apunte la referencia del nodo de nombres a la cuenta de almacenamiento **storage2** .

````
myNameNode <- "wasbs://container2@storage2.blob.core.windows.net"
myPort <- 0
````

Ubicación de los datos:

````
bigDataDirRoot <- "/private"
````

Definición del contexto de proceso de Spark:

````
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)
````

Establecimiento del contexto de proceso:

````
rxSetComputeContext(mySparkCluster)
````

Definición del sistema de archivos HDFS:

````
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)
````

Especificación del archivo de entrada en HDFS:

````
inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
````

Todas las referencias de archivos y directorios apuntan ahora a la cuenta de almacenamiento wasbs://container2@storage2.blob.core.windows.net. Este es el **nodo de nombres** que ha especificado.

Tenga en cuenta que deberá configurar el directorio /user/RevoShare/<SSH username> en **storage2** de la forma siguiente:

````
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare
hadoop fs -mkdir wasbs://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>
````

## <a name="use-an-azure-data-lake-store"></a>Uso de un almacén de Azure Data Lake
Para utilizar los almacenes de Data Lake con su cuenta de HDInsight, tiene que dar al clúster acceso a cada almacén de Azure Data Lake que quiera usar. El uso del almacén en el script de R es muy similar al uso de una cuenta de almacenamiento secundaria (tal como se describe en el procedimiento anterior).

## <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Incorporación de acceso del clúster a los almacenes de Azure Data Lake
El acceso a un almacén de Azure Data Lake se establece mediante el uso de una entidad de servicio de Azure Active Directory (AAD) asociada a su clúster de HDInsight.

### <a name="to-add-a-service-principal"></a>Incorporación de una entidad de servicio
1. Al crear el clúster de HDInsight, seleccione **Identidad de AAD del clúster** en la pestaña **Origen de datos**.

2. En el cuadro de diálogo **Identidad de AAD del clúster**, en **Seleccionar entidad de servicio de AD**, elija **Crear nuevo**.

Después de asignar un nombre a la entidad de servicio y crear una contraseña para la misma, haga clic en **Administrar acceso a ADLS** para asociar la entidad de servicio a los almacenes Data Lake Store.

También es posible agregar acceso al clúster a uno o varios almacenes Data Lake Store después de crear el clúster; para ello, abra la entrada de Azure Portal para un almacén Data Lake Store y vaya a **Explorador de datos > Acceso > Agregar**. 

Para obtener detalles adicionales sobre cómo agregar el acceso para el clúster HDI a almacenes Data Lake Store, vea el artículo [Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#create-an-hdinsight-cluster-with-access-to-azure-data-lake-store).

## <a name="use-the-data-lake-store-with-r-server"></a>Uso del almacén de Data Lake con R Server
Una vez que haya dado acceso a un almacén de Data Lake, puede usar el almacén en R Server en HDInsight de la manera en que lo haría con una cuenta de almacenamiento de Azure secundaria. La única diferencia es que el prefijo **wasb://** cambia a **adl://**, de la forma siguiente:

````
# Point to the ADL store (e.g. ADLtest)
myNameNode <- "adl://rkadl1.azuredatalakestore.net"
myPort <- 0

# Location of the data (assumes a /share directory on the ADL account)
bigDataDirRoot <- "/share"  

# Define Spark compute context
mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

# Set compute context
rxSetComputeContext(mySparkCluster)

# Define HDFS file system
hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# Specify the input file in HDFS to analyze
inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

# Create factors for days of the week
colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

# Define the data source
airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

# Run a linear regression
model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)
````

Estos son los comandos que se usan para configurar la cuenta de almacenamiento de Data Lake con el directorio RevoShare y agregar el archivo .csv de ejemplo para el ejemplo anterior:

````
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share
````

## <a name="use-azure-files-on-the-edge-node"></a>Uso de Archivos de Azure en el nodo perimetral
También hay una opción de almacenamiento de datos adecuada para su uso en el nodo perimetral llamada [Archivos de Azure](../storage/storage-how-to-use-files-linux.md "Archivos de Azure"). Con esta opción podrá montar un recurso compartido de archivos de Almacenamiento de Azure en el sistema de archivos de Linux. Esto puede resultar práctico para almacenar archivos de datos, scripts de R y objetos de resultado que podría necesitar más adelante cuando sea conveniente usar el sistema de archivos nativo en el nodo perimetral en lugar de HDFS.

Una ventaja importante de Archivos de Azure es que los recursos compartidos de archivos se pueden montar y utilizar en cualquier sistema que tenga un sistema operativo compatible, como Windows o Linux. Por ejemplo, puede utilizarse con otro clúster de HDInsight que sea suyo o de alguien de su equipo, con una máquina virtual de Azure, o incluso con un sistema local.

## <a name="next-steps"></a>Pasos siguientes
Ahora que comprende las opciones de Azure Storage, use los vínculos siguientes para detectar otras maneras de trabajar con R Server en HDInsight.

* [Información general de R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-overview.md)
* [Introducción al servidor de R en Hadoop](hdinsight-hadoop-r-server-get-started.md)
* [Instalación de RStudio con R Server en HDInsight (si no está instalado durante la creación del clúster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-compute-contexts.md)


