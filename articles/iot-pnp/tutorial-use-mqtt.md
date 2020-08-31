---
title: MQTT를 사용하여 IoT 플러그 앤 플레이 미리 보기 디바이스 클라이언트 만들기 | Microsoft Docs
description: Azure IoT 디바이스 SDK를 사용하지 않고 MQTT 프로토콜을 사용하여 IoT 플러그 앤 플레이 미리 보기 디바이스 클라이언트를 직접 만듭니다.
author: ericmitt
ms.author: ericmitt
ms.date: 05/13/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 56463b03fe633959585e14271050bcdaacb25663
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87535209"
---
# <a name="use-mqtt-to-develop-an-iot-plug-and-play-preview-device-client"></a>MQTT를 사용하여 IoT 플러그 앤 플레이 미리 보기 디바이스 클라이언트 개발

가급적이면 Azure IoT 디바이스 SDK 중 하나를 사용하여 IoT 플러그 앤 플레이 디바이스 클라이언트를 빌드해야 합니다. 그러나 메모리가 제한된 디바이스를 사용하는 시나리오에서는 MQTT 라이브러리를 사용하여 IoT 허브와 통신해야 할 수도 있습니다.

이 자습서의 샘플에서는 [Eclipse Mosquitto](http://mosquitto.org/) MQTT 라이브러리 및 Visual Studio를 사용합니다. 이 자습서의 단계에서는 개발 머신에서 Windows를 사용한다고 가정합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 구성 요소

Windows에서 이 자습서를 완료하려면 로컬 Windows 환경에 다음 소프트웨어를 설치합니다.

* [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) - Visual Studio를 [설치](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019)할 때 **C++를 사용한 데스크톱 개발** 워크로드를 포함해야 합니다.
* [Git](https://git-scm.com/download/)
* [CMake](https://cmake.org/download/)
* [Azure IoT 탐색기](howto-install-iot-explorer.md)

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브에 연결하는 디바이스의 공유 액세스 서명을 가져옵니다. 이 문자열을 기록해 둡니다. 이 자습서의 뒷부분에서 필요합니다.

```azurecli-interactive
az iot hub generate-sas-token -d <YourDeviceID> -n <YourIoTHubName>
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

다음과 같이 IoT 허브 연결 문자열을 사용하여 **Azure IoT 탐색기** 도구를 구성합니다.

1. **Azure IoT 탐색기** 도구를 실행합니다.
1. **설정** 페이지에서 IoT 허브 연결 문자열을 **앱 구성** 설정에 붙여넣습니다.
1. **저장 및 연결**을 선택합니다.
1. 이전에 추가한 디바이스는 기본 페이지의 디바이스 목록에 있습니다.

## <a name="clone-sample-repo"></a>샘플 리포지토리 복제

다음 명령을 실행하여 로컬 머신의 적절한 위치에 샘플 리포지토리를 복제합니다.

```cmd
git clone https://github.com/Azure-Samples/IoTMQTTSample.git
```

## <a name="install-mqtt-library"></a>MQTT 라이브러리 설치

`vcpkg` 도구를 사용하여 Eclipse Mosquitto 라이브러리를 다운로드하고 빌드합니다.

다음 명령을 사용하여 로컬 머신의 적절한 위치에 **Vcpkg** 리포지토리를 복제합니다.

```cmd
git clone https://github.com/Microsoft/vcpkg.git
```

다음 명령을 사용하여 `vcpkg` 환경을 준비합니다. `vcpkg integrate install`을 실행할 때 관리자 권한 명령 프롬프트가 필요합니다.

```cmd
cd vcpkg
.\bootstrap-vcpkg.bat
.\vcpkg integrate install
```

다음 명령을 사용하여 Eclipse Mosquitto 라이브러리를 다운로드하고 빌드합니다.

```cmd
.\vcpkg install mosquitto:x64-windows
```

## <a name="migrate-the-sample-to-iot-plug-and-play"></a>샘플을 IoT 플러그 앤 플레이로 마이그레이션

### <a name="review-the-non-iot-plug-and-play-sample-code"></a>비 IoT 플러그 앤 플레이 샘플 코드 검토

코드를 빌드하고 실행하기 전에 IoT 허브 및 디바이스의 세부 정보로 코드를 업데이트합니다.

Visual Studio에서 샘플 코드를 보려면 *IoTMQTTSample\src\Windows* 폴더의 *MQTTWin32.sln* 솔루션 파일을 엽니다.

**솔루션 탐색기**에서 **TelemetryMQTTWin32** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다.

**TelemetryMQTTWin32** 프로젝트에서 **MQTT_Mosquitto.cpp** 원본 파일을 엽니다. 연결 정보 정의를 이전에 적어 둔 디바이스 세부 정보로 업데이트합니다. 다음 항목의 토큰 문자열 자리 표시자를 바꿉니다.

* `IOTHUBNAME` 식별자를 앞에서 만든 IoT 허브 이름으로 바꿉니다.
* `DEVICEID` 식별자를 앞에서 만든 디바이스 이름으로 바꿉니다.
* `PWD` 식별자를 디바이스에 대해 생성한 공유 액세스 서명 값으로 바꿉니다.

Azure IoT 탐색기를 시작하고 원격 분석 수신 대기를 시작하여 코드가 제대로 작동하는지 확인합니다.

애플리케이션을 실행합니다(Ctrl+F5). 몇 초 후에 다음과 같은 출력이 표시됩니다.

:::image type="content" source="media/tutorial-use-mqtt/mqtt-sample-output.png" alt-text="MQTT 샘플 애플리케이션의 출력":::

Azure IoT 탐색기를 보면 IoT 플러그 앤 플레이 디바이스가 아닌 것을 확인할 수 있습니다.

:::image type="content" source="media/tutorial-use-mqtt/non-pnp-iot-explorer.png" alt-text="Azure IoT 탐색기의 비 IoT 플러그 앤 플레이 디바이스":::

### <a name="make-the-device-an-iot-plug-and-play-device"></a>디바이스를 IoT 플러그 앤 플레이 디바이스로 만들기

IoT 플러그 앤 플레이 디바이스는 일련의 간단한 규칙을 따라야 합니다. 디바이스가 연결할 때 모델 ID를 보내면 해당 디바이스는 IoT 플러그 앤 플레이 디바이스가 됩니다.

이 샘플에서는 MQTT 연결 패킷에 모델 ID**를 추가합니다. 모델 ID를 `USERNAME`에 querystring 매개 변수로 전달하고 `api-version`을 `2020-05-31-preview`로 변경합니다.

```c
// computed Host Username and Topic
//#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2018-06-30"
#define USERNAME IOTHUBNAME ".azure-devices.net/" DEVICEID "/?api-version=2020-05-31-preview&model-id=dtmi:com:example:Thermostat;1"
#define PORT 8883
#define HOST IOTHUBNAME //".azure-devices.net"
#define TOPIC "devices/" DEVICEID "/messages/events/"
```

샘플을 다시 빌드하고 실행합니다.

이제 다음과 같이 디바이스 쌍에 모델 ID가 포함됩니다.

:::image type="content" source="media/tutorial-use-mqtt/model-id-iot-explorer.png" alt-text="Azure IoT 탐색기에서 모델 ID 보기":::

이제 IoT 플러그 앤 플레이 구성 요소를 탐색할 수 있습니다.

:::image type="content" source="media/tutorial-use-mqtt/components-iot-explorer.png" alt-text="Azure IoT 탐색기에서 구성 요소 보기":::

이제 모델에 정의된 원격 분석, 속성 및 명령을 구현하도록 디바이스 코드를 수정할 수 있습니다. Mosquitto 라이브러리를 사용하여 자동 온도 조절기 디바이스를 구현하는 예제를 보려면 GitHub의 [Windows에서 IoT SDK를 사용하지 않고 Azure IoTHub에서 MQTT PnP 사용](https://github.com/Azure-Samples/IoTMQTTSample/tree/master/src/Windows/PnPMQTTWin32)을 참조하세요.

> [!NOTE]
> 기본적으로 공유 액세스 서명은 60분 동안만 유효합니다.

> [!NOTE]
>클라이언트에서는 `IoTHubRootCA_Baltimore.pem` 루트 인증서 파일을 사용하여 연결하는 IoT 허브의 ID를 확인합니다.

### <a name="mqtt-topics"></a>MQTT 토픽

다음은 디바이스에서 IoT 허브에 정보를 전송하는 데 사용하는 MQTT 토픽의 정의입니다. 자세한 내용은 [MQTT 프로토콜을 사용하여 IoT 허브와 통신](../iot-hub/iot-hub-mqtt-support.md)을 참조하세요.

* `DEVICE_CAPABILITIES_MESSAGE`는 디바이스에서 구현하는 인터페이스를 보고하는 데 사용하는 토픽을 정의합니다.
* `DEVICETWIN_PATCH_MESSAGE`는 디바이스에서 IoT 허브에 속성 업데이트를 보고하는 데 사용하는 토픽을 정의합니다.
* `DEVICE_TELEMETRY_MESSAGE`는 디바이스에서 IoT 허브에 원격 분석을 보고하는 데 사용하는 토픽을 정의합니다.

MQTT에 대한 자세한 내용은 [Azure IoT에 대한 MQTT 샘플](https://github.com/Azure-Samples/IoTMQTTSample/) GitHub 리포지토리를 참조하세요.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT 플러그 앤 플레이 규칙을 따르도록 MQTT 디바이스 클라이언트를 수정하는 방법을 배웠습니다. IoT 플러그 앤 플레이에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [아키텍처](concepts-architecture.md)

MQTT 프로토콜의 IoT Hub 지원에 대한 자세한 내용은 다음 항목을 참조하세요.

> [!div class="nextstepaction"]
> [MQTT 프로토콜을 사용하여 IoT 허브와 통신](../iot-hub/iot-hub-mqtt-support.md)
