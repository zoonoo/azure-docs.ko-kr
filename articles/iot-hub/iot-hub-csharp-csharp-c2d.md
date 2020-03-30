---
title: Azure IoT Hub(.NET)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: .NET용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 디바이스로 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: robinsh
ms.openlocfilehash: 7805b9b3f000b2bc2e45272ab9ff469d5711e581
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110204"
---
# <a name="send-messages-from-the-cloud-to-your-device-with-iot-hub-net"></a>IoT Hub(.NET)를 사용하여 클라우드에서 디바이스에 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [장치에서 IoT 허브로 원격 분석 보내기](quickstart-send-telemetry-dotnet.md) 퀵스타트는 IoT 허브를 만들고, IoT 허브에 장치 ID를 프로비전하고, 장치-클라우드 메시지를 보내는 장치 앱을 코딩하는 방법을 보여 주었습니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서는 [장치에서 IoT 허브로 원격 분석 보내기를](quickstart-send-telemetry-dotnet.md)기반으로 합니다. 다음 작업을 수행하는 방법을 보여 주며 다음과 같은 작업을 수행합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.

* 디바이스에서 클라우드-디바이스 메시지를 받습니다.

* 솔루션 백 엔드에서 IoT Hub에서 장치로 전송된 메시지에 대한 배달*승인(피드백)을*요청합니다.

클라우드-디바이스 메시지에 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)에서 찾아볼 수 있습니다.

이 자습서의 끝에서 두 개의 .NET 콘솔 앱을 실행 합니다.

* **시뮬레이션장치**. 이 앱은 IoT 허브에 연결되고 클라우드-디바이스 메시지를 수신합니다. 이 응용 프로그램은 [IoT 허브에 장치에서 원격 분석 보내기에서](quickstart-send-telemetry-dotnet.md)만든 응용 프로그램의 수정 된 버전입니다.

* **보내기클라우드토디바이스**. 이 앱은 IoT Hub를 통해 장치 앱에 클라우드-장치 메시지를 보낸 다음 배달 승인을 받습니다.

> [!NOTE]
> IoT Hub는 Azure IoT 장치 SDK를 통해 C, Java, 파이썬 및 자바 스크립트를 비롯한 많은 장치 플랫폼 및 언어에 대한 [SDK](iot-hub-devguide-sdks.md)지원을 제공합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [IoT Hub 개발자 가이드](iot-hub-devguide.md)를 참조하세요.
>

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio

* 활성 Azure 계정. 계정이 없는 경우 단 몇 분 만에 [무료 계정을](https://azure.microsoft.com/pricing/free-trial/) 만들 수 있습니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 장치 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="receive-messages-in-the-device-app"></a>디바이스 앱에서 메시지 수신

이 섹션에서는 원격 분석 보내기에서 만든 장치 앱을 [장치에서 IoT 허브로](quickstart-send-telemetry-dotnet.md) 수정하여 IoT 허브에서 클라우드-장치 메시지를 수신합니다.

1. Visual Studio에서 **SimulatedDevice** 프로젝트의 **Program** 클래스에 다음 메서드를 추가합니다.

   ```csharp
    private static async void ReceiveC2dAsync()
    {
        Console.WriteLine("\nReceiving cloud to device messages from service");
        while (true)
        {
            Message receivedMessage = await s_deviceClient.ReceiveAsync();
            if (receivedMessage == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received message: {0}", 
            Encoding.ASCII.GetString(receivedMessage.GetBytes()));
            Console.ResetColor();

            await s_deviceClient.CompleteAsync(receivedMessage);
        }
    }
   ```

1. **Main** 메서드에서 `Console.ReadLine()` 줄 바로 앞에 다음 메서드를 추가합니다.

   ```csharp
   ReceiveC2dAsync();
   ```

`ReceiveAsync` 메서드는 수신 메시지를 디바이스에서 받은 시간에 비동기적으로 반환합니다. 지정 가능한 시간 지정 기간 이후에 *null을* 반환합니다. 이 예제에서는 기본값인 1분이 사용됩니다. 앱에서 *null*을 수신하면 새 메시지를 계속 기다려야 합니다. 이 요구 사항은 `if (receivedMessage == null) continue` 줄 때문입니다.

`CompleteAsync()`에 대한 호출은 메시지가 정상적으로 처리되었음을 IoT Hub에 알립니다. 디바이스 큐에서 메시지를 안전하게 제거할 수 있습니다. 디바이스 앱의 메시지 처리를 완료하지 못하게 하는 문제가 발생하는 경우 IoT Hub에서 메시지를 다시 전달합니다. 장치 앱의 메시지 처리 논리는 동일한 메시지를 여러 번 수신하여 동일한 결과를 생성할 수 있도록 *idempotent여야*합니다.

애플리케이션이 메시지를 일시적으로 중단할 수도 있으며 이 경우 IoT hub는 나중에 사용하기 위해 큐에 메시지를 보관합니다. 또는 애플리케이션이 메시지를 거부할 수 있습니다. 이 경우 큐에서 메시지가 영구적으로 제거됩니다. 클라우드-디바이스 메시지 수명 주기에 대한 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)를 참조하세요.

   > [!NOTE]
   > MQTT 또는 AMQP 대신 HTTPS를 전송으로 사용하는 경우 `ReceiveAsync` 메서드가 즉시 반환됩니다. HTTPS에서 클라우드-디바이스 메시지에 대해 지원되는 패턴은 메시지를 가끔씩(25분에 한 번씩보다 적게) 확인하는 디바이스에 간헐적으로 연결됩니다. HTTPS 수신을 더 많이 실행하면 IoT Hub가 요청을 제한할 수 있습니다. MQTT, AMQP 및 HTTPS 지원과 IoT Hub 제한 간의 차이점에 대한 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)를 참조하세요.
   >

