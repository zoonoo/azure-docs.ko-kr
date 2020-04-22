---
title: Azure IoT Hub 디바이스 쌍 시작(.NET/.NET) | Microsoft Docs
description: Azure IoT Hub 디바이스 쌍을 사용하여 태그를 추가한 다음, IoT Hub 쿼리를 사용하는 방법입니다. .NET용 Azure IoT 디바이스 SDK를 사용하여 시뮬레이트된 디바이스 앱을 구현하고 .NET용 Azure IoT 서비스 SDK를 사용하여 태그를 추가하고 IoT Hub 쿼리를 실행하는 서비스 앱을 구현합니다.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733126"
---
# <a name="get-started-with-device-twins-net"></a>장치 쌍으로 시작하기(.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

이 자습서에서는 다음과 같은 .NET 콘솔 앱을 만듭니다.

* **생성장치 ID**. 이 응용 프로그램은 시뮬레이션 된 장치 응용 프로그램을 연결하는 장치 ID 및 관련 보안 키를 만듭니다.

* **AddTagsAnd쿼리**. 이 백 엔드 응용 프로그램은 태그 및 쿼리 장치 쌍둥이를 추가합니다.

* **보고서 연결**. 이 장치 앱은 이전에 만든 장치 ID로 IoT 허브에 연결하는 장치를 시뮬레이션하고 연결 상태를 보고합니다.

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) 문서는 디바이스 및 백 엔드 앱을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 대한 정보를 제공합니다.
>

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio.

