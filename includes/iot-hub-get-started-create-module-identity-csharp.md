---
title: 포함 파일
description: 포함 파일
services: iot-hub
author: chrissie926
ms.service: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: menchi
ms.custom: include file
ms.openlocfilehash: a5c1ddd085ae65b9920d73f50f993f4646785a69
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "68883798"
---
## <a name="create-a-module-identity"></a>모듈 ID 만들기

이 섹션에서는 허브의 ID 레지스트리에서 디바이스 ID 및 모듈 ID를 만드는 .NET 콘솔 앱을 만듭니다. ID 레지스트리에 항목이 없는 경우 디바이스 또는 모듈을 허브에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드의 ID 레지스트리 섹션](../articles/iot-hub/iot-hub-devguide-identity-registry.md)을 참조하세요.

이 콘솔 앱을 실행하면 디바이스 및 모듈 둘 다의 고유한 ID 및 키가 생성됩니다. 디바이스 및 모듈은 IoT Hub에 디바이스-클라우드 메시지를 보낼 때 이러한 값을 사용하여 자신을 식별합니다. ID는 대/소문자를 구분합니다.

1. Visual Studio를 열고 **새 프로젝트 만들기** 를 선택합니다.

1. **새 프로젝트 만들기** 에서 C#에 대해 **콘솔 앱(.NET Framework)** 을 선택합니다.

1. **다음** 을 선택하여 **새 프로젝트 구성** 을 엽니다. 프로젝트 이름을 *CreateIdentitie* 로, 솔루션 이름을 *IoTHubGetStarted* 로 지정합니다. .NET Framework 버전이 4.6.1 이상인지 확인합니다.

    ![Visual Studio 솔루션에 대한 이름 및 프레임워크 입력](./media/iot-hub-get-started-create-module-identity-csharp/configure-createidentities-project.png)

1. Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **솔루션용 NuGet 패키지 관리** 를 엽니다. **찾아보기** 탭을 선택합니다.

1. **Microsoft.Azure.Devices** 를 검색합니다. 이를 선택한 후 **설치** 를 선택합니다.

    ![Azure IoT Hub .NET 서비스 SDK 현재 버전 설치](./media/iot-hub-get-started-create-module-identity-csharp/install-service-sdk.png)

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Common.Exceptions;
   ```

1. **Program** 클래스에 다음 필드를 추가합니다. 자리 표시자 값을 이전 섹션에서 만든 허브의 IoT Hub 연결 문자열로 대체합니다.

   ```csharp
   const string connectionString = "<replace_with_iothub_connection_string>";
   const string deviceID = "myFirstDevice";
   const string moduleID = "myFirstModule";
   ```

1. **Main** 클래스에 다음 코드를 추가합니다.

   ```csharp
   static void Main(string[] args)
   {
       AddDeviceAsync().Wait();
       AddModuleAsync().Wait();
   }
   ```

1. **Program** 클래스에 다음 메서드를 추가합니다.

    ```csharp
    private static async Task AddDeviceAsync()
    {
       RegistryManager registryManager = 
         RegistryManager.CreateFromConnectionString(connectionString);
       Device device;

       try
       {
           device = await registryManager.AddDeviceAsync(new Device(deviceID));
       }
       catch (DeviceAlreadyExistsException)
        {
            device = await registryManager.GetDeviceAsync(deviceID);
        }

        Console.WriteLine("Generated device key: {0}", 
          device.Authentication.SymmetricKey.PrimaryKey);
    }

    private static async Task AddModuleAsync()
    {
        RegistryManager registryManager = 
          RegistryManager.CreateFromConnectionString(connectionString);
        Module module;

        try
        {
            module = 
              await registryManager.AddModuleAsync(new Module(deviceID, moduleID));
        }
        catch (ModuleAlreadyExistsException)
        {
            module = await registryManager.GetModuleAsync(deviceID, moduleID);
        }

        Console.WriteLine("Generated module key: {0}", module.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    `AddDeviceAsync` 메서드는 ID **myFirstDevice** 로 디바이스 ID를 만듭니다. ID 레지스트리에 해당 디바이스 ID가 이미 있는 경우 코드는 기존 디바이스 정보만 검색합니다. 그러면 앱에서 해당 ID에 대한 기본 키를 표시합니다. 이 키를 시뮬레이션된 디바이스 앱에서 사용하여 허브에 연결합니다.

    `AddModuleAsync` 메서드는 **myFirstDevice** 디바이스에서 ID가 **myFirstModule** 인 모듈 ID를 만듭니다. ID 레지스트리에 해당 모듈 ID가 이미 있는 경우 코드는 기존 모듈 정보만 검색합니다. 그러면 앱에서 해당 ID에 대한 기본 키를 표시합니다. 이 키를 시뮬레이션된 모듈 앱에서 사용하여 허브에 연결합니다.

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. 이 앱을 실행하고 디바이스 키 및 모듈 키를 기록합니다.

> [!NOTE]
> IoT Hub ID 레지스트리는 디바이스 및 모듈 ID만 저장하여 허브에 보안 액세스를 사용합니다. ID 레지스트리는 보안 자격 증명으로 사용할 디바이스 ID 및 키를 저장합니다. 또한 ID 레지스트리는 각 디바이스에 대한 액세스를 사용하지 않도록 설정하는 데 사용할 수 있는 해당 디바이스에 대한 enabled/disabled 플래그를 저장합니다. 앱이 다른 디바이스별 메타데이터를 저장해야 할 경우 애플리케이션별 스토리지를 사용해야 합니다. 모듈 ID에 대한 enabled/disabled 플래그는 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드](../articles/iot-hub/iot-hub-devguide-identity-registry.md)를 참조하세요.
