---
title: Traslado de los datos de HDFS local | Microsoft Docs
description: "Obtenga información acerca de cómo mover datos desde HDFS local mediante Factoría de datos de Azure"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 3215b82d-291a-46db-8478-eac1a3219614
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: d49d7e6b4a9485c2371eb02ac8068adfde9bad6b
ms.openlocfilehash: c7f27fe2560c1800f05c205a73fe738cc609d642


---
# <a name="move-data-from-on-premises-hdfs-using-azure-data-factory"></a>Movimiento de datos desde HDFS local mediante Factoría de datos de Azure
En este artículo se describe cómo se puede usar la actividad de copia en Factoría de datos de Azure para mover datos de HDFS local a otro almacén de datos. Este artículo se basa en el artículo sobre [actividades de movimiento de datos](data-factory-data-movement-activities.md) , que presenta una introducción general del movimiento de datos con la actividad de copia y las combinaciones del almacén de datos admitidas.

En la actualidad, Factoría de datos solo admite el movimiento de datos desde HDFS local a otros almacenes de datos, pero no el movimiento inverso.

## <a name="enabling-connectivity"></a>Habilitación de la conectividad
El servicio Factoría de datos admite la conexión a HDFS local mediante Data Management Gateway. Consulte el artículo sobre cómo [mover datos entre ubicaciones locales y la nube](data-factory-move-data-between-onprem-and-cloud.md) para obtener información acerca de Data Management Gateway, así como instrucciones paso a paso sobre cómo configurar la puerta de enlace. Use la puerta de enlace para conectar con HDFS, aunque esté hospedado en una máquina virtual de IaaS de Azure.

Aunque puede instalar la puerta de enlace en el mismo equipo local o en la máquina virtual de Azure como HDFS, se recomienda que instale la puerta de enlace en un equipo independiente o una máquina virtual de IaaS de Azure. Al tener la puerta de enlace en un equipo independiente, se reduce la contención de recursos y se mejora el rendimiento. Al instalar la puerta de enlace en una máquina independiente, la máquina debería poder acceder a la máquina con HDFS.

## <a name="copy-data-wizard"></a>Asistente para copia de datos
La manera más sencilla de crear una canalización que copie datos hacia o desde el sistema HDFS local es usar el Asistente para copia de datos. Consulte [Tutorial: crear una canalización con la actividad de copia mediante el Asistente para copia de Data Factory](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización mediante el Asistente para copiar datos.

En los siguientes ejemplos se proporcionan definiciones JSON que puede usar para crear una canalización mediante [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md) o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). En ellos se muestra cómo copiar datos de un sistema HDFS local a Almacenamiento de blobs de Azure. Sin embargo, los datos se pueden copiar en cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.