## <a name="get-the-iot-hub-connection-string"></a>IoT 허브 연결 문자열 받기

이 문서에서는 [장치에서 원격 분석](quickstart-send-telemetry-dotnet.md)보내기에서 만든 IoT 허브를 통해 클라우드-장치 메시지를 보내는 백 엔드 서비스를 만듭니다. 클라우드-장치 메시지를 보내려면 서비스에 서비스 **연결** 권한이 필요합니다. 기본적으로 모든 IoT 허브는 이 권한을 부여하는 **서비스라는** 공유 액세스 정책으로 만들어집니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이제 장치 앱에 클라우드-장치 메시지를 보내는 .NET 콘솔 앱을 작성합니다.

1. 현재 Visual Studio 솔루션에서 새**프로젝트** **파일** > 을**선택합니다.** >  **새 프로젝트 만들기에서** **콘솔 앱(.NET 프레임워크)을**선택한 다음 **다음**을 선택합니다.

1. 프로젝트 *SendCloudToDevice*의 이름을 지정합니다. **솔루션**에서 **솔루션에 추가를** 선택하고 .NET Framework의 최신 버전을 수락합니다. **만들기**를 선택하여 프로젝트를 만듭니다.

   ![비주얼 스튜디오에서 새 프로젝트 구성](./media/iot-hub-csharp-csharp-c2d/sendcloudtodevice-project-configure.png)

1. 솔루션 탐색기에서 새 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리를 선택합니다.**

