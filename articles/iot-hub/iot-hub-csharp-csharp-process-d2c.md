---
title: "경로를 사용하여 Azure IoT Hub 장치-클라우드 메시지 처리(.Net) | Microsoft Docs"
description: "다른 백 엔드 서비스에 메시지를 발송하기 위해 경로 규칙 및 사용자 지정 끝점을 사용하여 IoT Hub 장치-클라우드 메시지를 처리하는 방법을 설명합니다."
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 5177bac9-722f-47ef-8a14-b201142ba4bc
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: ff0b234f27e2d5068cc0dcdc73e32e60f8622633
ms.lasthandoff: 03/06/2017


---
# <a name="process-iot-hub-device-to-cloud-messages-using-routes-net"></a>경로를 사용하여 IoT Hub 장치-클라우드 메시지 처리(.Net)

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>소개
Azure IoT Hub는 수백만의 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 다른 자습서([IoT Hub 시작] 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기][lnk-c2d])에서는 IoT Hub의 기본 장치-클라우드 및 클라우드-장치 메시징 기능을 사용하는 방법을 보여 줍니다.

이 자습서는 [IoT Hub 시작] 자습서를 기반으로 하며, 라우팅 규칙을 사용하여 손쉬운 구성 기반 방식으로 장치-클라우드 메시지를 발송하는 방법을 보여 줍니다. 추가 처리를 위해 솔루션 백 엔드의 즉각적인 작업을 요구하는 메시지를 격리하는 방법을 보여 줍니다. 예를 들어 장치는 CRM 시스템으로의 티켓 삽입을 트리거하는 경보 메시지를 보낼 수 있습니다. 이와 반대로 데이터 요소 메시지는 단순히 분석 엔진으로 전달됩니다. 예를 들어 나중에 분석을 위해 저장해야 하는 장치의 온도 원격 분석이 데이터 요소 메시지에 해당합니다.

이 자습서의 끝 부분에서 다음의 세 가지 .NET 콘솔 앱을 실행합니다.

* **SimulatedDevice** - [IoT Hub 시작] 자습서에서 만든 수정된 버전의 앱이며, 매초 데이터 요소 장치-클라우드 메시지를 보내고 10초마다 대화형 장치-클라우드 메시지를 보냅니다. 이 앱에서는 IoT Hub와 통신하는 데 AMQP 프로토콜을 사용합니다.
* **ReadDeviceToCloudMessages** - 시뮬레이션된 장치 앱에서 보낸 중요하지 않은 원격 분석을 표시합니다.
* **ReadCriticalQueue** - IoT Hub에 연결된 Service Bus 큐에서 시뮬레이션된 장치 앱이 보낸 중요한 메시지를 큐에서 제거합니다.

> [!NOTE]
> IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 JavaScript 포함)에 SDK를 지원합니다. 물리적 장치를 사용하여 이 자습서의 시뮬레이션된 장치를 바꾸는 방법 및 장치를 IoT Hub에 연결하는 방법에 대해 알아보려면 [Azure IoT 개발자 센터]를 참조하세요.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Visual Studio 2015 또는 Visual Studio 2017.
* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/free/) 을 만들 수 있습니다.

[Azure Storage] 및 [Azure Service Bus]에 대한 기본 지식이 있어야 합니다.

## <a name="send-interactive-messages-from-a-simulated-device-app"></a>시뮬레이션된 장치 앱에서 대화형 메시지 보내기
이 섹션에서는 [IoT Hub 시작] 자습서에서 만든 시뮬레이션된 장치 앱을 수정하여 즉시 처리해야 하는 메시지를 가끔씩 보낼 수 있습니다.

Visual Studio의 **SimulatedDevice** 프로젝트에서 `SendDeviceToCloudMessagesAsync` 메서드를 다음 코드로 바꿉니다.

```
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
            string levelValue;

            if (rand.NextDouble() > 0.7)
            {
                messageString = "This is a critical message";
                levelValue = "critical";
            }
            else
            {
                levelValue = "normal";
            }
            
            var message = new Message(Encoding.ASCII.GetBytes(messageString));
            message.Properties.Add("level", levelValue);
            
            await deviceClient.SendEventAsync(message);
            Console.WriteLine("{0} > Sent message: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
        }
    }
```

이 메서드는 장치에서 보낸 메시지에 `"level": "critical"` 속성을 임의로 추가합니다. 그러면 솔루션 백 엔드에 의한 즉각적인 작업을 요구하는 메시지를 시뮬레이션합니다. 장치 앱에서 메시지 본문 대신 메시지 속성에 이 정보를 전달하므로 IoT Hub에서 메시지를 적절한 메시지 대상으로 라우팅할 수 있습니다.

> [!NOTE]
> 메시지 속성을 사용하면 여기서 보여 주는 실행 부하 과다 경로(hot path) 예제 외에도 실행 부하 과소 경로(cold path) 처리를 포함하여 다양한 시나리오의 메시지를 라우팅할 수 있습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>IoT Hub에 큐 추가 및 메시지 라우팅
이 섹션에서는 다음을 수행합니다.

