---
title: IoT 플러그 앤 플레이 미리 보기 샘플 Python 디바이스 코드를 Azure IoT Hub에 연결 | Microsoft Docs
description: Python을 사용하여 IoT 허브에 연결되는 IoT 플러그 앤 플레이 미리 보기 샘플 디바이스 코드를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: ericmitt
ms.author: ericmitt
ms.date: 7/14/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 84ef7ff18c294097da20640c1de237b41900cb40
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352699"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub-python"></a>빠른 시작: 샘플 IoT 플러그 앤 플레이 미리 보기 디바이스 애플리케이션을 IoT Hub에 연결(Python)

[!INCLUDE [iot-pnp-quickstarts-device-selector.md](../../includes/iot-pnp-quickstarts-device-selector.md)]

이 빠른 시작에서는 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 전송되는 원격 분석을 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 Python용으로 작성되었으며 Python용 Azure IoT Hub 디바이스 SDK에 포함되어 있습니다. Azure IoT 탐색기 도구를 사용하면 솔루션 빌더가 디바이스 코드를 보지 않고도 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 개발 머신에 Python 3.7이 필요합니다. [python.org](https://www.python.org/)에서 여러 플랫폼에 권장되는 최신 버전을 다운로드할 수 있습니다. 다음 명령을 사용하여 현재 Python 버전을 확인할 수 있습니다.  

```cmd/sh
python --version
```

### <a name="azure-iot-explorer"></a>Azure IoT 탐색기

이 빠른 시작의 두 번째 부분에서 샘플 디바이스와 상호 작용하려면 **Azure IoT 탐색기** 도구를 사용합니다. 운영 체제용 [최신 릴리스의 Azure IoT 탐색기를 다운로드하여 설치](./howto-use-iot-explorer.md)합니다.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브에 대한 IoT 허브 연결 문자열을 가져옵니다. 이 연결 문자열을 기록해 두십시오. 이 빠른 시작의 뒷부분에서 사용하게 됩니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Azure IoT 탐색기 도구를 사용하여 IoT 허브 연결 문자열을 찾을 수도 있습니다.

다음 명령을 실행하여 허브에 추가한 디바이스에 대한 디바이스 연결 문자열을 가져옵니다. 이 연결 문자열을 기록해 두십시오. 이 빠른 시작의 뒷부분에서 사용하게 됩니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="set-up-your-environment"></a>환경 설정

이 패키지는 공개 미리 보기 새로 고침을 위한 PIP로 게시됩니다. 패키지 버전은 `2.1.4` 이상이어야 합니다.

로컬 Python 환경에서 다음과 같이 파일을 설치합니다.

```cmd/sh
pip install azure-iot-device
```

Python SDK IoT 리포지토리를 복제하고 **마스터**를 체크 아웃합니다.

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-python
```

## <a name="run-the-sample-device"></a>샘플 디바이스 실행

*azure-iot-sdk-python\azure-iot-device\samples\pnp* 폴더에는 IoT 플러그 앤 플레이 디바이스에 대한 샘플 코드가 포함되어 있습니다. 이 빠른 시작에서는 *pnp_thermostat py* 파일을 사용합니다. 이 샘플 코드는 IoT 플러그 앤 플레이 호환 디바이스를 구현하고 Azure IoT Python 디바이스 클라이언트 라이브러리를 사용합니다.

**IOTHUB_DEVICE_CONNECTION_STRING**이라는 환경 변수를 만들어 이전에 기록한 디바이스 연결 문자열을 저장합니다.

텍스트 편집기에서 **pnp_thermostat.py** 파일을 엽니다. 다음과 같은 방법을 살펴보세요.

1. [자동 온도 조절기](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)를 고유하게 나타내는 단일 DTMI(Device Twin 모델 식별자)를 정의하는 방법. DTMI는 사용자에게 알려져야 하며 디바이스 구현 시나리오에 따라 달라집니다. 현재 샘플의 경우 모델은 온도 모니터링과 관련된 원격 분석, 속성, 명령이 있는 자동 온도 조절기를 나타냅니다.

1. 함수를 통해 명령 처리기 구현을 정의하는 방법. 이러한 처리기를 작성하여 디바이스가 명령 요청에 응답하는 방식을 정의합니다.

1. 함수를 통해 명령 응답을 정의하는 방법. IoT 허브로 응답을 보내는 명령 응답 함수를 만듭니다.

1. 애플리케이션을 종료할 수 있는 입력 키보드 수신기 함수를 정의합니다.

1. **main** 함수를 사용하는 방법. **main** 함수는 다음을 수행합니다.

    1. 디바이스 SDK를 사용하여 디바이스 클라이언트를 만들고 IoT 허브에 연결합니다.

    1. 속성을 업데이트합니다. 현재 사용하는 모델인 **자동 온도 조절기**는 자동 온도 조절기의 두 가지 속성으로 `targetTemperature`와 `maxTempSinceLastReboot`를 정의하기 때문에 해당 속성을 사용하겠습니다. 속성은 `device_client`에 정의된 `patch_twin_reported_properties` 메서드를 사용하여 업데이트됩니다.

    1. **execute_command_listener** 함수를 사용하여 명령 요청 수신 대기를 시작합니다. 이 함수는 서비스가 보내는 명령을 수신 대기하는 '수신기'를 설정합니다. 수신기를 설정할 때 `method_name`, `user_command_handler` 및 `create_user_response_handler`를 제공합니다. 
        - `user_command_handler` 함수는 디바이스가 명령을 수신할 때 수행할 작업을 정의합니다. 예를 들어 알람이 울릴 때 이 명령을 수신한 효과는 깨어나는 것입니다. 이것을 호출되는 명령의 '효과'라고 간주합니다.
        - `create_user_response_handler` 함수는 명령이 성공적으로 실행될 때 IoT 허브로 전송할 응답을 만듭니다. 예를 들어 알람이 울릴 때 다시 알림을 눌러서 응답하면 이것이 서비스에 대한 피드백입니다. 이것을 서비스에 대한 응답이라고 간주합니다. 포털에서 이 응답을 볼 수 있습니다.

    1. 원격 분석 보내기를 시작합니다. **pnp_send_telemetry**는 pnp_methods.py 파일에 정의되어 있습니다. 샘플 코드는 루프를 사용하여 8초마다 이 함수를 호출합니다.

    1. **Q** 또는 **q**를 누르면 모든 수신기 및 작업을 사용하지 않도록 설정하고 루프를 종료합니다.

코드를 살펴보았으면 다음 명령을 사용하여 샘플을 실행합니다.

```cmd/sh
python pnp_thermostat.py
```

디바이스가 허브로 원격 분석 데이터를 보내고 있으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었다는 것을 나타내는 다음 출력이 표시됩니다.

```cmd/sh
Connecting using Connection String HostName=<your hub name>.azure-devices.net;DeviceId=<your device id>;SharedAccessKey=<your device shared access key>
Listening for command requests and property updates
Press Q to quit
Sending telemetry for temperature
Sent message
```

다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드 유효성 검사

디바이스 클라이언트 샘플이 시작되면 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 IoT 플러그 앤 플레이 디바이스를 IoT Hub에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스와 상호 작용하는 솔루션을 빌드하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [솔루션에 연결된 IoT 플러그 앤 플레이 미리 보기 디바이스와 상호 작용](quickstart-service-python.md)
