---
title: Azure IoT Hub 빠른 시작에서 디바이스 제어(Python) | Microsoft Docs
description: 이 빠른 시작에서는 두 개의 Python 애플리케이션 샘플을 실행합니다. 하나의 애플리케이션은 허브에 연결된 디바이스를 원격으로 제어할 수 있는 백 엔드 애플리케이션입니다. 또 다른 애플리케이션은 원격으로 제어할 수 있는 허브에 연결된 디바이스를 시뮬레이션 합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/21/2019
ms.openlocfilehash: b36e5d88c67a4aabf530aa8d945c17870e9c126b
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74892654"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-python"></a>빠른 시작: IoT Hub에 연결된 디바이스 제어(Python)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub는 클라우드에서 IoT 디바이스를 관리하고, 스토리지 또는 처리를 위해 클라우드에 다량의 디바이스 원격 분석 데이터를 수집할 수 있는 Azure 서비스입니다. 이 빠른 시작에서는 *직접 메서드*를 사용하여 IoT 허브에 연결된 시뮬레이션된 디바이스를 제어합니다. 직접 메서드를 사용하여 IoT 허브에 연결된 디바이스의 동작을 원격으로 변경할 수 있습니다.

빠른 시작에서는 미리 작성된 두 개의 Python 애플리케이션을 사용합니다.

* 백 엔드 애플리케이션에서 호출된 직접 메소드에 응답하는 시뮬레이션된 디바이스 애플리케이션입니다. 직접 메서드 호출을 수신하기 위해 이 애플리케이션을 IoT 허브의 디바이스별 엔드포인트에 연결합니다.

* 시뮬레이션된 디바이스에서 직접 메서드를 호출하는 백 엔드 애플리케이션입니다. 디바이스에서 직접 메서드를 호출하려면 이 애플리케이션을 IoT 허브의 서비스 측 엔드포인트에 연결합니다.

> [!IMPORTANT]
> 이 문서에서는 백 엔드 애플리케이션에서 Python V1 서비스 클라이언트를 사용하고, 디바이스 애플리케이션에서 Python V2 디바이스 클라이언트를 사용합니다. V1 서비스 클라이언트는 Azure IoT Python SDK GitHub 리포지토리의 [v1-deprecated 분기](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated)에 있습니다. V1 서비스 클라이언트용 Pip 패키지(*azure-iothub-service-client*)에 개발 머신에 설치된 Python 버전을 포함하여 플랫폼별 요구 사항이 엄격하게 적용됩니다. 이러한 요구 사항은 **필수 조건** 섹션에 나와 있습니다.
>

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

다음 명령을 실행하여 Cloud Shell 인스턴스에 Azure CLI용 Microsoft Azure IoT 확장을 추가합니다. IOT 확장은 Azure CLI에 IoT Hub, IoT Edge 및 IoT DPS(Device Provisioning Service)별 명령을 추가합니다.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

아직 수행하지 않은 경우 https://github.com/Azure-Samples/azure-iot-samples-python/archive/master.zip 에서 Python 프로젝트를 샘플을 다운로드하고 ZIP 보관 파일을 추출합니다.

**Windows의 경우** V1 IoT Hub 서비스 클라이언트 Pip 패키지를 설치하려면 다음 필수 구성 요소가 필요합니다.