* 활성 Azure 계정. 계정이 없는 경우 단 몇 분 만에 [무료 계정을](https://azure.microsoft.com/pricing/free-trial/) 만들 수 있습니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 장치 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>IoT Hub에서 새 디바이스 등록

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>IoT 허브 연결 문자열 받기

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>서비스 응용 프로그램 만들기

이 섹션에서는 **myDeviceId**와 연결된 장치 쌍에 위치 메타데이터를 추가하는 C#을 사용하여 .NET 콘솔 앱을 만듭니다. 그런 다음, IoT Hub에 저장된 디바이스 쌍을 쿼리하여 미국에 있는 디바이스를 선택한 다음 셀룰러 연결을 보고하는 디바이스를 선택합니다.

1. Visual Studio에서 **새 프로젝트 만들기**를 선택합니다. **새 프로젝트 만들기에서** **콘솔 앱(.NET 프레임워크)을**선택한 다음 **다음**을 선택합니다.

1. 새 프로젝트 구성에서 프로젝트 **AddTagsAndQuery의**이름을 **지정합니다.**

    ![AddTagsAndQuery 프로젝트 구성](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. 솔루션 탐색기에서 **AddTagsAndQuery** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리를 선택합니다.**

1. **찾아보기를** 선택하고 **검색하고 Microsoft.Azure.Devices**를 선택합니다. **설치**를 선택합니다.

    ![NuGet 패키지 관리자 창](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   이 단계에서는 [Azure IoT 서비스 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet 패키지 및 종속 항목에 참조를 다운로드, 설치 및 추가합니다.

1. Program.cs 파일 `using` 의 맨 위에 **Program.cs** 다음 문을 추가합니다.

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. IoT 허브 연결 문자열 Get에서 복사한 `{iot hub connection string}` [IoT Hub 연결 문자열로](#get-the-iot-hub-connection-string)바꿉입니다.

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. 다음 메서드를 **Program** 클래스에 추가합니다.

    ```csharp  
    public static async Task AddTagsAndQuery()
    {
        var twin = await registryManager.GetTwinAsync("myDeviceId");
        var patch =
            @"{
                tags: {
                    location: {
                        region: 'US',
                        plant: 'Redmond43'
                    }
                }
            }";
        await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```

    **RegistryManager** 클래스는 서비스의 디바이스 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 이전 코드에서는 **registryManager** 개체를 초기화한 다음, **myDeviceId**에 대한 디바이스 쌍을 검색하고, 마지막으로 원하는 위치 정보로 태그를 업데이트합니다.

    업데이트한 후 두 개의 쿼리를 실행합니다. 첫 번째는 **Redmond43** 공장에 위치한 디바이스의 디바이스 쌍만을 선택하고, 두 번째는 또한 셀룰러 네트워크를 통해서 연결된 디바이스만을 선택하기 위해 쿼리를 구체화합니다.

    이전 코드는 **쿼리** 개체를 만들 때 반환되는 최대 문서 수를 지정합니다. **query** 개체에는 모든 결과를 검색하기 위해 여러 번 **GetNextAsTwinAsync** 메서드를 호출하는 데 사용할 수 있는 **HasMoreResults** 부울 속성이 들어 있습니다. **GetNextAsJson이라는** 메서드는 장치 쌍이 아닌 결과(예: 집계 쿼리의 결과)에 사용할 수 있습니다.

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. **AddTagsAndQuery** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그**를 선택한 후 **새 인스턴스 시작**을 선택하여 이 애플리케이션을 실행합니다. **Redmond43**에 위치한 모든 디바이스를 요청하는 쿼리에 대한 결과로는 하나의 디바이스를 보고 셀룰러 네트워크를 사용하는 디바이스에 대해서는 결과를 제한하는 쿼리에 대한 결과로는 아무 디바이스도 볼 수 없어야 합니다.

    ![창에서 쿼리 결과](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

다음 섹션에서는 연결 정보를 보고하고 이전 섹션의 쿼리 결과를 변경하는 디바이스 앱을 만듭니다.

## <a name="create-the-device-app"></a>디바이스 앱 만들기

이 섹션에서는 **myDeviceId**로 허브에 연결하는 .NET 콘솔 앱을 만든 다음 셀룰러 네트워크를 사용하여 연결되는 정보에 포함된 reported 속성을 업데이트합니다.

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다. **새 프로젝트 만들기에서** **콘솔 앱(.NET 프레임워크)을**선택한 다음 **다음**을 선택합니다.

1. 새 프로젝트 구성에서 프로젝트 **보고서 연결성의**이름을 **지정합니다.** **솔루션의**경우 **에 추가를**선택한 다음 **을 선택합니다.**

1. 솔루션 탐색기에서 보고서 **연결** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리를 선택합니다.**

1. **찾아보기를** 선택하고 **검색하고 Microsoft.Azure.Devices.Client**을 선택합니다. **설치**를 선택합니다.

   이 단계는 [Azure IoT 장치 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet 패키지 및 해당 종속성에 대한 참조를 다운로드, 설치 및 추가합니다.

1. Program.cs 파일 `using` 의 맨 위에 **Program.cs** 다음 문을 추가합니다.

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. **Program** 클래스에 다음 필드를 추가합니다. `{device connection string}` [IoT 허브에서 새 장치 등록에서](#register-a-new-device-in-the-iot-hub)언급한 장치 연결 문자열로 바꿉습니다.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. 다음 메서드를 **Program** 클래스에 추가합니다.

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    **Client** 개체는 서비스의 디바이스 쌍을 조작하는 데 필요한 모든 메서드를 표시합니다. 위에 표시된 코드는 **클라이언트** 개체를 초기화한 다음 **myDeviceId**에 대한 장치 쌍입니다.

1. 다음 메서드를 **Program** 클래스에 추가합니다.

    ```csharp  
    public static async void ReportConnectivity()
    {
        try
        {
            Console.WriteLine("Sending connectivity data as reported property");

            TwinCollection reportedProperties, connectivity;
            reportedProperties = new TwinCollection();
            connectivity = new TwinCollection();
            connectivity["type"] = "cellular";
            reportedProperties["connectivity"] = connectivity;
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

   위의 코드는 **myDeviceId의** 보고된 속성을 연결 정보로 업데이트합니다.

1. 마지막으로 **Main** 메서드에 다음 줄을 추가합니다.

    ```csharp
    try
    {
        InitClient();
        ReportConnectivity();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정을**선택합니다.

1. **공통 속성** > **시작 프로젝트에서**여러 **시작 프로젝트를**선택합니다. **보고서 연결의**경우 **작업으로** **시작을** 선택합니다. **확인** 을 선택하여 변경 내용을 저장합니다.  

1. **보고서 연결** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **디버그를**선택한 다음 **새 인스턴스를 시작하여**이 앱을 실행합니다. 응용 프로그램 트윈 정보를 받고 보고 된 ***속성으로***연결을 보내는 표시 해야 합니다.

    ![디바이스 앱을 실행하여 연결 보고](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   장치가 연결 정보를 보고한 후 두 쿼리모두에 나타납니다.

1. **AddTagsAndQuery** 프로젝트를 마우스 오른쪽 단추로 클릭하고 새**인스턴스 시작** **디버그를** > 선택하여 쿼리를 다시 실행합니다. 이번에는 **myDeviceId가** 두 쿼리 결과에 나타납니다.

    ![디바이스 연결이 성공적으로 보고됨](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 디바이스 ID를 만들었습니다. 백 엔드 앱에서 tags로 디바이스 메타데이터를 추가하고, 디바이스 쌍에서 디바이스 연결 정보를 보고하는 시뮬레이션된 디바이스 앱을 작성했습니다. 또한 SQL과 유사한 IoT Hub 쿼리 언어를 사용하여 이 정보를 쿼리하는 방법도 살펴보았습니다.

다음 리소스에서 자세히 알아볼 수 있습니다.

* 장치에서 원격 분석을 보내는 방법을 알아보려면 [장치에서 IoT 허브로 원격 분석 보내기 자습서를 참조하세요.](quickstart-send-telemetry-dotnet.md)

* 장치 쌍의 원하는 속성을 사용하여 장치를 구성하는 방법을 알아보려면 [원하는 속성 사용 자습서를](tutorial-device-twins.md) 참조하세요.

* 사용자가 제어하는 앱에서 팬을 켜는 것과 같이 대화식으로 장치를 제어하는 방법에 대해 알아보려면 [직접 메서드 사용](quickstart-control-device-dotnet.md) 자습서를 참조하십시오.