## <a name="sample-copy-data-from-on-premises-hdfs-to-azure-blob"></a>Ejemplo: copiar datos de HDFS local a un blob de Azure
En este ejemplo, se muestra cómo copiar datos de un sistema HDFS local al Almacenamiento de blobs de Azure. Sin embargo, se pueden copiar datos **directamente** a cualquiera de los receptores indicados [aquí](data-factory-data-movement-activities.md#supported-data-stores-and-formats) mediante la actividad de copia en Data Factory de Azure.  

El ejemplo consta de las siguientes entidades de factoría de datos:

1. Un servicio vinculado del tipo [OnPremisesHdfs](#hdfs-linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [FileShare](#hdfs-dataset-type-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [FileSystemSource](#hdfs-copy-activity-type-properties) y [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

El ejemplo copia los datos de un HDFS local a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

En primer lugar, configure la puerta de enlace de administración de datos. Las instrucciones se encuentran en el artículo sobre cómo [mover datos entre ubicaciones locales y en la nube](data-factory-move-data-between-onprem-and-cloud.md) .

**Servicio vinculado de HDFS** En este ejemplo se usa la autenticación de Windows. Consulte la sección [Propiedades del servicio vinculado de HDFS](#hdfs-linked-service-properties) para conocer los diferentes tipos de autenticación que se pueden usar.

```JSON
{
    "name": "HDFSLinkedService",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

**Servicio vinculado de Almacenamiento de Azure**

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Conjunto de datos de entrada de HDFS** Este conjunto de datos hace referencia a la carpeta DataTransfer/UnitTest/ de HDFS. La canalización copia todos los archivos de su carpeta en el destino.

Si se establece "external": "true", se informa al servicio Data Factory que el conjunto de datos es externo a Data Factory y que no lo genera ninguna actividad de la factoría de datos.

```JSON
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1). La ruta de acceso de la carpeta para el blob se evalúa dinámicamente según la hora de inicio del segmento que se está procesando. La ruta de acceso de la carpeta usa las partes year, month, day y hours de la hora de inicio.

```JSON
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar estos conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición de JSON de canalización, el tipo **source** se establece en **FileSystemSource** y el tipo **sink** se establece en **BlobSink**. La consulta SQL especificada para la propiedad **query** selecciona los datos de la última hora que se van a copiar.

```JSON
{
    "name": "pipeline",
    "properties":
    {
        "activities":
        [
            {
                "name": "HdfsToBlobCopy",
                "inputs": [ {"name": "InputDataset"} ],
                "outputs": [ {"name": "OutputDataset"} ],
                "type": "Copy",
                "typeProperties":
                {
                    "source":
                    {
                        "type": "FileSystemSource"
                    },
                    "sink":
                    {
                        "type": "BlobSink"
                    }
                },
                "policy":
                {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "00:05:00"
                }
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```

## <a name="hdfs-linked-service-properties"></a>Propiedades del servicio vinculado de HDFS
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de HDFS.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **Hdfs** |Sí |
| URL |Dirección URL a HDFS |Sí |
| authenticationType |Anónima o Windows. <br><br> Para usar la **autenticación Kerberos** para el conector HDFS, consulte [esta sección](#use-kerberos-authentication-for-hdfs-connector) a fin de configurar el entorno local en consecuencia. |Sí |
| userName |Nombre de usuario para la autenticación de Windows |Sí (para la autenticación de Windows) |
| contraseña |Contraseña para la autenticación de Windows |Sí (para la autenticación de Windows) |
| gatewayName |Nombre de la puerta de enlace que el servicio Factoría de datos debe usar para conectarse a HDFS. |Sí |
| encryptedCredential |[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) de la credencial de acceso. |No |

Consulte [Movimiento de datos entre orígenes locales y la nube con Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) para obtener más información sobre cómo establecer las credenciales para HDFS local.

### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-windows-authentication"></a>Uso de autenticación de Windows

```JSON
{
    "name": "hdfs",
    "properties":
    {
        "type": "Hdfs",
        "typeProperties":
        {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="use-kerberos-authentication-for-hdfs-connector"></a>Uso de autenticación Kerberos para el conector HDFS
Existen dos opciones para configurar el entorno local para usar la autenticación Kerberos en el conector HDFS. Puede elegir la que mejor se adapte en su caso.
* Opción 1: [Hacer que la máquina de puerta de enlace se una al dominio Kerberos](#kerberos-join-realm)
* Opción 2: [Habilitar la confianza mutua entre el dominio de Windows y el dominio Kerberos](#kerberos-mutual-trust)

### <a name="a-namekerberos-join-realmaoption-1-make-gateway-machine-join-kerberos-realm"></a><a name="kerberos-join-realm"></a>Opción 1: Hacer que la máquina de puerta de enlace se una al dominio Kerberos

#### <a name="requirement"></a>Requisito:

* La máquina de puerta de enlace debe unirse el dominio Kerberos y no puede unirse a ningún dominio de Windows.

#### <a name="how-to-configure"></a>Configuración:

**En la máquina de puerta de enlace:**

1.  Ejecute la utilidad **Ksetup** para configurar el dominio y el servidor KDC de Kerberos.

    La máquina debe configurarse como miembro de un grupo de trabajo dado que un dominio Kerberos es diferente de un dominio de Windows. Para ello, establezca el dominio Kerberos y agregue un servidor KDC de la manera siguiente: Sustituya *REALM.COM* por su dominio respectivo, según sea necesario.

            C:> Ksetup /setdomain REALM.COM
            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>

    **Reinicie** la máquina después de ejecutar estos 2 comandos.

2.  Compruebe la configuración con el comando **Ksetup**. La salida debe ser parecida a esta:

            C:> Ksetup
            default realm = REALM.COM (external)
            REALM.com:
                kdc = <your_kdc_server_address>

**En Azure Data Factory:**

* Configure el conector HDFS mediante la **autenticación de Windows** junto con el nombre y la contraseña de la entidad de seguridad de Kerberos para conectarse al origen de datos de HDFS. Compruebe la sección [HDFS Linked Service properties](#hdfs-linked-service-properties) (Propiedades de servicio vinculado de HDFS) en los detalles de configuración.

### <a name="a-namekerberos-mutual-trustaoption-2-enable-mutual-trust-between-windows-domain-and-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Opción 2: Habilitar la confianza mutua entre el dominio de Windows y el dominio Kerberos

#### <a name="requirement"></a>Requisito:
*   La máquina de puerta de enlace debe unirse a un dominio de Windows.
*   Necesita permiso para actualizar la configuración del controlador de dominio.

#### <a name="how-to-configure"></a>Configuración:

> [!NOTE]
> Sustituya REALM.COM y AD.COM en el siguiente tutorial por su propio dominio y controlador de dominio, según sea necesario.

**En el servidor KDC:**

1.  Edite la configuración de KDC en el archivo **krb5.conf** para permitir que KDC confíe en el dominio de Windows al que se hace referencia debajo de la plantilla de configuración. De forma predeterminada, la configuración está ubicada en **/etc/krb5.conf**.

            [logging]
             default = FILE:/var/log/krb5libs.log
             kdc = FILE:/var/log/krb5kdc.log
             admin_server = FILE:/var/log/kadmind.log

            [libdefaults]
             default_realm = REALM.COM
             dns_lookup_realm = false
             dns_lookup_kdc = false
             ticket_lifetime = 24h
             renew_lifetime = 7d
             forwardable = true

            [realms]
             REALM.COM = {
              kdc = node.REALM.COM
              admin_server = node.REALM.COM
             }
            AD.COM = {
             kdc = windc.ad.com
             admin_server = windc.ad.com
            }

            [domain_realm]
             .REALM.COM = REALM.COM
             REALM.COM = REALM.COM
             .ad.com = AD.COM
             ad.com = AD.COM

            [capaths]
             AD.COM = {
              REALM.COM = .
             }

        **Restart** the KDC service after configuration.

2.  Prepare una entidad de seguridad llamada **krbtgt/REALM.COM@AD.COM** en el servidor KDC con el siguiente comando:

            Kadmin> addprinc krbtgt/REALM.COM@AD.COM

3.  En el archivo de configuración de servicio de HDFS **hadoop.security.auth_to_local**, agregue `RULE:[1:$1@$0](.*@AD.COM)s/@.*//`.

**En el controlador de dominio:**

1.  Ejecute a continuación los comandos **Ksetup** para agregar una entrada de dominio:

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

2.  Establezca la confianza entre el dominio de Windows y el dominio Kerberos. [contraseña] es la contraseña de la entidad de seguridad ** krbtgt/REALM.COM@AD.COM**.

            C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /passwordt:[password]

3.  Seleccione el algoritmo de cifrado usado en Kerberos.

    1. Vaya a Administrador de servidores > Administración de directivas de grupo > Dominio > Objetos de directiva de grupo > Default or Active Domain Policy (Directiva de dominio predeterminada o activa) y haga clic en Editar.

    2. En la ventana emergente **Editor de administración de directivas de grupo**, vaya a Configuración de equipo > Directivas > Configuración de Windows > Configuración de seguridad > Directivas locales > Opciones de seguridad, y configure **Seguridad de red: configurar tipos de cifrado permitidos para Kerberos**.

    3. Seleccione el algoritmo de cifrado que quiere usar al conectarse a KDC. Normalmente, puede seleccionar todas las opciones.

        ![Configuración de tipos de cifrado para Kerberos](media/data-factory-hdfs-connector/config-encryption-types-for-kerberos.png)

    4. Use el comando **Ksetup** para especificar el algoritmo de cifrado que se usará en el dominio específico.

                C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96

4.  Cree la asignación entre la cuenta de dominio y la entidad de seguridad de Kerberos, a fin de usar la entidad de seguridad de Kerberos en el dominio de Windows.

    1. Inicie las herramientas administrativas > **Usuarios y equipos de Active Directory**.

    2. Configure características avanzadas; para ello, haga clic en **Ver** > **Características avanzadas**.

    3. Busque la cuenta a la que quiere crear asignaciones y haga clic con el botón derecho para ver **Asignaciones de nombres** > haga clic en la pestaña **Nombres de Kerberos**.

    4. Agregue una entidad de seguridad desde el dominio.

        ![Asignación de la identidad de seguridad](media/data-factory-hdfs-connector/map-security-identity.png)

**En la máquina de puerta de enlace:**

* Ejecute a continuación los comandos **Ksetup** para agregar una entrada de dominio.

            C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
            C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM

**En Azure Data Factory:**

* Configure el conector de HDFS mediante la **autenticación de Windows** en combinación con la cuenta de dominio o la entidad de seguridad de Kerberos para conectarse al origen de datos de HDFS. Compruebe la sección [HDFS Linked Service properties](#hdfs-linked-service-properties) (Propiedades de servicio vinculado de HDFS) en los detalles de configuración.


## <a name="hdfs-dataset-type-properties"></a>Propiedades del tipo de conjunto de datos de HDFS
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos del tipo **FileShare** (que incluye el conjunto de datos de HDFS) tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| folderPath |Ruta de acceso a la carpeta. Ejemplo: `myfolder`<br/><br/>Use el carácter de escape "\" para los caracteres especiales de la cadena. Por ejemplo: para folder\subfolder, especifique la carpeta\\\\subcarpeta y para d:\samplefolder, especifique d:\\\\samplefolder.<br/><br/>Puede combinar esta propiedad con **partitionBy** para que las rutas de acceso de carpeta se basen en las fechas y horas de inicio y finalización del segmento. |Sí |
| fileName |Especifique el nombre del archivo en **folderPath** si quiere que la tabla haga referencia a un archivo específico de la carpeta. Si no especifica ningún valor para esta propiedad, la tabla apunta a todos los archivos de la carpeta.<br/><br/>Si no se especifica fileName para un conjunto de datos de salida, el nombre del archivo generado estaría en el siguiente formato: <br/><br/>Data.<Guid>.txt (por ejemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy se puede usar para especificar un valor de folderPath dinámico, un nombre de archivo para datos de series temporales. Por ejemplo, folderPath se parametriza para cada hora de datos. |No |
| formato | Se admiten los siguientes tipos de formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** y **ParquetFormat**. Establezca la propiedad **type** de formato en uno de los siguientes valores. Para más información, consulte las secciones [Formato de texto](#specifying-textformat), [Formato Json](#specifying-jsonformat), [Formato Avro](#specifying-avroformat), [Formato Orc](#specifying-orcformat) y [Formato Parquet](#specifying-parquetformat). <br><br> Si desea **copiar los archivos tal cual** entre los almacenes basados en archivos (copia binaria), omita la sección de formato en las definiciones de los conjuntos de datos de entrada y salida. |No |
| compresión | Especifique el tipo y el nivel de compresión de los datos. Los tipos admitidos son: **GZip**, **Deflate**, **BZip2** y **ZipDeflate**, y los niveles que se admiten son: **Optimal** (Óptimo) y **Fastest** (Más rápido). Para más información, consulte la sección [Especificación de la compresión](#specifying-compression). |No |

> [!NOTE]
> filename y fileFilter no pueden usarse simultáneamente.
>
>

### <a name="using-partionedby-property"></a>Uso de la propiedad partitionedBy
Como ya se ha indicado en la sección anterior, con partitionedBy se puede especificar un valor de folderPath dinámico, un nombre de archivo para datos de series temporales. Esto se puede hacer con las macros de Factoría de datos y las variables de sistema SliceStart y SliceEnd, que indican el periodo de tiempo lógico de un segmento de datos especificado.

Para aprender más sobre los conjuntos de datos de series temporales [Creación de conjuntos de datos](data-factory-create-datasets.md), [Programación y ejecución](data-factory-scheduling-and-execution.md) y [Creación de canalizaciones](data-factory-create-pipelines.md) para conocer más detalles sobre los conjuntos de datos de series temporales, la programación y los segmentos.

#### <a name="sample-1"></a>Muestra 1:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
En este ejemplo, {Slice} se reemplaza por el valor de la variable del sistema SliceStart de Data Factory en el formato (YYYYMMDDHH) especificado. SliceStart hace referencia a la hora de inicio del segmento. folderPath es diferente para cada segmento. Por ejemplo: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Ejemplo 2:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
En este ejemplo, year, month, day y time de SliceStart se extraen en variables independientes que se usan en las propiedades folderPath y fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="hdfs-copy-activity-type-properties"></a>Propiedades de tipo de actividad de copia de HDFS
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

En caso de actividad de copia, si el origen es del tipo **FileSystemSource** , estarán disponibles las propiedades siguientes en la sección typeProperties:

**FileSystemSource** admite las siguientes propiedades:

| Propiedad | Descripción | Valores permitidos | Obligatorio |
| --- | --- | --- | --- |
| recursive |Indica si los datos se leen de forma recursiva de las subcarpetas o solo de la carpeta especificada. |True, False (predeterminada) |No |

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.



<!--HONumber=Jan17_HO2-->