* Service Bus 큐를 만듭니다.
* IoT Hub에 연결합니다.
* 메시지의 속성 존재 여부를 기반으로 큐에 메시지를 보내도록 IoT Hub를 구성합니다.

Service Bus 큐에서 메시지를 처리하는 방법에 대한 자세한 내용은 [큐 시작][Service Bus queue]을 참조하세요.

1. [큐 시작][Service Bus queue]에서 설명한 대로 Service Bus 큐를 만듭니다. 큐는 IoT Hub와 동일한 구독 및 지역에 있어야 합니다. 네임스페이스 및 큐 이름을 적어둡니다.

2. Azure Portal에서 IoT Hub를 열고 **끝점**을 클릭합니다.
    
    ![IoT Hub의 끝점][30]

3. **끝점** 블레이드 위쪽에서 **추가**를 클릭하여 IoT Hub에 큐를 추가합니다. 끝점 이름을 **CriticalQueue**로 지정하고 드롭다운을 사용하여 **Service Bus 큐**, 큐가 있는 Service Bus 네임스페이스 및 큐 이름을 선택합니다. 완료되면 아래쪽의 **저장** 을 클릭합니다.
    
    ![끝점 추가][31]
    
4. 이제 IoT Hub에서 **경로**를 클릭합니다. 블레이드 위쪽에서 **추가**를 클릭하여 방금 추가한 큐로 메시지를 라우팅하는 라우팅 규칙을 만듭니다. 데이터 원본으로 **DeviceTelemetry**를 선택합니다. 조건으로 `level="critical"`을 입력하고 방금 사용자 지정 끝점으로 추가한 큐를 경로 규칙 끝점으로 선택합니다. 완료되면 아래쪽의 **저장** 을 클릭합니다.
    
    ![경로 추가][32]
    
    대체(fallback) 경로가 **ON**으로 설정되어 있는지 확인합니다. 이 값은 IoT Hub에 대한 기본 구성입니다.
    
    ![대체(fallback) 경로][33]

## <a name="read-from-the-queue-endpoint"></a>큐 끝점에서 읽기
이 섹션에서는 큐 끝점에서 메시지를 읽습니다.

1. Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 바탕화면 프로젝트를 현재 솔루션에 추가합니다. **ReadCriticalQueue** 프로젝트의 이름을 지정합니다.

2. [솔루션 탐색기]에서 **ReadCriticalQueue** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다. 이 작업을 수행하면 **Nuget 패키지 관리자** 창이 표시됩니다.

3. **WindowsAzure.ServiceBus**를 검색하여 **설치**를 클릭하고 사용 약관에 동의합니다. 이 작업을 수행하면 모든 종속성과 함께 Azure Service Bus에 대한 참조를 다운로드, 설치 및 추가합니다.

4. **Program.cs** 파일의 맨 위에 다음 **using** 문을 추가합니다.
   
    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다. 연결 문자열을 큐에 대한 **수신 대기** 권한으로 바꿉니다.
   
    ```
    Console.WriteLine("Receive critical messages. Ctrl-C to exit.\n");
    var connectionString = "{service bus listen string}";
    var queueName = "{queue name}";
    
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);

    client.OnMessage(message =>
        {
            Stream stream = message.GetBody<Stream>();
            StreamReader reader = new StreamReader(stream, Encoding.ASCII);
            string s = reader.ReadToEnd();
            Console.WriteLine(String.Format("Message body: {0}", s));
        });
        
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>응용 프로그램 실행
이제 응용 프로그램을 실행할 준비가 되었습니다.

1. 솔루션 탐색기의 Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **ReadDeviceToCloudMessages**, **SimulatedDevice** 및 **ReadCriticalQueue** 프로젝트에 대한 작업으로 **시작**을 선택합니다.
2. **F5** 키를 눌러 세 가지 콘솔 앱을 시작합니다. **ReadDeviceToCloudMessages** 앱에는 **SimulatedDevice** 응용 프로그램에서 보낸 중요하지 않은 메시지만 있고, **ReadCriticalQueue** 앱에는 중요한 메시지만 있습니다.
   
   ![3개의 콘솔 앱][50]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 IoT Hub의 메시지 라우팅 기능을 사용하여 장치-클라우드 메시지를 안정적으로 전달하는 방법을 살펴보았습니다.

[IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법][lnk-c2d]에서는 솔루션 백 엔드에서 장치로 메시지를 보내는 방법을 보여줍니다.

IoT Hub를 사용하는 전체 종단 간 솔루션의 예를 보려면 [Azure IoT Suite][lnk-suite]를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드]를 참조하세요.

IoT Hub의 메시지 라우팅에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기][lnk-devguide-messaging]를 참조하세요.

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/route-creation.png
[33]: ./media/iot-hub-csharp-csharp-process-d2c/fallback-route.png

<!-- Links -->

[Azure blob storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[IoT Hub developer guide - Device to cloud]: iot-hub-devguide-messaging.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[IoT Hub 시작]: iot-hub-csharp-csharp-getstarted.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[Azure IoT 개발자 센터]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Get Started with Event Hubs]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Event Hubs Programming Guide]: ../event-hubs/event-hubs-programming-guide.md
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Build multi-tier applications with Service Bus]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