1. **NuGet 패키지 관리에서** **찾아보기를**선택한 다음 **Microsoft.Azure.Devices를**검색하고 선택합니다. **설치를**선택합니다.

   이 단계는 [Azure IoT 서비스 SDK NuGet 패키지에](https://www.nuget.org/packages/Microsoft.Azure.Devices/)대한 참조를 다운로드, 설치 및 추가합니다.

1. Program.cs 파일 `using` 맨 위에 다음 **Program.cs** 문을 추가합니다.

   ``` csharp
   using Microsoft.Azure.Devices;
   ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 IoT 허브 연결 문자열 에서 이전에 복사한 [IoT 허브 연결 문자열로](#get-the-iot-hub-connection-string)바꿉꿉입니다.

   ``` csharp
   static ServiceClient serviceClient;
   static string connectionString = "{iot hub connection string}";
   ```

1. **프로그램** 클래스에 다음 메서드를 추가합니다. 장치에서 [IoT 허브로 원격 분석 보내기에서](quickstart-send-telemetry-dotnet.md)장치를 정의할 때 장치 이름을 사용한 것으로 설정합니다.

   ``` csharp
   private async static Task SendCloudToDeviceMessageAsync()
   {
        var commandMessage = new
         Message(Encoding.ASCII.GetBytes("Cloud to device message."));
        await serviceClient.SendAsync("myFirstDevice", commandMessage);
   }
   ```

   이 메서드는 새 클라우드-디바이스 메시지를 ID `myFirstDevice`를 사용하여 디바이스에 보냅니다. 이 매개 변수를 [장치에서 IoT hub로 원격 분석 보내기에](quickstart-send-telemetry-dotnet.md)사용된 매개 변수에서 수정한 경우에만 변경합니다.

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

   ``` csharp
   Console.WriteLine("Send Cloud-to-Device message\n");
   serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

   Console.WriteLine("Press any key to send a C2D message.");
   Console.ReadLine();
   SendCloudToDeviceMessageAsync().Wait();
   Console.ReadLine();
   ```

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정을**선택합니다.

1. **공통 속성** > **시작 프로젝트에서**여러 시작 **프로젝트를**선택한 다음 **ReadDeviceToCloudMessages,** **시뮬레이션 장치**및 **SendCloudToDevice에**대한 **시작** 작업을 선택합니다. **확인** 을 선택하여 변경 내용을 저장합니다.

1. **F5**키를 누릅니다. 세 애플리케이션이 모두 시작됩니다. **SendCloudToDevice** 창을 선택하고 **Enter** 키를 누릅니다. 디바이스 앱에서 수신하고 있는 메시지가 표시됩니다.

   ![앱 메시지 수신](./media/iot-hub-csharp-csharp-c2d/sendc2d1.png)

## <a name="receive-delivery-feedback"></a>배달 피드백 받기

각 클라우드-장치 메시지에 대해 IoT Hub에서 배달(또는 만료) 승인을 요청할 수 있습니다. 이 옵션을 사용하면 솔루션 백 엔드에서 다시 시도 또는 보정 논리를 쉽게 알릴 수 있습니다. 클라우드-디바이스 피드백에 대한 자세한 내용은 [IoT Hub를 통한 D2C 및 C2D](iot-hub-devguide-messaging.md)를 참조하세요.

이 섹션에서는 **SendCloudToDevice** 앱을 수정하여 피드백을 요청하고 IoT Hub로부터 수신합니다.

1. Visual Studio의 **SendCloudToDevice** 프로젝트의 **Program** 클래스에 다음 메서드를 추가합니다.

   ```csharp
   private async static void ReceiveFeedbackAsync()
   {
        var feedbackReceiver = serviceClient.GetFeedbackReceiver();

        Console.WriteLine("\nReceiving c2d feedback from service");
        while (true)
        {
            var feedbackBatch = await feedbackReceiver.ReceiveAsync();
            if (feedbackBatch == null) continue;

            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Received feedback: {0}",
              string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
            Console.ResetColor();

            await feedbackReceiver.CompleteAsync(feedbackBatch);
        }
    }
    ```

    수신 패턴은 디바이스 앱으로부터 클라우드-디바이스 메시지를 받는 데 사용되는 것과 동일합니다.

1. **Main** 메서드에서 바로 다음에 `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)`다음 줄을 추가합니다.

   ``` csharp
   ReceiveFeedbackAsync();
   ```

1. 클라우드-장치 메시지 배달에 대한 피드백을 요청하려면 **SendCloudToDeviceMessageAsync** 메서드에서 속성을 지정해야 합니다. 줄 바로 다음에 다음 `var commandMessage = new Message(...);` 줄을 추가합니다.

   ``` csharp
   commandMessage.Ack = DeliveryAcknowledgement.Full;
   ```

1. **F5** 키를 눌러 앱을 실행합니다. 세 애플리케이션이 모두 시작됩니다. **SendCloudToDevice** 창을 선택하고 **Enter** 키를 누릅니다. 장치 앱에서 메시지를 수신하는 것이 확인됩니다. 몇 초 후에 **SendCloudToDevice** 애플리케이션에서 피드백 메시지를 수신하는지 확인해야 합니다.

   ![앱 메시지 수신](./media/iot-hub-csharp-csharp-c2d/sendc2d2.png)

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서 [Transient 오류 처리에서](/azure/architecture/best-practices/transient-faults)제안된 대로 지수 백오프와 같은 재시도 정책을 구현해야 합니다.
>

## <a name="next-steps"></a>다음 단계

이 방법에서는 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하는 완전한 엔드투엔드 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기](https://docs.microsoft.com/azure/iot-suite/)를 참조하세요.

IoT Hub를 사용하여 솔루션 개발에 대해 자세히 알아보려면 [IoT Hub 개발자 가이드를](iot-hub-devguide.md)참조하십시오.
