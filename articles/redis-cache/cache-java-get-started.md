---
title: Uso de Azure Redis Cache con Java | Microsoft Docs
description: "Introducción a Caché en Redis de Azure usando Java"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 5369dcd6ad1ec93c63eb442db9fc5ffdcca37375
ms.openlocfilehash: b95f37db90b105962c01545e25c8e14c53257ebc


---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Uso de Caché en Redis de Azure con Java
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Caché en Redis de Azure le proporciona acceso a una caché en Redis dedicada, administrada por Microsoft. Se puede obtener acceso a su caché desde cualquier aplicación dentro de Microsoft Azure.

En este tema se explica cómo comenzar a usar Caché en Redis de Azure mediante Java.

## <a name="prerequisites"></a>Requisitos previos
[Jedis](https://github.com/xetorthio/jedis) - Cliente de Java para Redis

Este tutorial usa Jedis, pero puede usar cualquier cliente de Java enumerado en [http://redis.io/clients](http://redis.io/clients).

## <a name="create-a-redis-cache-on-azure"></a>Crear una caché de Redis en Azure
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Recuperación del nombre de host y las claves de acceso
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Conexión a la caché de forma segura mediante SSL
Las últimas compilaciones de [jedis](https://github.com/xetorthio/jedis) permiten conectarse a Azure Redis Cache mediante SSL. En el ejemplo siguiente se muestra cómo conectarse a Caché en Redis de Azure con el punto de conexión SSL de 6380. Reemplace `<name>` por el nombre de la memoria caché y `<key>` por su clave principal o secundaria, tal como se ha descrito en la sección anterior, [Recuperación del nombre de host y las claves de acceso](#retrieve-the-host-name-and-access-keys).

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */

> [!NOTE]
> El puerto no SSL está deshabilitado para instancias nuevas de Azure Redis Cache. Si usa otro cliente incompatible con SSL, consulte [cómo habilitar el puerto no SSL](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Agregar algo a la memoria caché y recuperarlo
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Pasos siguientes
* [Habilite los diagnósticos de cache](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) para que pueda [supervisar](https://msdn.microsoft.com/library/azure/dn763945.aspx) el estado de la memoria caché.
* Lea la [documentación de Redis](http://redis.io/documentation)oficial.



<!--HONumber=Feb17_HO2-->


