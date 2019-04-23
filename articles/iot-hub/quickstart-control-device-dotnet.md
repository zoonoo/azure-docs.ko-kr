---
title: Azure IoT Hub 빠른 시작(.NET)에서 디바이스 제어 | Microsoft Docs
description: 이 빠른 시작에서 두 개의 샘플 C# 애플리케이션을 실행합니다. 하나의 애플리케이션은 허브에 연결된 장치를 원격으로 제어할 수 있는 백 엔드 애플리케이션입니다. 또 다른 애플리케이션은 원격으로 제어할 수 있는 허브에 연결된 장치를 시뮬레이션 합니다.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/22/2019
ms.openlocfilehash: fc219d9e3e5b365f341b2997804586e67275c1b7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59046515"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-net"></a>빠른 시작: IoT Hub에 연결된 디바이스 제어(.NET)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub는 IoT 디바이스에서 클라우드로 다량의 원격 분석 데이터를 수집하고 클라우드에서 디바이스를 관리할 수 있게 해주는 Azure 서비스입니다. 이 빠른 시작에서는 *직접 메서드*를 사용하여 IoT 허브에 연결된 시뮬레이션된 디바이스를 제어합니다. 직접 메서드를 사용하여 IoT 허브에 연결된 디바이스의 동작을 원격으로 변경할 수 있습니다.

빠른 시작은 두 가지 미리 작성된 .NET 애플리케이션을 사용합니다.

* 백 엔드 애플리케이션에서 호출된 직접 메소드에 응답하는 시뮬레이션된 장치 애플리케이션입니다. 직접 메서드 호출을 수신하기 위해 이 애플리케이션을 IoT 허브의 장치별 엔드포인트에 연결합니다.

* 시뮬레이션된 장치에서 직접 메서드를 호출하는 백 엔드 애플리케이션입니다. 장치에서 직접 메서드를 호출하려면 이 애플리케이션을 IoT 허브의 서비스 측 엔드포인트에 연결합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 빠른 시작에서 실행하는 두 개의 샘플 애플리케이션은 C#을 사용하여 작성되었습니다. 개발 컴퓨터에서 .NET Core SDK 2.1.0 이상이 필요합니다.

[.NET](https://www.microsoft.com/net/download/all)에서 여러 플랫폼에 대한 .NET Core SDK를 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 C#의 현재 버전을 확인할 수 있습니다.

```cmd/sh
dotnet --version
```

다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IOT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service)별 명령을 추가합니다.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

아직 그렇게 하지 않았다면 https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip에서 샘플 C# 프로젝트를 다운로드하고 ZIP 보관 파일을 추출합니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 [빠른 시작: 디바이스에서 IoT 허브로 원격 분석 보내기](quickstart-send-telemetry-dotnet.md)를 완료한 경우 이 단계를 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

이전 [빠른 시작: 디바이스에서 IoT 허브로 원격 분석 보내기](quickstart-send-telemetry-dotnet.md)를 완료한 경우 이 단계를 건너뛸 수 있습니다.

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

   **MyDotnetDevice**: 등록 중인 디바이스의 이름입니다. 표시된 것처럼 **MyDotnetDevice**를 사용하세요. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서 해당 이름을 사용해야 하고, 애플리케이션 예제에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyDotnetDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

   **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyDotnetDevice 
      --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

## <a name="retrieve-the-service-connection-string"></a>서비스 연결 문자열 검색

백 엔드 애플리케이션을 허브에 연결하여 메시지를 검색할 수 있게 하려면 IoT 허브 _서비스 연결 문자열_이 필요합니다. 다음 명령은 IoT Hub에 대한 서비스 연결 문자열을 검색합니다.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --output table
```

다음과 같은 서비스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={YourSharedAccessKey}`

이 값은 빠른 시작의 뒷부분에서 사용합니다. 서비스 연결 문자열은 디바이스 연결 문자열과는 다릅니다.  

## <a name="listen-for-direct-method-calls"></a>직접 메서드 호출 수신 대기

시뮬레이션된 장치 애플리케이션은 IoT 허브의 장치별 엔드포인트에 연결하고, 시뮬레이션된 원격 분석을 전송하고, 허브에서 직접 메서드 호출을 수신 대기합니다. 이 빠른 시작에서 허브의 직접 메서드 호출은 디바이스에 원격 분석을 보내는 간격을 변경하도록 지시합니다. 시뮬레이션된 디바이스는 직접 메서드를 실행한 후 승인을 다시 허브로 보냅니다.

1. 로컬 터미널 창에서 샘플 C# 프로젝트의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\simulated-device-2** 폴더로 이동합니다.

2. 원하는 텍스트 편집기에서 **SimulatedDevice.cs** 파일을 엽니다.

    `s_connectionString` 변수의 값을 이전에 적어둔 디바이스 연결 문자열로 바꿉니다. 그런 다음 변경 사항을 **SimulatedDevice.cs** 파일에 저장합니다.

3. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 장치 애플리케이션에 필요한 패키지를 설치합니다.

    ```cmd/sh
    dotnet restore
    ```

4. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 장치 애플리케이션을 빌드하고 실행합니다.

    ```cmd/sh
    dotnet run
    ```

    다음 스크린샷에서는 시뮬레이션된 장치 애플리케이션에서 IoT 허브에 원격 분석을 보낼 때의 출력을 보여 줍니다.

    ![시뮬레이션된 디바이스 실행](./media/quickstart-control-device-dotnet/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>직접 메서드 호출

백 엔드 애플리케이션은 IoT Hub의 서비스 측 엔드포인트에 연결합니다. 애플리케이션은 IoT 허브를 통해 장치에 직접 메서드 호출을 하고 승인을 수신 대기합니다. IoT Hub 백 엔드 애플리케이션은 일반적으로 클라우드에서 실행됩니다.

1. 또 다른 로컬 터미널 창에서 샘플 C# 프로젝트의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\back-end-application** 폴더로 이동합니다.

2. 원하는 텍스트 편집기에서 **BackEndApplication.cs** 파일을 엽니다.

    `s_connectionString` 변수의 값을 이전에 적어둔 서비스 연결 문자열로 바꿉니다. 그런 다음 변경 사항을 **BackEndApplication.cs** 파일에 저장합니다.

3. 로컬 터미널 창에서 다음 명령을 실행하여 백 엔드 애플리케이션에 필요한 라이브러리를 설치합니다.

    ```cmd/sh
    dotnet restore
    ```

4. 로컬 터미널 창에서 다음 명령을 실행하여 백 엔드 애플리케이션을 빌드하고 실행합니다.

    ```cmd/sh
    dotnet run
    ```

    다음 스크린샷에서는 애플리케이션에서 장치에 직접 메서드를 호출하고 승인을 받을 때의 출력을 보여 줍니다.

    ![백 엔드 애플리케이션 실행](./media/quickstart-control-device-dotnet/BackEndApplication.png)

    백 엔드 애플리케이션을 실행한 후 시뮬레이션된 장치를 실행하는 콘솔 창에 메시지가 표시되고 메시지를 보내는 속도가 변경됩니다.

    ![시뮬레이션된 클라이언트에서 변경](./media/quickstart-control-device-dotnet/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 백 엔드 애플리케이션에서 디바이스에 직접 메서드를 호출하고, 시뮬레이션된 디바이스 애플리케이션에서 직접 메서드 호출에 응답했습니다.

디바이스-클라우드 메시지를 클라우드의 다른 대상으로 라우팅하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> [자습서: 처리를 위해 다른 엔드포인트로 원격 분석 라우팅](tutorial-routing.md)