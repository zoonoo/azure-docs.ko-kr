---
title: IoT 플러그 앤 플레이 미리 보기 디바이스 만들기 및 테스트 | Microsoft Docs
description: 디바이스 개발자가 VS Code를 사용하여 IoT 플러그 앤 플레이 미리 보기 디바이스의 새 디바이스 기능 모델을 만들고 테스트하는 방법을 알아봅니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/30/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 209c63207653e98967a5a66e36c0cae53b7e044f
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550318"
---
# <a name="tutorial-create-and-test-a-device-capability-model-using-visual-studio-code"></a>자습서: Visual Studio Code를 사용하여 디바이스 기능 모델 만들기 및 테스트

이 자습서에서는 디바이스 개발자가 Visual Studio Code를 사용하여 _디바이스 기능 모델_을 만드는 방법을 보여 줍니다. 모델을 사용하여 클라우드의 Azure IoT Hub 인스턴스에 연결하는 디바이스에서 실행되는 기본 코드를 생성할 수 있습니다.

생성된 기본 코드를 빌드하는 방법을 설명하는 이 자습서의 섹션에서는 Windows를 사용하고 있다고 가정합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 디바이스 기능 모델 만들기
> * 모델에서 기본 디바이스 코드 생성
> * 생성된 코드에서 스텁 구현
> * IoT Hub와의 상호 작용을 테스트하는 코드 실행

## <a name="prerequisites"></a>사전 요구 사항

