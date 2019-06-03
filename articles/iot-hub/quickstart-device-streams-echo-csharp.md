---
title: Azure IoT Hub 디바이스 스트림을 통해 C#에서 디바이스 앱과 통신(미리 보기) | Microsoft Docs
description: 이 빠른 시작에서는 IoT Hub를 통해 설정된 디바이스 스트림을 통해 통신하는 두 개의 샘플 C# 애플리케이션을 실행합니다.
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: rezas
ms.openlocfilehash: 8df57d3d36dcae851c9c0e23ea609e200a429605
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832882"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>빠른 시작: IoT Hub 디바이스 스트림을 통해 C#에서 디바이스 애플리케이션과 통신(미리 보기)

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Microsoft Azure IoT Hub는 현재 디바이스 스트림을 [미리 보기 기능](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)으로 지원합니다.

[IoT Hub 디바이스 스트림](./iot-hub-device-streams-overview.md)은 서비스 및 디바이스 애플리케이션이 안전하고 방화벽 친화적인 방식으로 통신할 수 있도록 합니다. 이 빠른 시작은 데이터를 보내고 받는 데(에코) 디바이스 스트림을 활용하는 두 개의 C# 프로그램을 포함합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

*  디바이스 스트림의 미리 보기는 현재 다음 지역에서 만든 IoT Hub에 대해서만 지원됩니다.

   *  **미국 중부**

   *  **미국 중부 EUAP**

이 빠른 시작에서 실행하는 두 개의 샘플 애플리케이션은 C#을 사용하여 작성되었습니다. 개발 컴퓨터에서 .NET Core SDK 2.1.0 이상이 필요합니다.

*  [.NET에서 여러 플랫폼에 대한 .NET Core SDK](https://www.microsoft.com/net/download/all)를 다운로드하세요.

다음 명령을 사용하여 개발 컴퓨터에서 C#의 현재 버전을 확인할 수 있습니다.

```
dotnet --version
```

*  다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IOT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service)별 명령을 추가합니다.

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

* https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip에서 샘플 C# 다운로드하고 ZIP 보관 파일을 추출합니다. 디바이스 및 서비스 쪽 모두에서 필요합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

[!INCLUDE [iot-hub-include-create-hub-device-streams](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>디바이스 등록

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyDevice**: 등록된 디바이스에 지정된 이름입니다. 표시된 것처럼 MyDevice를 사용합니다. 다른 장치 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용해야 하고, 샘플 애플리케이션에서 장치 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    다음 예제와 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

3. 또한 서비스 쪽 애플리케이션을 IoT Hub에 연결하고 디바이스 스트림을 설정하기 위해 IoT Hub에서 *서비스 연결 문자열*이 필요합니다. 다음 명령은 IoT Hub에 대한 이 값을 검색합니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub show-connection-string --policy-name service --name YourIoTHubName
    ```

    다음과 같이 표시되는 반환 값을 기록해 둡니다.

   `"HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"`

## <a name="communicate-between-device-and-service-via-device-streams"></a>디바이스 스트림을 통해 디바이스와 서비스 간의 통신

이 섹션에서는 디바이스 쪽 애플리케이션과 서비스 쪽 애플리케이션을 둘 다 실행하고 서로 통신합니다.

### <a name="run-the-service-side-application"></a>서비스 쪽 애플리케이션 실행

압축을 푼 프로젝트 폴더에서 `iot-hub/Quickstarts/device-streams-echo/service`로 이동합니다. 다음 정보가 필요합니다.

| 매개 변수 이름 | 매개 변수 값 |
|----------------|-----------------|
| `ServiceConnectionString` | IoT Hub의 서비스 연결 문자열을 입력합니다. |
| `DeviceId` | 예를 들어 MyDevice 같이 앞에서 만든 디바이스의 ID를 입력합니다. |

다음과 같이 코드를 컴파일하고 실행합니다.

```
cd ./iot-hub/Quickstarts/device-streams-echo/service/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<ServiceConnectionString>" "<MyDevice>"

# In Windows
dotnet run <ServiceConnectionString> <MyDevice>
```

> [!NOTE]
> 디바이스 쪽 애플리케이션에서 시간 내에 응답하지 않는 경우 시간 초과가 발생합니다.

### <a name="run-the-device-side-application"></a>디바이스 쪽 애플리케이션 실행

압축을 푼 프로젝트 폴더에서 `iot-hub/Quickstarts/device-streams-echo/device` 디렉터리로 이동합니다. 다음 정보가 필요합니다.

| 매개 변수 이름 | 매개 변수 값 |
|----------------|-----------------|
| `DeviceConnectionString` | IoT Hub의 디바이스 연결 문자열을 입력합니다. |

다음과 같이 코드를 컴파일하고 실행합니다.

```
cd ./iot-hub/Quickstarts/device-streams-echo/device/

# Build the application
dotnet build

# Run the application
# In Linux/MacOS
dotnet run "<DeviceConnectionString>"

# In Windows
dotnet run <DeviceConnectionString>
```

마지막 단계의 끝에서 서비스 쪽 프로그램은 디바이스에 대한 스트림을 시작하고 설정되면 스트림을 통해 문자열 버퍼를 서비스로 보냅니다. 이 샘플에서 서비스 쪽 프로그램은 디바이스에 동일한 데이터를 간단히 다시 에코하여 두 애플리케이션 간의 성공적인 양방향 통신을 보여줍니다. 아래 그림을 참조하세요.

디바이스 쪽 콘솔 출력:

![디바이스 쪽 콘솔 출력](./media/quickstart-device-streams-echo-csharp/device-console-output.png)

서비스 쪽 콘솔 출력: ![서비스 쪽 콘솔 출력](./media/quickstart-device-streams-echo-csharp/service-console-output.png )

스트림을 통해 전송되는 트래픽은 직접 전송되는 것이 아니라 IoT Hub를 통해 터널링됩니다. 제공되는 이점은 [디바이스 스트림 이점](./iot-hub-device-streams-overview.md#benefits)에 자세히 나와 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT Hub를 설정했고, 디바이스를 등록했으며, 디바이스 및 서비스 쪽에서 C# 애플리케이션 간 디바이스 스트림을 설정했고, 애플리케이션 간에 데이터를 주고 받는 데 스트림을 사용했습니다.

디바이스 스트림에 대해 자세히 알아보려면 아래 링크를 사용합니다.

> [!div class="nextstepaction"]
> [디바이스 스트림 개요](./iot-hub-device-streams-overview.md)