* [Python 버전 **3.6.x**](https://www.python.org/downloads/)가 설치되어 있는지 확인합니다.

* [Visual Studio용 Visual C++ 재배포 가능 패키지](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads)가 설치되어 있는지 확인합니다.

**Windows가 아닌 플랫폼의 경우** V1 SDK 설명서의 [Python Pip 패키지 배포 표](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md#python-pip-package-distribution-table)를 참조하세요. 플랫폼에 지정된 Python 3.x 버전 및 모든 관련 요구 사항이 개발 머신에 설치되어 있는지 확인합니다. 2\.7이 아닌 Python 3.x를 설치하면 V2 디바이스 클라이언트에서 비동기 작업을 수행하도록 설정되며, 이 빠른 시작에서도 사용됩니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이전 [빠른 시작: 디바이스에서 IoT 허브로 원격 분석 보내기](quickstart-send-telemetry-python.md)를 완료한 경우 이 단계를 건너뛸 수 있습니다.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>디바이스 등록

이전 [빠른 시작: 디바이스에서 IoT 허브로 원격 분석 보내기](quickstart-send-telemetry-python.md)를 완료한 경우 이 단계를 건너뛸 수 있습니다.

연결을 위해 디바이스를 IoT Hub에 등록해야 합니다. 이 빠른 시작에서는 Azure Cloud Shell을 사용하여 시뮬레이션된 디바이스를 등록합니다.

1. Azure Cloud Shell에서 다음 명령을 실행하여 디바이스 ID를 만듭니다.

    **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    **MyPythonDevice**: 등록 중인 디바이스의 이름입니다. 표시된 대로 **MyPythonDevice**를 사용하는 것이 좋습니다. 다른 디바이스 이름을 선택하는 경우 이 문서 전체에서도 해당 이름을 사용해야 하며, 샘플 애플리케이션에서 디바이스 이름을 업데이트한 후 실행해야 합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyPythonDevice
    ```

2. Azure Cloud Shell에서 다음 명령을 실행하여 방금 등록한 디바이스의 _디바이스 연결 문자열_을 가져옵니다.

    **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyPythonDevice --output table
    ```

    다음과 같은 디바이스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다.

3. 또한 백 엔드 애플리케이션을 IoT 허브에 연결하여 메시지를 검색할 수 있게 하려면 _서비스 연결 문자열_이 필요합니다. 다음 명령은 IoT Hub에 대한 서비스 연결 문자열을 검색합니다.

    **YourIoTHubName**: 이 자리 표시자를 IoT 허브용으로 선택한 이름으로 바꿉니다.

    ```azurecli-interactive
    az iot hub show-connection-string \
      --policy-name service \
      --name {YourIoTHubName} \
      --output table
    ```

    다음과 같은 서비스 연결 문자열을 기록해 둡니다.

   `HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

    이 값은 빠른 시작의 뒷부분에서 사용합니다. 서비스 연결 문자열은 이전 단계에서 기록한 디바이스 연결 문자열과 다릅니다.

## <a name="listen-for-direct-method-calls"></a>직접 메서드 호출 수신 대기

시뮬레이션된 디바이스 애플리케이션은 IoT 허브의 디바이스별 엔드포인트에 연결하고, 시뮬레이션된 원격 분석을 전송하고, 허브에서 직접 메서드 호출을 수신 대기합니다. 이 빠른 시작에서 허브의 직접 메서드 호출은 디바이스에 원격 분석을 보내는 간격을 변경하도록 지시합니다. 시뮬레이션된 디바이스는 직접 메서드를 실행한 후 승인을 다시 허브로 보냅니다.

1. 로컬 터미널 창에서 Python 프로젝트 샘플의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\simulated-device-2** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **SimulatedDevice.py** 파일을 엽니다.

    `CONNECTION_STRING` 변수의 값을 이전에 기록해 둔 디바이스 연결 문자열로 바꿉니다. 그런 다음, 변경 내용을 **SimulatedDevice.py**에 저장합니다.

1. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션에 필요한 라이브러리를 설치합니다.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션을 실행합니다.

    ```cmd/sh
    python SimulatedDevice.py
    ```

    다음 스크린샷에서는 시뮬레이션된 디바이스 애플리케이션에서 IoT 허브에 원격 분석을 보낼 때의 출력을 보여 줍니다.

    ![시뮬레이션된 디바이스 실행](./media/quickstart-control-device-python/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>직접 메서드 호출

백 엔드 애플리케이션은 IoT Hub의 서비스 측 엔드포인트에 연결합니다. 애플리케이션은 IoT 허브를 통해 디바이스에 직접 메서드 호출을 하고 승인을 수신 대기합니다. IoT Hub 백 엔드 애플리케이션은 일반적으로 클라우드에서 실행됩니다.

1. 다른 로컬 터미널 창에서 Python 프로젝트 샘플의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\back-end-application** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **BackEndApplication.py** 파일을 엽니다.

    `CONNECTION_STRING` 변수의 값을 이전에 기록해 둔 서비스 연결 문자열로 바꿉니다. 그런 다음, 변경 내용을 **BackEndApplication.py**에 저장합니다.

1. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션에 필요한 라이브러리를 설치합니다.

    ```cmd/sh
    pip install azure-iothub-service-client future
    ```

1. 로컬 터미널 창에서 다음 명령을 실행하여 백 엔드 애플리케이션을 실행합니다.

    ```cmd/sh
    python BackEndApplication.py
    ```

    다음 스크린샷에서는 애플리케이션에서 디바이스에 직접 메서드를 호출하고 승인을 받을 때의 출력을 보여 줍니다.

    ![백 엔드 애플리케이션 실행](./media/quickstart-control-device-python/BackEndApplication.png)

    백 엔드 애플리케이션을 실행한 후 시뮬레이션된 디바이스를 실행하는 콘솔 창에 메시지가 표시되고 메시지를 보내는 속도가 변경됩니다.

    ![시뮬레이션된 클라이언트에서 변경](./media/quickstart-control-device-python/SimulatedDevice-2.png)

    > [!NOTE]
    > *iothub_service_client*를 가져오는 동안 오류가 발생하는 경우 [필수 조건](#prerequisites)에서 플랫폼에 지정된 정확한 버전의 Python 및 기타 모든 관련 아티팩트가 설치되어 있는지 확인합니다. 필수 구성 요소를 확인한 후에도 오류가 계속 발생하는 경우 플랫폼용 서비스 클라이언트를 빌드해야 할 수 있습니다. 플랫폼용 SDK를 빌드하는 방법에 대한 자세한 내용은 V1 SDK 설명서의 [devbox 설치 지침](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md)을 참조하세요.
    >

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 백 엔드 애플리케이션에서 디바이스에 직접 메서드를 호출하고, 시뮬레이션된 디바이스 애플리케이션에서 직접 메서드 호출에 응답했습니다.

디바이스-클라우드 메시지를 클라우드의 다른 대상으로 라우팅하는 방법을 알아보려면 다음 자습서로 계속 진행합니다.

> [!div class="nextstepaction"]
> [자습서: 처리를 위해 다른 엔드포인트로 원격 분석 라우팅](tutorial-routing.md)