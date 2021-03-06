---
title: "Introducción a Azure IoT Hub (.NET) | Microsoft Docs"
description: "Cómo enviar mensajes del dispositivo a la nube desde un dispositivo a una instancia de IoT Hub de Azure mediante el SDK de IoT de Azure para .NET. Cree una aplicación de dispositivo simulado para enviar mensajes, una aplicación de servicio para registrar el dispositivo en el registro de identidad y una aplicación de servicio para leer los mensajes de dispositivo a la nube desde IoT Hub."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: f40604ff-8fd6-4969-9e99-8574fbcf036c
ms.service: iot-hub
ms.devlang: dotnet
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 969e6bd55ca69e293f13b66f1a51f1d5fd1996b7
ms.lasthandoff: 03/06/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-net"></a>Conexión del dispositivo simulado en el centro de IoT con .NET
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Al final de este tutorial tendrá tres aplicaciones de consola de .NET:

* **CreateDeviceIdentity**, que crea una identidad de dispositivo y una clave de seguridad asociada para conectar la aplicación de dispositivo simulado.
* **ReadDeviceToCloudMessages**, que muestra los datos de telemetría enviados por la aplicación de dispositivo simulado.
* **SimulatedDevice**, que se conecta con su IoT Hub con la identidad de dispositivo creada anteriormente, y envía un mensaje de telemetría cada segundo mediante el protocolo MQTT.

> [!NOTE]
> Para más información acerca de los diversos SDK que puede usar para crear ambas aplicaciones para que se ejecuten en dispositivos y en el back-end de la solución, consulte [Azure IoT SDKs][lnk-hub-sdks] (SDK de IoT de Azure).
> 
> 

Para completar este tutorial, necesitará lo siguiente:

* Visual Studio 2015 o Visual Studio 2017.
* Una cuenta de Azure activa. (En caso de no tenerla, puede crear una [cuenta gratuita][lnk-free-trial] en solo unos minutos).

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Ya se creó IoT Hub y ya tiene el nombre de host y la cadena de conexión de IoT Hub que necesita para completar el resto del tutorial.

## <a name="create-a-device-identity"></a>Creación de una identidad de dispositivo
En esta sección, creará una aplicación de consola .NET que crea una identidad de dispositivo en el registro de identidades de su instancia de IoT Hub. No se puede conectar un dispositivo a IoT Hub a menos que tenga una entrada en el registro de identidades. Para más información, consulte la sección sobre el registro de la identidad de la [guía para desarrolladores de IoT Hub][lnk-devguide-identity]. Cuando ejecuta esta aplicación de consola, se genera una clave y un identificador de dispositivo únicos con el que el dispositivo puede identificarse cuando envía al Centro de IoT mensajes de dispositivo a la nube.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a una nueva solución mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Llame **CreateDeviceIdentity** al proyecto e **IoTHubGetStarted** a la solución.
   
    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10]
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **CreateDeviceIdentity** y, a continuación, seleccione **Administrar paquetes NuGet**.
3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **microsoft.azure.devices**, seleccione **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-nuget-service-sdk] y sus dependencias.
   
    ![Ventana del Administrador de paquetes NuGet][11]
4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;
5. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub para el centro que creó en la sección anterior.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
6. Agregue el método siguiente a la clase **Program** :
   
        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }
   
    Este método crea una identidad de dispositivo con el identificador **myFirstDevice**. (Si el identificador de dispositivo ya existe en el Registro de identidad, el código simplemente recupera la información existente del dispositivo). A continuación, la aplicación muestra la clave principal de esa identidad. Esta clave se usará en la aplicación de dispositivo simulado para conectarse a IoT Hub.
7. Por último, agregue las líneas siguientes al método **Main** :
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();
8. Ejecute la aplicación y anote la clave del dispositivo.
   
    ![Clave de dispositivo generada por la aplicación][12]

