---
title: "Azure IoT Hub 시작(.NET) | Microsoft Docs"
description: ".NET용 IoT SDK를 사용하여 Azure IoT Hub에 장치-클라우드 메시지를 보내는 방법을 알아봅니다. 시뮬레이션된 장치 및 서비스 앱을 만들어서 장치를 등록하고 메시지를 전송하고 IoT Hub의 메시지를 읽습니다."
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
ms.date: 05/08/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 2734a90284432ee218efb4fea68684de4b069dd6
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="connect-your-device-to-your-iot-hub-using-net"></a>.NET을 사용하여 IoT Hub에 장치 연결
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

이 자습서의 끝 부분에서 다음의 세 가지 .NET 콘솔 앱이 만들어집니다.

* **CreateDeviceIdentity**는 장치 ID 및 시뮬레이션된 보안 키를 만들어 장치 앱에 연결합니다.
* **ReadDeviceToCloudMessages**는 장치 앱에서 보낸 원격 분석을 표시합니다.
* **SimulatedDevice**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 MQTT 프로토콜을 사용하여 매초마다 원격 분석 메시지를 보냅니다.

Github의 세 가지 앱이 포함된 Visual Studio 솔루션을 다운로드하거나 복제할 수 있습니다.

```bash
git clone https://github.com/Azure-Samples/iot-hub-dotnet-simulated-device-client-app.git
```

> [!NOTE]
> 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보는 [Azure IoT SDK][lnk-hub-sdks]를 참고하세요.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

이제 IoT Hub가 만들어졌고 이 자습서 나머지 부분을 완료하는 데 필요한 호스트 이름과 IoT Hub 연결 문자열을 갖게 되었습니다.

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-csharp](../../includes/iot-hub-get-started-create-device-identity-csharp.md)]

<a id="D2C_csharp"></a>
## <a name="receive-device-to-cloud-messages"></a>장치-클라우드 메시지 받기
이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 읽는 .NET 콘솔 앱을 만듭니다. IoT Hub가 [Azure Event Hubs][lnk-event-hubs-overview] 호환 끝점을 노출하여 장치-클라우드 메시지를 읽을 수 있습니다. 작업을 단순화하기 위해 이 자습서에서는 처리량이 높은 배포용이 아닌 기본적인 판독기를 만듭니다. 대량의 장치-클라우드 메시지를 처리하는 방법을 알아보려면 [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial] 자습서를 참조하세요. Event Hubs에서 메시지를 처리하는 방법에 대한 자세한 내용은 [Event Hubs 시작][lnk-eventhubs-tutorial] 자습서를 참조하세요. (이 자습서는 IoT Hub Event Hub와 호환되는 끝점에 적용할 수 있습니다.)

> [!NOTE]
> 장치-클라우드 메시지를 읽는 Event Hub와 호환 가능한 끝점은 항상 AMQP 프로토콜을 사용합니다.
> 
> 

1. Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 바탕화면 프로젝트를 현재 솔루션에 추가합니다. .NET Framework 버전이 4.5.1 이상인지 확인합니다. 프로젝트 **ReadDeviceToCloudMessages**의 이름을 지정합니다.
   
    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][10a]
2. 솔루션 탐색기에서 **ReadDeviceToCloudMessages** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **WindowsAzure.ServiceBus**를 검색하고 **설치**를 선택한 다음 사용 약관에 동의합니다. 이 프로시저에서는 모든 종속 항목과 함께 [Azure Service Bus][lnk-servicebus-nuget]에 대한 참조를 다운로드, 설치 및 추가합니다. 이 패키지를 사용하면 응용 프로그램을 IoT Hub의 이벤트 허브와 호환되는 끝점에 연결할 수 있습니다.
4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
   ```csharp
   using Microsoft.ServiceBus.Messaging;
   using System.Threading;
   ```
5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 "IoT Hub 만들기" 섹션에서 만든 허브의 IoT Hub 연결 문자열로 대체합니다.
   
   ```csharp
   static string connectionString = "{iothub connection string}";
   static string iotHubD2cEndpoint = "messages/events";
   static EventHubClient eventHubClient;
   ```
6. **Program** 클래스에 다음 메서드를 추가합니다.
   
   ```csharp
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
   ```
   
    이 메서드는 **EventHubReceiver** 인스턴스를 사용하여 모든 IoT Hub 장치-클라우드 수신 파티션의 메시지를 수신합니다. 시작된 후 보낸 메시지만 수신하도록 **EventHubReceiver** 개체를 만든 경우 `DateTime.Now` 매개 변수를 전달하는 방법을 참조하세요. 이 필터는 테스트 환경에서 현재 메시지 집합을 볼 수 있어 유용합니다. 프로덕션 환경에서는 코드가 모든 메시지를 처리하는지 확인해야 합니다. 자세한 내용은 [IoT Hub 장치-클라우드 메시지 처리 방법][lnk-process-d2c-tutorial] 자습서를 참조하세요.
7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
   ```csharp
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
   ```

## <a name="create-a-device-app"></a>장치 앱 만들기
이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 .NET 콘솔 앱을 작성합니다.

1. Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 바탕화면 프로젝트를 현재 솔루션에 추가합니다. .NET Framework 버전이 4.5.1 이상인지 확인합니다. 프로젝트 **SimulatedDevice**의 이름을 지정합니다.
   
    ![새 Visual C# Windows 클래식 데스크톱 프로젝트][10b]
2. 솔루션 탐색기에서 **SimulatedDevice** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.
3. **NuGet 패키지 관리자** 창에서 **찾아보기**를 선택하고 **Microsoft.Azure.Devices.Client**를 검색한 다음 **설치**를 선택하여 **Microsoft.Azure.Devices.Client** 패키지를 설치하고 사용 약관에 동의합니다. 이 프로시저에서는 [Azure IoT 장치 SDK NuGet 패키지][lnk-device-nuget] 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.
4. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.
   
   ```csharp
   using Microsoft.Azure.Devices.Client;
   using Newtonsoft.Json;
   ```
5. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 "IoT Hub 만들기" 섹션에서 가져온 IoT Hub 호스트 이름과 "장치 ID 만들기" 섹션에서 가져온 장치 키로 대체합니다.
   
   ```csharp
   static DeviceClient deviceClient;
   static string iotHubUri = "{iot hub hostname}";
   static string deviceKey = "{device key}";
   ```

6. **Program** 클래스에 다음 메서드를 추가합니다.
   
   ```csharp
    private static async void SendDeviceToCloudMessagesAsync()
    {
        double minTemperature = 20;
        double minHumidity = 60;
        int messageId = 1;
        Random rand = new Random();

        while (true)
        {
            double currentTemperature = minTemperature + rand.NextDouble() * 15;
            double currentHumidity = minHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
                messageId = messageId++,
                deviceId = "myFirstDevice",
                temperature = currentTemperature,
                humidity = currentHumidity
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("temperatureAlert", (currentTemperature > 30) ? "true" : "false");

            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
   ```
    이 메서드는 1초마다 새로운 장치-클라우드 메시지를 보냅니다. 메시지에는 장치 ID가 있는 JSON 직렬화된 개체와 온도 센서 및 습도 센서를 시뮬레이션하기 위해 임의로 생성된 숫자가 있습니다.
7. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.
   
   ```csharp
   Console.WriteLine("Simulated device\n");
   deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey), TransportType.Mqtt);

   SendDeviceToCloudMessagesAsync();
   Console.ReadLine();
   ```
   
   기본적으로 .NET Framework 앱에서 **Create** 메서드는 AMQP 프로토콜을 사용하여 IoT Hub(UWP 및 PCL 클라이언트는 기본적으로 HTTP 사용)와 통신하는 **DeviceClient** 인스턴스를 만듭니다. MQTT 또는 HTTP 프로토콜을 사용하려면 프로토콜을 지정할 수 있도록 해주는 **Create** 메서드의 재정의를 사용합니다. HTTP 프로토콜을 사용하려면 **Microsoft.AspNet.WebApi.Client** NuGet 패키지를 프로젝트에 추가하여 **System.Net.Http.Formatting** 네임스페이스를 포함해야 합니다.

이 자습서에서는 IoT Hub 장치 앱을 만드는 단계를 안내합니다. [Azure IoT Hub에 연결된 서비스][lnk-connected-service] Visual Studio 확장을 사용하여 장치 앱에 필요한 코드를 추가할 수 있습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
> 
> 

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. 솔루션 탐색기의 Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **ReadDeviceToCloudMessages** 및 **SimulatedDevice** 프로젝트 모두에 대한 작업으로 **시작**을 선택합니다.
   
    ![시작 프로젝트 속성][41]
2. **F5** 를 눌러 두 응용 프로그램 실행을 시작합니다. **SimulatedDevice** 앱의 콘솔 출력은 장치 앱에서 IoT Hub로 전송한 메시지를 보여줍니다. **ReadDeviceToCloudMessages** 앱의 콘솔 출력은 IoT Hub가 수신하는 메시지를 보여 줍니다.
   
    ![앱에서 콘솔 출력][42]
3. [Azure Portal][lnk-portal]의 **사용량** 타일에 IoT Hub로 전송된 메시지 수가 표시됩니다.
   
    ![Azure Portal 사용량 타일][43]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 이 장치 ID를 사용하여 장치-클라우드 메시지를 IoT Hub로 보내기 위해 장치 앱을 사용하도록 설정했습니다. IoT Hub에서 받은 메시지를 표시하는 앱도 만들었습니다. 

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [장치 연결][lnk-connect-device]
* [장치 관리 시작][lnk-device-management]
* [IoT Edge 시작][lnk-iot-edge]

IoT 솔루션을 확장하고 대량의 장치-클라우드 메시지를 처리하는 방법을 알아보려면 [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial] 자습서를 참조하세요.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10a]: ./media/iot-hub-csharp-csharp-getstarted/create-receive-csharp1.png
[10b]: ./media/iot-hub-csharp-csharp-getstarted/create-device-csharp1.png


<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: iot-hub-linux-iot-edge-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

