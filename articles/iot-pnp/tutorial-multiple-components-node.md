---
title: IoT 플러그 앤 플레이 미리 보기 샘플 Node.js 구성 요소 디바이스 코드를 IoT Hub에 연결 | Microsoft Docs
description: 여러 구성 요소를 사용하고 IoT 허브에 연결하는 IoT 플러그 앤 플레이 미리 보기 샘플 Node.js 디바이스 코드를 빌드하고 실행합니다. Azure IoT 탐색기 도구를 사용하여 디바이스에서 허브로 전송된 정보를 봅니다.
author: olivakar
ms.author: olkar
ms.date: 07/10/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.custom: devx-track-javascript
ms.openlocfilehash: d26179ab82f29ce8f937f5b444463c1308d92047
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87904075"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-nodejs"></a>자습서: 샘플 IoT 플러그 앤 플레이 미리 보기 여러 구성 요소 디바이스 애플리케이션을 IoT Hub에 연결(Node.js)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

이 자습서에서는 구성 요소 및 루트 인터페이스를 사용하여 샘플 IoT 플러그 앤 플레이 디바이스 애플리케이션을 빌드하고, IoT 허브에 연결하고, Azure IoT 탐색기 도구를 사용하여 허브로 전송된 정보를 확인하는 방법을 보여줍니다. 샘플 애플리케이션은 Node.js용으로 작성되었으며 Node.js용 Azure IoT Hub 디바이스 SDK에 포함되어 있습니다. 솔루션 빌더는 디바이스 코드를 볼 필요 없이 Azure IoT 탐색기 도구를 사용하여 IoT 플러그 앤 플레이 디바이스의 기능을 이해할 수 있습니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 개발 머신에 Node.js가 필요합니다. [nodejs.org](https://nodejs.org)에서 여러 플랫폼에 권장하는 최신 버전을 다운로드할 수 있습니다.

다음 명령을 사용하여 개발 컴퓨터에서 Node.js의 현재 버전을 확인할 수 있습니다.

```cmd/sh
node --version
```

### <a name="azure-iot-explorer"></a>Azure IoT 탐색기

이 자습서의 두 번째 부분에서 샘플 디바이스와 상호 작용하려면 **Azure IoT 탐색기** 도구를 사용합니다. 운영 체제용 [최신 릴리스의 Azure IoT 탐색기를 다운로드하여 설치](./howto-use-iot-explorer.md)합니다.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

다음 명령을 실행하여 허브에 대한 _IoT 허브 연결 문자열_을 가져옵니다. 이 연결 문자열을 기록해 둡니다. 이 자습서의 뒷부분에서 필요합니다.

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Azure IoT 탐색기 도구를 사용하여 IoT 허브 연결 문자열을 찾을 수도 있습니다.

다음 명령을 실행하여 허브에 추가한 디바이스에 대한 _디바이스 연결 문자열_을 가져옵니다. 이 연결 문자열을 기록해 둡니다. 이 자습서의 뒷부분에서 필요합니다.

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>코드 다운로드

이 자습서에서는 Node.js용 Azure IoT Hub 디바이스 SDK를 복제하고 빌드하는 데 사용할 수 있는 개발 환경을 준비합니다.

선택한 디렉터리에서 명령 프롬프트를 엽니다. 다음 명령을 실행하여 [Node.js용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 리포지토리를 다음 위치에 복제합니다.

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-node
```

이 작업을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="install-required-libraries"></a>필수 라이브러리 설치

디바이스 SDK를 사용하여 포함된 샘플 코드를 빌드합니다. 여러분이 빌드하는 애플리케이션은 IoT 허브에 연결하는 여러 구성 요소 및 루트 인터페이스를 사용하여 플러그 앤 플레이 디바이스를 시뮬레이션합니다. 이 애플리케이션은 원격 분석 데이터 및 속성을 보내고 명령을 수신합니다.

1. 로컬 터미널 창에서, 복제된 리포지토리의 폴더로 이동하여 */azure-iot-sdk-node/device/samples/pnp* 폴더를 찾습니다. 그런 다음, 다음 명령을 실행하여 필요한 라이브러리를 설치합니다.

```cmd/sh
npm install
```

그러면 폴더의 샘플을 실행하는 데 필요한 관련 npm 파일이 설치됩니다.

1. 이전에 기록한 디바이스 연결 문자열을 사용하여 환경 변수를 구성합니다.

```cmd/sh
set DEVICE_CONNECTION_STRING=<YourDeviceConnectionString>
```

## <a name="review-the-code"></a>코드 검토

*azure-iot-sdk-node\device\samples\pnp* 폴더로 이동합니다.

*azure-iot-sdk-node\device\samples\pnp* 폴더에는 IoT 플러그 앤 플레이 온도 컨트롤러 디바이스에 대한 샘플 코드가 포함되어 있습니다.

*pnpTemperatureController.js* 파일의 코드는 IoT 플러그 앤 플레이 온도 컨트롤러 디바이스를 구현합니다. 이 샘플에서 구현하는 모델은 [여러 구성 요소](concepts-components.md)를 사용합니다. [온도 디바이스용 DTDL(Digital Twins 정의 언어) 모델 파일](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)은 디바이스에서 구현하는 원격 분석, 속성 및 명령을 정의합니다.

선택한 코드 편집기에서 *pnpTemperatureController.js* 파일을 엽니다. 샘플 코드는 다음 작업을 수행하는 방법을 보여줍니다.

1. 구현되는 디바이스의 DTMI인 `modelId`를 정의합니다. 이 DTMI는 사용자 정의되며 [온도 컨트롤러 DTDL 모델](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)의 DTMI와 일치해야 합니다.

2. 온도 컨트롤러 DTDL 모델에 정의된 구성 요소를 구현합니다. 실제 온도 컨트롤러의 구성 요소는 이러한 두 인터페이스를 구현해야 합니다. 이러한 두 인터페이스는 중앙 리포지토리에 이미 게시되어 있습니다. 이 샘플의 두 인터페이스는 다음과 같습니다.
  - Thermostat
  - Azure에서 개발한 디바이스 정보

3. 구성 요소 이름을 정의합니다. 이 샘플에는 두 개의 자동 온도 조절기와 하나의 디바이스 정보 구성 요소가 있습니다.

4. 명령 이름을 정의합니다. 디바이스에서 이러한 명령에 응답합니다.

5. `serialNumber` 상수를 정의합니다. `serialNumber`는 고정된 특정 디바이스입니다.

6. 명령 처리기를 정의합니다.

7. 명령 응답을 보낼 함수를 정의합니다.

8. 명령 요청을 기록하는 도우미 함수를 정의합니다.

9. 속성을 만드는 도우미 함수를 정의합니다.

10. 속성 업데이트 수신기를 정의합니다.

11. 이 디바이스의 원격 분석 데이터를 전송하는 함수를 정의합니다. 자동 온도 조절기와 루트 구성 요소 둘 다 원격 분석 데이터를 전송합니다. 이 함수는 구성 요소 이름을 매개 변수로 수신합니다.

12. 다음과 같은 작업을 수행하는 `main` 함수를 정의합니다.

    1. 디바이스 SDK를 사용하여 디바이스 클라이언트를 만들고 IoT 허브에 연결합니다. 디바이스에서 `modelId`를 제공합니다. 그러면 IoT Hub가 디바이스를 IoT 플러그 앤 플레이 디바이스로 식별할 수 있습니다.

    1. `onDeviceMethod` 함수를 사용하여 명령 요청 수신 대기를 시작합니다. 이 함수는 다음과 같이 서비스의 명령 요청에 대한 수신기를 설정합니다.
        - 디바이스 DTDL은 `reboot` 및 `getMaxMinReport` 명령을 정의합니다.
        - `commandHandler` 함수는 디바이스가 명령에 응답하는 방법을 정의합니다.

    1. `setInterval` 및 `sendTelemetry`를 사용하여 원격 분석 보내기를 시작합니다.

    1. `helperCreateReportedPropertiesPatch` 함수를 사용하여 속성을 만들고 `updateComponentReportedProperties` 함수를 사용하여 속성을 업데이트합니다.

    1. `desiredPropertyPatchListener`를 사용하여 속성 업데이트를 수신 대기합니다.

    1. **Q** 또는 **q**를 누르면 모든 수신기 및 작업을 사용하지 않도록 설정하고 루프를 종료합니다.

코드를 살펴보았으면 다음 명령을 사용하여 샘플을 실행합니다.

```cmd\sh
node pnpTemperatureController.js
```

디바이스가 허브로 원격 분석 데이터를 보내기 시작했으며, 이제 명령 및 속성 업데이트를 받을 준비가 되었다는 것을 나타내는 다음 출력이 표시됩니다.

![디바이스 확인 메시지](media/tutorial-multiple-components-node/multiple-component.png)

다음 단계를 완료하는 동안 샘플을 계속 실행하세요.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Azure IoT 탐색기를 사용하여 코드의 유효성을 검사합니다.

디바이스 클라이언트 샘플이 시작되면 Azure IoT 탐색기 도구를 사용하여 작동하는지 확인합니다.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT 플러그 앤 플레이 디바이스를 구성 요소와 IoT 허브에 연결하는 방법을 알아보았습니다. IoT 플러그 앤 플레이 디바이스 모델에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [IoT 플러그 앤 플레이 미리 보기 모델링 개발자 가이드](concepts-developer-guide.md)
