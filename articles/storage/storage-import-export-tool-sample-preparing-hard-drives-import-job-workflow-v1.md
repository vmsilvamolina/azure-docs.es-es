---
title: "Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación de Azure| Microsoft Docs"
description: "Vea un tutorial para conocer el proceso completo de preparación de las unidades para un trabajo de importación en el servicio Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
translationtype: Human Translation
ms.sourcegitcommit: 8de848b1192ff1c10e0375053c4e03f18c06184e
ms.openlocfilehash: ee7a8c9ae4cda5b67184100dd37ee4e0384aff26
ms.lasthandoff: 02/16/2017


---

# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación
Este tema le guiará por el proceso completo de preparar las unidades para un trabajo de importación.  
  
Este ejemplo importa los datos siguientes en una cuenta de Azure Storage denominada `mystorageaccount`:  
  
|Ubicación|Descripción|  
|--------------|-----------------|  
|H:\Video|Una colección de vídeos, 5 TB en total.|  
|H:\Photo|Una colección de fotos, 30 GB en total.|  
|K:\Temp\FavoriteMovie.ISO|Una imagen de disco Blu-ray™, 25 GB.|  
|\\\bigshare\john\music|Una colección de archivos de música en un recurso compartido de red, 10 GB en total.|  
  
El trabajo de importación importará estos datos en los siguientes destinos en la cuenta de almacenamiento:  
  
|Origen|Directorio virtual o blob de destino|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|  
  
Con esta asignación, el archivo `H:\Video\Drama\GreatMovie.mov` se importará al blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
A continuación, para determinar cuántas unidades de disco duro se necesitan, calcule el tamaño de los datos:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
En este ejemplo, dos unidades de disco duro de 3TB deberían ser suficientes. Sin embargo, dado que el directorio de origen `H:\Video` tiene 5 TB de datos y la capacidad de la unidad de disco duro es de solo 3TB, es necesario dividir `H:\Video` en dos directorios más pequeños antes de ejecutar la herramienta Microsoft Azure Import/Export: `H:\Video1` y `H:\Video2`. Este paso genera los siguientes directorios de origen:  
  
|Ubicación|Tamaño|Directorio virtual o blob de destino|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 GB|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.blob.core.windows.net/music|  
  
 Tenga en cuenta que aunque el directorio `H:\Video` se ha dividido en dos directorios, ambos apuntan al mismo directorio virtual de destino en la cuenta de almacenamiento. De esta manera, todos los archivos de vídeo se mantienen en un único contenedor `video` en la cuenta de almacenamiento.  
  
 A continuación, los directorios de origen anteriores se distribuyen uniformemente en las dos unidades de disco duro:  
  
||||  
|-|-|-|  
|Unidad de disco duro|Directorios de origen|Tamaño total|  
|Primera unidad|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Segunda unidad|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Además, puede establecer los metadatos siguientes para todos los archivos:  
  
-   **UploadMethod:** Windows Azure Import/Export Service  
  
-   **DataSetName:** SampleData  
  
-   **CreationDate:** 10/1/2013  
  
Para establecer los metadatos de los archivos importados, cree un archivo de texto, `c:\WAImportExport\SampleMetadata.txt`, con el siguiente contenido:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export Service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
También puede establecer algunas propiedades para el blob `FavoriteMovie.ISO`:  
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
Para establecer estas propiedades, cree un archivo de texto, `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Ahora está listo para ejecutar la herramienta Azure Import/Export para preparar las dos unidades de disco duro. Observe lo siguiente:  
  
-   La primera unidad se monta como unidad X.  
  
-   La segunda unidad se monta como unidad Y.  
  
-   La clave para la cuenta de almacenamiento `mystorageaccount` es `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Preparación del disco para la importación cuando los datos se han cargado previamente
 
 Si los datos que se van a importar ya están presentes en el disco, use la marca /skipwrite. Tanto el valor de /t como de /srcdir deben apuntar al disco que se está preparando para la importación. Si no es necesario que todos los datos del disco vayan al mismo directorio virtual de destino o raíz de la cuenta de almacenamiento, ejecute el mismo comando para cada directorio por separado manteniendo el valor de/id igual en todas las ejecuciones.

>[!NOTE] 
>No especifique /format, porque borrará los datos del disco. Puede especificar /encrypt o /bk dependiendo de si el disco ya está cifrado o no. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

Para la primera unidad, ejecute la herramienta Azure Import/Export dos veces para copiar los dos directorios de origen:  
  
```
## First copy session for first drive  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

```  
## Second copy session for first drive  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```
  
Para la segunda unidad, ejecute la herramienta Azure Import/Export tres veces, una para cada uno de los directorios de origen y otra para el archivo de imagen independiente Blu-Ray™:  
  
```
## First copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
```
## Second copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
```
## Third copy session  
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
Cuando se hayan completado las sesiones de copia, podrá desconectar las dos unidades del equipo de copia y enviarlas al centro de datos de Microsoft Azure adecuado. Cargará los dos archivos de diario, `FirstDrive.jrn` y `SecondDrive.jrn`, cuando cree el trabajo de importación en el [Portal de administración de Microsoft Azure](https://manage.windowsazure.com/).  
  
## <a name="see-also"></a>Otras referencias  
[Preparing Hard Drives for an Import Job](storage-import-export-tool-preparing-hard-drives-import-v1.md)  (Preparación de unidades de disco duro para un trabajo de importación)  
[Quick reference for frequently used commands for import jobs](storage-import-export-tool-quick-reference-v1.md) (Referencia rápida para comandos usados con frecuencia) 