이 자습서에서 디바이스 기능 모델을 사용하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/download): VS Code는 여러 플랫폼에 사용할 수 있습니다.
* [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 확장 팩. 다음 단계에 따라 VS Code에 확장 팩을 설치합니다.

    1. VS Code에서 **확장** 탭을 선택합니다.
    1. **Azure IoT Tools**를 검색합니다.
    1. **설치**를 선택합니다.

이 자습서에서 생성된 C 코드를 Windows에 빌드하려면 다음이 필요합니다.

* **C++ 빌드 도구** 및 **NuGet 패키지 관리자 구성 요소** 워크로드를 사용하여 [Visual Studio용 도구를 빌드](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16)합니다. 또는 동일한 워크로드가 설치된 [Visual Studio(Community, Professional 또는 Enterprise)](https://visualstudio.microsoft.com/downloads/) 2019, 2017 또는 2015가 이미 있는 경우
* [Git](https://git-scm.com/download)
* [CMake](https://cmake.org/download/)

이 자습서에서 디바이스 코드를 테스트하려면 다음이 필요합니다.

* [Azure IoT 탐색기](https://github.com/Azure/azure-iot-explorer/releases)
* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="model-your-device"></a>디바이스 모델링

_디지털 쌍 정의 언어_를 사용하여 디바이스 기능 모델을 만듭니다. 모델은 일반적으로 여러 _인터페이스_ 정의 파일과 단일 모델 파일로 구성됩니다. **Azure IoT Tools for VS Code**에는 이러한 JSON 파일을 만들고 편집할 수 있는 도구가 포함되어 있습니다.

### <a name="create-the-interface-file"></a>인터페이스 파일 만들기

VS Code에서 IoT 디바이스의 기능을 정의하는 인터페이스 파일을 만들려면 다음을 수행합니다.

1. **devicemodel**이라는 폴더를 만듭니다.

1. VS Code를 시작하고, **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이**를 입력한 다음, **IoT 플러그 앤 플레이: 인터페이스 만들기** 명령을 선택합니다.

1. 만든 **devicemodel** 폴더를 찾아서 선택합니다.

1. 그런 다음, 인터페이스 이름으로 **EnvironmentalSensor**를 입력하고, **Enter** 키를 누릅니다. VS Code에서 **EnvironmentalSensor.interface.json**이라는 샘플 인터페이스 파일을 만듭니다.

1. 이 파일의 내용을 다음 JSON으로 바꾸고, `@id` 필드의 `{your name}`을 고유한 값으로 바꿉니다. a-z, A-Z, 0-9 및 밑줄 문자만 사용합니다. 자세한 내용은 [디지털 쌍 식별자 형식](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format)을 참조하세요. 인터페이스를 리포지토리에 저장하려면 인터페이스 ID가 고유해야 합니다.

    ```json
    {
      "@id": "urn:{your name}:EnvironmentalSensor:1",
      "@type": "Interface",
      "displayName": "Environmental Sensor",
      "description": "Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
      "comment": "Requires temperature and humidity sensors.",
      "contents": [
        {
          "@type": "Property",
          "displayName": "Device State",
          "description": "The state of the device. Two states online/offline are available.",
          "name": "state",
          "schema": "boolean"
        },
        {
          "@type": "Property",
          "displayName": "Customer Name",
          "description": "The name of the customer currently operating the device.",
          "name": "name",
          "schema": "string",
          "writable": true
        },
        {
          "@type": "Property",
          "displayName": "Brightness Level",
          "description": "The brightness level for the light on the device. Can be specified as 1 (high), 2 (medium), 3 (low)",
          "name": "brightness",
          "writable": true,
          "schema": "long"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Temperature"
          ],
          "description": "Current temperature on the device",
          "displayName": "Temperature",
          "name": "temp",
          "schema": "double",
          "unit": "Units/Temperature/fahrenheit"
        },
        {
          "@type": [
            "Telemetry",
            "SemanticType/Humidity"
          ],
          "description": "Current humidity on the device",
          "displayName": "Humidity",
          "name": "humid",
          "schema": "double",
          "unit": "Units/Humidity/percent"
        },
        {
          "@type": "Telemetry",
          "name": "magnetometer",
          "displayName": "Magnetometer",
          "comment": "This shows a complex telemetry that contains a magnetometer reading.",
          "schema": {
            "@type": "Object",
            "fields": [
              {
                "name": "x",
                "schema": "integer"
              },
              {
                "name": "y",
                "schema": "integer"
              },
              {
                "name": "z",
                "schema": "integer"
              }
            ]
          }
        },
        {
          "@type": "Command",
          "name": "turnon",
          "response": {
            "name": "turnon",
            "schema": "string"
          },
          "comment": "This Commands will turn-on the LED light on the device.",
          "commandType": "synchronous"
        },
        {
          "@type": "Command",
          "name": "turnoff",
          "comment": "This Commands will turn-off the LED light on the device.",
          "response": {
            "name": "turnoff",
            "schema": "string"
          },
          "commandType": "synchronous"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
    }
    ```

    이 인터페이스는 디바이스 속성(예: **Customer Name**), 원격 분석 유형(예: **Temperature**) 및 명령(예: **turnon**)을 정의합니다.

1. 이 인터페이스 파일의 끝에 **blink**라는 명령 기능을 추가합니다. 명령을 추가하기 전에 반드시 쉼표를 추가해야 합니다. 정의를 입력하여 IntelliSense, 자동 완성 및 유효성 검사를 통해 인터페이스 정의를 편집하는 방법을 확인합니다.

    ```json
    {
      "@type": "Command",
      "description": "This command will begin blinking the LED for given time interval.",
      "name": "blink",
      "request": {
        "name": "blinkRequest",
        "schema": {
          "@type": "Object",
          "fields": [
            {
              "name": "interval",
              "schema": "long"
            }
          ]
        }
      },
      "response": {
        "name": "blinkResponse",
        "schema": "string"
      },
      "commandType": "synchronous"
    }
    ```

1. 파일을 저장합니다.

### <a name="create-the-model-file"></a>모델 파일 만들기

모델 파일은 IoT 플러그 앤 플레이 디바이스에서 구현하는 인터페이스를 지정합니다. 일반적으로 모델에는 디바이스의 특정 기능을 정의하는 하나 이상의 인터페이스와 모든 IoT 플러그 앤 플레이 디바이스에서 구현해야 하는 하나의 표준 인터페이스를 포함하여 둘 이상의 인터페이스가 있습니다.

VS Code를 사용하여 IoT 플러그 앤 플레이 디바이스에서 구현하는 인터페이스를 지정하는 모델 파일을 만들려면 다음을 수행합니다.

1. **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이**를 입력한 다음, **IoT 플러그 앤 플레이: 기능 모델 만들기** 명령을 선택합니다. 그런 다음, 모델 이름으로 **SensorboxModel**을 입력합니다. VS Code에서 **SensorboxModel.capabilitymodel.json**이라는 샘플 인터페이스 파일을 만듭니다.

1. 이 파일의 내용을 다음 JSON으로 바꾸고, `@id` 필드 및 `EnvironmentalSensor` 인터페이스의 `{your name}`을 **EnvironmentalSensor.interface.json** 파일에서 사용한 것과 동일한 값으로 바꿉니다. 인터페이스를 리포지토리에 저장하려면 인터페이스 ID가 고유해야 합니다.

    ```json
    {
      "@id": "urn:{your name}:SensorboxModel:1",
      "@type": "CapabilityModel",
      "displayName": "Environmental Sensorbox Model",
      "implements": [
        {
          "schema": "urn:{your name}:EnvironmentalSensor:1",
          "name": "environmentalSensor"
        },
        {
          "schema": "urn:azureiot:DeviceManagement:DeviceInformation:1",
          "name": "deviceinfo"
        }
      ],
      "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
    }
    ```

    모델에서 **EnvironmentalSensor** 인터페이스와 표준 **DeviceInformation** 인터페이스를 구현하는 디바이스를 정의합니다.

1. 파일을 저장합니다.

### <a name="download-the-deviceinformation-interface"></a>DeviceInformation 인터페이스 다운로드

모델에서 기본 코드를 생성하려면 먼저 *퍼블릭 모델 리포지토리*에서 **DeviceInformation**의 로컬 복사본을 만들어야 합니다. 퍼블릭 모델 리포지토리에는 이미 **DeviceInformation** 인터페이스가 포함되어 있습니다.

VS Code를 사용하여 퍼블릭 모델 리포지토리에서 **DeviceInformation** 인터페이스를 다운로드하려면 다음을 수행합니다.

1. **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이**를 입력하고, **모델 리포지토리 열기** 명령을 선택한 다음, **퍼블릭 모델 리포지토리**를 선택합니다.

1. **인터페이스**를 선택하고, ID가 `urn:azureiot:DeviceManagement:DeviceInformation:1`인 디바이스 정보 인터페이스를 선택한 다음, **다운로드**를 선택합니다.

이제 디바이스 기능 모델을 구성하는 다음 세 개의 파일이 있습니다.

* urn_azureiot_DeviceManagement_DeviceInformation_1.interface.json
* EnvironmentalSensor.interface.json
* SensorboxModel.capabilitymodel.json

## <a name="publish-the-model"></a>모델 게시

Azure IoT 탐색기 도구에서 디바이스 기능 모델을 읽으려면 해당 모델을 회사 리포지토리에 게시해야 합니다. VS Code에서 게시하려면 회사 리포지토리에 대한 연결 문자열이 필요합니다.

1. [IoT용 Azure Certified 포털](https://aka.ms/ACFI)로 이동합니다.

1. Microsoft _회사 계정_을 사용하여 포털에 로그인합니다.

1. **회사 리포지토리**를 선택한 다음, **연결 문자열**을 선택합니다.

1. 연결 문자열을 복사합니다.

VS Code에서 회사 리포지토리를 열려면 다음을 수행합니다.

1. **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이**를 입력한 다음, **IoT 플러그 앤 플레이: 모델 리포지토리 열기** 명령을 선택합니다.

1. **조직 모델 리포지토리 열기**를 선택하고, 연결 문자열에 붙여넣습니다.

1. **Enter** 키를 눌러 회사 리포지토리를 엽니다.

디바이스 기능 모델과 인터페이스를 회사 리포지토리에 게시하려면 다음을 수행합니다.

1. **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이**를 입력한 다음, **IoT 플러그 앤 플레이: 모델 리포지토리에 파일 전송** 명령을 선택합니다.

1. **EnvironmentalSensor.interface.json** 및 **SensorboxModel.capabilitymodel.json** 파일을 선택하고, **확인**을 선택합니다.

이제 파일이 회사 리포지토리에 저장됩니다.

## <a name="generate-code"></a>코드 생성

**Azure IoT Tools for VS Code**를 사용하여 모델에서 기본 C 코드를 생성할 수 있습니다. VS Code에서 기본 코드를 생성하려면 다음을 수행합니다.

1. **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이**를 입력한 다음, **IoT 플러그 앤 플레이: 디바이스 코드 스텁 생성** 명령을 선택합니다.

1. **SensorboxModel.capabilitymodel.json** 기능 모델 파일을 선택합니다.

1. 프로젝트 이름으로 **sensorbox_app**을 입력합니다.

1. 언어로 **ANSI C**를 선택합니다.

1. 연결 방법으로 **IoT Hub 디바이스 연결 문자열을 통해**을 선택합니다.

1. 프로젝트 템플릿으로 **Windows의 CMake 프로젝트**를 선택합니다.

1. 디바이스 SDK를 포함하는 방법으로 **Via Vcpkg**를 선택합니다.

VS Code에서 기본 C 코드를 생성하고, 해당 파일을 **modelcode** 폴더의 **sensorbox_app** 폴더에 저장합니다. VS Code에서 생성된 코드 파일이 포함된 새 창이 열립니다.

## <a name="update-the-generated-code"></a>생성된 코드 업데이트

코드를 빌드하고 실행하기 전에 스텁 속성, 원격 분석 및 명령을 구현해야 합니다.

VS Code에서 스텁 코드에 대한 구현을 제공하려면 다음을 수행합니다.

1. **SensorboxModel_impl.c** 파일을 엽니다.

1. 스텁 함수를 구현하는 코드를 추가합니다.

1. 변경 내용을 저장합니다.

## <a name="build-the-code"></a>코드 빌드

Azure IoT Hub를 사용하여 IoT 플러그 앤 플레이 디바이스를 테스트하는 코드를 실행하기 전에 해당 코드를 컴파일해야 합니다.

**sensorbox_app** 폴더의 **Readme.md** 파일에 있는 지침에 따라 Windows에서 코드를 빌드하고 실행합니다. 다음 섹션에는 디바이스 코드를 실행할 때 사용할 디바이스 연결 문자열을 검색하는 방법에 대한 지침이 포함되어 있습니다.

## <a name="test-the-code"></a>코드 테스트

코드를 실행하면 IoT Hub에 연결되고 샘플 원격 분석 및 속성 값 보내기가 시작됩니다. 또한 디바이스는 IoT Hub에서 보낸 명령에 응답합니다. 이 동작을 확인하려면 다음을 수행합니다.

1. IoT Hub를 만듭니다.

    ```azurecli-interactive
    az group create --name environmentalsensorresources --location centralus
    az iot hub create --name {your iot hub name} \
      --resource-group environmentalsensorresources --sku F1
    ```

1. 디바이스를 IoT Hub에 추가하고 해당 연결 문자열을 검색합니다.

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {your iot hub name} --device-id MyPnPDevice
    az iot hub device-identity show-connection-string --hub-name {your iot hub name} --device-id MyPnPDevice --output table
    ```

    연결 문자열을 기록해 둡니다.

1. 명령 프롬프트에서 SDK 및 샘플이 빌드된 **azure-iot-sdk-c** 폴더로 이동합니다. 그런 다음, **cmake\\sensorbox_app\\Release** 폴더로 이동합니다.

1. 다음 명령 실행:

    ```cmd
    sensorbox_app.exe {your device connection string}
    ```

1. Azure IoT 탐색기 도구를 사용하여 IoT Hub에 연결된 IoT 플러그 앤 플레이 디바이스와 상호 작용합니다. 자세한 내용은 [Azure IoT 탐색기 설치 및 사용](./howto-install-iot-explorer.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 인증 준비가 된 IoT 플러그 앤 플레이를 구축했으므로 다음을 수행하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [인증을 위해 준비된 디바이스 빌드](tutorial-build-device-certification.md)