> [!NOTE]
> El registro de identidades de IoT Hub solo almacena identidades de dispositivos para permitir el acceso seguro al centro de IoT. Almacena las claves y los identificadores de dispositivo para usarlos como credenciales de seguridad, y un indicador de habilitado o deshabilitado que permite deshabilitar el acceso a un dispositivo individual. Si la aplicación necesita almacenar otros metadatos específicos del dispositivo, debe usar un almacén específico de la aplicación. Consulte la [guía para desarrolladores de IoT Hub][lnk-devguide-identity] para más información.
> 
> 

## <a name="receive-device-to-cloud-messages"></a>Recepción de mensajes de dispositivo a nube
En esta sección, creará una aplicación de consola de .NET que lee los mensajes de dispositivo a nube desde un IoT Hub. El centro de IoT expone un punto de conexión compatible con [Azure Event Hubs][lnk-event-hubs-overview] para permitirle leer los mensajes del dispositivo a la nube. Para simplificar las cosas, este tutorial crea un lector básico que no es apto para una implementación de alta capacidad de procesamiento. El tutorial sobre [procesamiento de mensajes del dispositivo a la nube][lnk-process-d2c-tutorial] muestra cómo procesar mensajes del dispositivo a la nube a escala. Para más información sobre cómo procesar los mensajes de los Centros de eventos, consulte el tutorial [Introducción a Event Hubs][lnk-eventhubs-tutorial]. (Este tutorial es aplicable a los puntos de conexión compatibles con los centros de eventos de IoT Hub).

> [!NOTE]
> El punto de conexión compatible con Event Hubs para leer mensajes de dispositivo a la nube siempre usa el protocolo AMQP.
> 
> 

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **ReadDeviceToCloudMessages**.
   
    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10a]
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **ReadDeviceToCloudMessages** y luego haga clic en **Administrar paquetes NuGet**.
3. En la ventana** Administrador de paquetes NuGet**, busque **WindowsAzure.ServiceBus**, seleccione **Instalar** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia a [Azure Service Bus][lnk-servicebus-nuget], con todas sus dependencias. Este paquete permite que la aplicación para se conecte al punto de conexión compatible con el Centro de eventos en el Centro de IoT.
4. Agregue las siguientes instrucciones `using` al principio del archivo **Program.cs** :
   
        using Microsoft.ServiceBus.Messaging;
        using System.Threading;
5. Agregue los campos siguientes a la clase **Program** . Sustituya el valor de marcador de posición por la cadena de conexión de IoT Hub que creó en la sección "Creación de un IoT Hub".
   
        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;
6. Agregue el método siguiente a la clase **Program** :
   
        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;
   
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }
   
    Este método usa una instancia de **EventHubReceiver** para recibir mensajes de todas las particiones de recepción de dispositivo a nube del Centro de IoT. Observe cómo pasar un parámetro `DateTime.Now` al crear el objeto **EventHubReceiver** para que solo reciba los mensajes enviados después de iniciarse. Este filtro es útil en un entorno de prueba, porque puede ver el conjunto actual de mensajes. En un entorno de producción, el código debe asegurarse de que este procesa todos los mensajes. Para más información, consulte el [Tutorial: procesamiento de mensajes de dispositivo a la nube de IoT Hub][lnk-process-d2c-tutorial].
7. Por último, agregue las líneas siguientes al método **Main** :
   
        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);
   
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
   
        CancellationTokenSource cts = new CancellationTokenSource();
   
        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };
   
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado
En esta sección, creará una aplicación de consola de .NET que simula un dispositivo que envía mensajes de dispositivo a nube a un IoT Hub.

