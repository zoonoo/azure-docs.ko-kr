---
title: Azure IoT Hub 모듈 ID 및 모듈 쌍 시작(포털 및 .NET) | Microsoft Docs
description: 포털 및 .NET을 사용하여 모듈 ID를 만들고 모듈 쌍을 업데이트하는 방법을 알아봅니다.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.openlocfilehash: 3b1872699b8b3ac72424f00cd74bb90b8b7be87f
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65873170"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>포털 및 .NET 디바이스를 사용하여 IoT Hub 모듈 ID 및 모듈 쌍 시작

> [!NOTE]
> [모듈 ID 및 모듈 쌍](iot-hub-devguide-module-twins.md)은 Azure IoT Hub 디바이스 ID 및 디바이스 쌍과 비슷하지만 더 자세한 세분성을 제공합니다. Azure IoT Hub 장치 ID와 장치 쌍은 백 엔드 애플리케이션에서 장치를 구성할 수 있도록 하고 장치 상태에 대한 가시성을 제공하지만, 모듈 ID와 모듈 쌍은 장치의 개별 구성 요소에 대해 이러한 기능을 제공합니다. 운영 체제 기반 디바이스 또는 펌웨어 디바이스와 같이 여러 구성 요소가 있는 가능한 디바이스에서 각 구성 요소에 대해 격리된 구성과 조건을 허용합니다.
>

이 자습서에서는 다음에 대해 알아봅니다.

1. 포털에서 모듈 ID를 만드는 방법

2. 장치에서 모듈 쌍.NET 장치 SDK 업데이트를 사용 하는 방법입니다.

> [!NOTE]
> 장치와 솔루션 백 엔드에서 실행하기 위해 두 애플리케이션을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보는 [Azure IoT SDK](iot-hub-devguide-sdks.md)를 참조하세요.
>

이 자습서를 완료하려면 다음이 필요합니다.

* 있습니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/pricing/free-trial/)을 만들 수 있습니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>IoT Hub에 대한 연결 문자열 검색

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT 허브에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>포털에서 모듈 ID 만들기

하나의 디바이스 ID 내에 최대 20개의 모듈 ID를 만들 수 있습니다. 위쪽의 **모듈 ID 추가** 단추를 클릭하여 **myFirstModule**이라는 첫 번째 모듈 ID를 만듭니다.

  ![디바이스 세부 정보](./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.png)

방금 만든 모듈 ID를 저장하고 클릭합니다. 모듈 ID 세부 정보를 볼 수 있습니다. 연결 문자열 - 기본 키를 저장합니다. 디바이스에서 모듈을 설정한 경우 다음 섹션에서 사용됩니다.

  ![디바이스 세부 정보](./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.png)

## <a name="update-the-module-twin-using-net-device-sdk"></a>.NET 디바이스 SDK를 사용하여 모듈 쌍 업데이트

IoT Hub에서 모듈 ID를 성공적으로 만들었습니다. 시뮬레이트된 디바이스에서 클라우드와 통신해 보겠습니다. 모듈 ID가 만들어지면 모듈 쌍이 IoT Hub에서 암시적으로 생성됩니다. 이 섹션에서는 시뮬레이션된 디바이스에 모듈 쌍 reported 속성을 업데이트하는 .NET 콘솔 앱을 만듭니다.

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

Visual Studio에서 **콘솔 앱(.NET Framework)** 프로젝트 템플릿을 사용하여 Visual C# Windows 클래식 데스크톱 프로젝트를 기존 솔루션에 추가합니다. .NET Framework 버전이 4.6.1 이상인지 확인합니다. 프로젝트 이름을 **UpdateModuleTwinReportedProperties**로 지정합니다.

  ![Visual Studio 프로젝트 만들기](./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.png)

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>최신 Azure IoT Hub .NET 디바이스 SDK 설치

모듈 ID 및 모듈 쌍은 공개 미리 보기로 제공됩니다. 만 IoT Hub 시험판 장치 Sdk에서에서 제공 됩니다. Visual Studio에서 도구 > Nuget 패키지 관리자 > 솔루션용 Nuget 패키지 관리를 차례로 엽니다. Microsoft.Azure.Devices.Client를 검색합니다. 있는지 확인 한 후에 시험판 확인란을 포함 합니다. 최신 버전을 선택하고 설치합니다. 이제 모든 모듈 기능에 액세스할 수 있습니다.

  ![Azure IoT Hub .NET 서비스 SDK V1.16.0-preview-005 설치](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

## <a name="get-your-module-connection-string"></a>모듈 연결 문자열 가져오기

에 로그인 [Azure portal](https://portal.azure.com/)합니다. IoT Hub로 이동하고 IoT 디바이스를 클릭합니다. myFirstDevice를 찾아서 열면 성공적으로 만들어진 myFirstModule이 표시됩니다. 모듈 연결 문자열을 복사합니다. 이는 다음 단계에서 필요합니다.

  ![Azure Portal 모듈 세부 정보](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.png)

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>UpdateModuleTwinReportedProperties 콘솔 앱 만들기

**Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

**Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 모듈 연결 문자열로 바꿉니다.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
private static ModuleClient Client = null;
```

**Program** 클래스에 **OnDesiredPropertyChanged** 메서드를 추가합니다.

```csharp
private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
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

마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

```csharp
static void Main(string[] args)
{
    Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

    try
    {
        Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
        Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

        Console.WriteLine("Retrieving twin");
        var twinTask = Client.GetTwinAsync();
        twinTask.Wait();
        var twin = twinTask.Result;
        Console.WriteLine(JsonConvert.SerializeObject(twin));

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
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

이 코드 샘플에서는 AMQP 프로토콜을 사용하여 모듈 쌍을 검색하고 reported 속성을 업데이트하는 방법을 보여 줍니다. 공개 미리 보기에서는 모듈 쌍 작업에 대해서만 AMQP를 지원합니다.

## <a name="run-the-apps"></a>앱 실행

이제 앱을 실행할 준비가 되었습니다. 솔루션 탐색기의 Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다. **여러 개의 시작 프로젝트**를 선택하고 **시작**을 콘솔 앱에 대한 작업으로 선택합니다. 그런 다음, F5 키를 눌러 두 앱을 모두 실행합니다.

## <a name="next-steps"></a>다음 단계

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [IoT Hub 모듈 id 및 모듈 쌍.NET 백업 및.NET 장치를 사용 하 여 시작](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [IoT Edge 시작](../iot-edge/tutorial-simulate-device-linux.md)