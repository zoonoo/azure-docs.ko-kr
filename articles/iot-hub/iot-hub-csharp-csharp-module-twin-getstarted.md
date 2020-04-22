---
title: Azure IoT Hub 모듈 ID & 모듈 트윈(.NET)
description: .NET용 IoT SDK를 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp
ms.openlocfilehash: 919d1e37e6066c78e83d58be4fe4667ec67e45ad
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733387"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>IoT Hub 모듈 ID 및 모듈 트윈(.NET) 시작하기

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 디바이스 ID 및 디바이스 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID 및 장치 쌍은 백 엔드 응용 프로그램이 장치를 구성하고 장치의 조건에 대한 가시성을 제공할 수 있도록 하는 반면 모듈 ID 및 모듈 쌍은 장치의 개별 구성 요소에 대해 이러한 기능을 제공합니다. 운영 체제 기반 장치 또는 펌웨어 장치와 같은 여러 구성 요소가 있는 지원 장치에서 모듈 ID 및 모듈 트윈을 사용하면 각 구성 요소에 대해 격리된 구성 및 조건을 사용할 수 있습니다.

이 자습서를 마치면 다음과 같은 두 개의 .NET 콘솔 앱이 생깁니다.

* **생성ID**. 이 응용 프로그램은 장치 및 모듈 클라이언트를 연결하는 장치 ID, 모듈 ID 및 관련 보안 키를 만듭니다.

* **업데이트ModuleTwin보고된 속성**. 이 앱은 업데이트된 모듈 쌍보고 속성을 IoT 허브로 보냅니다.

> [!NOTE]
> 디바이스와 솔루션 백 엔드에서 실행하기 위해 두 애플리케이션을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보는 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio.

* 활성 Azure 계정. 계정이 없는 경우 단 몇 분 만에 [무료 계정을](https://azure.microsoft.com/pricing/free-trial/) 만들 수 있습니다.

## <a name="create-a-hub"></a>허브 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT 허브 연결 문자열 받기

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET 디바이스 SDK를 사용하여 모듈 쌍 업데이트

이 섹션에서는 시뮬레이션된 디바이스에 모듈 쌍 reported 속성을 업데이트하는 .NET 콘솔 앱을 만듭니다.

시작하기 전에 모듈 연결 문자열을 가져옵니다. [Azure Portal](https://portal.azure.com/)에 로그인합니다. 허브로 이동하여 **IoT 장치를**선택합니다. **myFirstDevice**찾기 . **myFirstDevice를** 선택하여 열고 **myFirstModule을** 선택합니다. **모듈 ID 세부 정보에서**다음 절차에서 필요한 경우 연결 **문자열(기본 키)을** 복사합니다.

   ![Azure Portal 모듈 세부 정보](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. Visual Studio에서 새 프로젝트 **파일을** > 선택하여 솔루션에**새** > **프로젝트를**추가합니다. 새 프로젝트 만들기에서 **콘솔 앱(.NET 프레임워크)을**선택하고 다음 을 **선택합니다.**

1. 프로젝트 이름을 *UpdateModuleTwinReportedProperties*로 지정합니다. **솔루션의**경우 **솔루션에 추가를 선택합니다.** .NET Framework 버전이 4.6.1 이상인지 확인합니다.

    ![Visual Studio 프로젝트 만들기](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. 프로젝트를 작성하려면 **만들기를** 선택합니다.

1. Visual Studio에서 오픈 **도구** > **NuGet 패키지 관리자는** > **솔루션에 대한 NuGet 패키지를 관리합니다.** **찾아보기** 탭을 선택합니다.

1. **Microsoft.Azure.Devices.Client를**검색하고 선택한 다음 **설치를**선택합니다.

    ![Azure IoT Hub .NET 서비스 SDK 최신 버전 설치](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Program.cs 파일 `using` 의 맨 위에 **Program.cs** 다음 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 모듈 연결 문자열로 바꿉니다.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. **Program** 클래스에 **OnDesiredPropertyChanged** 메서드를 추가합니다.

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

1. **Main** 메서드에 다음 줄을 추가합니다.

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    이 코드 샘플에서는 AMQP 프로토콜을 사용하여 모듈 쌍을 검색하고 reported 속성을 업데이트하는 방법을 보여 줍니다. 공개 미리 보기에서는 모듈 쌍 작업에 대해서만 AMQP를 지원합니다.

1. 선택적으로 이러한 문을 **Main** 메서드에 추가하여 모듈에서 IoT Hub로 이벤트를 보낼 수 있습니다. 이러한 줄을 블록 `try catch` 아래에 놓습니다.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 수 있습니다.

1. Visual Studio에서 **솔루션 탐색기에서**솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트 설정을**선택합니다.

1. **공통 속성에서** **시작 프로젝트를 선택합니다.**

1. **여러 시작 프로젝트를**선택한 다음 **앱의** 작업으로 시작을 선택하고 변경 내용을 수락하도록 **확인합니다.**

1. **F5를** 눌러 앱을 시작합니다.

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md)

* [IoT Edge 시작](../iot-edge/tutorial-simulate-device-linux.md)