1. En Visual Studio, agregue un proyecto de escritorio clásico de Windows de Visual C# a la solución actual mediante la plantilla de proyecto **Aplicación de consola (.NET Framework)**. Asegúrese de que la versión de .NET Framework sea 4.5.1 o una posterior. Asigne al proyecto el nombre **SimulatedDevice**.
   
    ![Nuevo proyecto de escritorio clásico de Windows de Visual C#][10b]
2. En el Explorador de soluciones, haga clic con el botón derecho en el proyecto **SimulatedDevice** y, a continuación, seleccione **Administrar paquetes NuGet**.
3. En la ventana **Administrador de paquetes NuGet**, seleccione **Examinar**, busque **Microsoft.Azure.Devices.Client**, haga clic en **Instalar** para instalar el paquete **Microsoft.Azure.Devices** y acepte los términos de uso. Este procedimiento permite descargar, instalar y agregar una referencia al [paquete NuGet del SDK de dispositivo de IoT de Azure][lnk-device-nuget] y sus dependencias.
4. Agregue la siguiente instrucción `using` en la parte superior del archivo **Program.cs** :
   
        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;
5. Agregue los campos siguientes a la clase **Program** . Sustituya los valores de marcador de posición por el nombre de host de IoT Hub que recuperó en la sección "Creación de un IoT Hub" y la clave de dispositivo que recuperó en la sección "Creación de una identidad de dispositivo".
   
        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";
6. Agregue el método siguiente a la clase **Program** :
   
        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();
   
            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;
   
                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));
   
                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);
   
                Task.Delay(1000).Wait();
            }
        }
   
    Este método envía un nuevo mensaje de dispositivo a nube cada segundo. El mensaje contiene un objeto JSON serializado con el identificador de dispositivo y un número generado aleatoriamente para simular un sensor de velocidad del viento.
7. Por último, agregue las líneas siguientes al método **Main** :
   
        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey), TransportType.Mqtt);
   
        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();
   
   De forma predeterminada, el método **Create** crea una instancia de **DeviceClient** que usa el protocolo AMQP para comunicarse con IoT Hub. Para usar el protocolo MQTT o HTTP, use la invalidación del método **Create** que permite especificar el protocolo. Si usa el protocolo HTTPS, debe agregar también el paquete NuGet **Microsoft.AspNet.WebApi.Client** al proyecto para incluir el espacio de nombres **System.Net.Http.Formatting**.

Este tutorial le guiará por los pasos para crear una aplicación de dispositivo simulada de IoT Hub. También puede utilizar la extensión de Visual Studio [Connected Service for Azure IoT Hub][lnk-connected-service] (Servicio conectado para Azure IoT Hub) para agregar el código necesario a la aplicación del dispositivo.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo de MSDN [Transient Fault Handling][lnk-transient-faults] (Tratamiento de errores temporales).
> 
> 

## <a name="run-the-apps"></a>Ejecución de las aplicaciones
Ya está preparado para ejecutar las aplicaciones.

1. En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en la solución y seleccione **Establecer proyectos de inicio**. Seleccione **Proyectos de inicio múltiples** y seleccione **Iniciar** como acción para los proyectos **ReadDeviceToCloudMessages** y **SimulatedDevice**.
   
    ![Propiedades del proyecto de inicio][41]
2. Presione **F5** para iniciar la ejecución de ambas aplicaciones. La salida de la consola de la aplicación **SimulatedDevice** muestra los mensajes que envía la aplicación de dispositivo simulado al centro de IoT. La salida de la consola de la aplicación **ReadDeviceToCloudMessages** muestra los mensajes que recibe el Centro de IoT.
   
    ![Salida de la consola de aplicaciones][42]
3. El icono **Uso** de [Azure Portal][lnk-portal] muestra el número de mensajes enviados al centro de IoT:
   
    ![Icono Uso del Portal de Azure][43]

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, configuró un centro de IoT en Azure Portal y, después, creó una identidad de dispositivo en el registro de identidades del centro de IoT. Usó esta identidad de dispositivo para habilitar la aplicación del dispositivo simulado para enviar al centro de IoT los mensajes del dispositivo a la nube. También creó otra aplicación que muestra los mensajes recibidos por el centro de IoT. 

Para continuar la introducción a IoT Hub y explorar otros escenarios de IoT, consulte:

* [Conexión del dispositivo][lnk-connect-device]
* [Introducción a la administración de dispositivos][lnk-device-management]
* [Introducción al SDK de puerta de enlace de IoT][lnk-gateway-SDK]

Para aprender a ampliar su solución IoT y cómo procesar mensajes de dispositivo a la nube a escala, consulte [Tutorial: procesamiento de mensajes de dispositivo a la nube][lnk-process-d2c-tutorial].

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

