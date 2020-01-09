---
title: 즉시 인증 가능한 IoT 플러그 앤 플레이 미리 보기 디바이스 빌드 | Microsoft Docs
description: 디바이스 개발자로서 즉시 인증 가능한 IoT 플러그 앤 플레이 미리 보기 디바이스를 빌드하는 방법을 알아봅니다.
author: tbhagwat3
ms.author: tanmayb
ms.date: 12/28/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 43fc928b1274159839dc0df395e86d065f84b4c7
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/31/2019
ms.locfileid: "75550269"
---
# <a name="build-an-iot-plug-and-play-preview-device-thats-ready-for-certification"></a>즉시 인증 가능한 IoT 플러그 앤 플레이 미리 보기 디바이스 빌드

이 자습서에서는 디바이스 개발자로서 즉시 인증 가능한 IoT 플러그 앤 플레이 미리 보기 디바이스를 빌드하는 방법을 설명합니다.

인증 테스트에서는 다음과 같은 내용을 확인합니다.

- IoT 플러그 앤 플레이 디바이스 코드가 디바이스에 설치됩니다.
- IoT 플러그 앤 플레이 디바이스 코드가 Azure IoT SDK를 사용하여 빌드됩니다.
- 디바이스 코드가 [Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md)를 지원합니다.
- 디바이스 코드가 디바이스 정보 인터페이스를 구현합니다.
- 기능 모델 및 디바이스 코드가 IoT Central과 호환됩니다.

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

- [Visual Studio Code](https://code.visualstudio.com/download)
- [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 확장 팩

[빠른 시작: 디바이스 기능 모델을 사용하여 디바이스 만들기](quickstart-create-pnp-device-windows.md)에서 만든 IoT 플러그 앤 플레이 디바이스도 필요합니다.

## <a name="store-a-capability-model-and-interfaces"></a>기능 모델 및 인터페이스 저장

IoT 플러그 앤 플레이 디바이스의 경우 디바이스 기능을 JSON 파일로 정의하는 기능 모델과 인터페이스를 작성해야 합니다.

이러한 JSON 파일은 다음 세 위치에 저장할 수 있습니다.

- 공용 모델 리포지토리
- 회사 모델 리포지토리
- 본인의 디바이스

현재는 디바이스를 인증하려면 회사 모델 리포지토리 또는 공용 모델 리포지토리에 파일을 저장해야 합니다.

## <a name="include-the-required-interfaces"></a>필요한 인터페이스 포함

인증 프로세스를 통과하려면 기능 모델에 **디바이스 정보** 인터페이스를 포함하고 구현해야 합니다. 이 인터페이스에는 다음과 같은 ID가 있습니다.

```json
"@id": "urn:azureiot:DeviceManagement:DeviceInformation:1"
```

> [!NOTE]
> [빠른 시작: 디바이스 기능 모델을 사용하여 디바이스 만들기](quickstart-create-pnp-device-windows.md)를 완료하셨다면 이미 모델에 **디바이스 정보** 인터페이스가 포함되어 있습니다.

디바이스 모델에 **디바이스 정보** 인터페이스를 포함하려면 기능 모델의 `implements` 속성에 인터페이스 ID를 추가합니다.

```json
{
  "@id": "urn:yourcompanyname:sample:ThermostatDevice:1",
  "@type": "CapabilityModel",
  "displayName": "Thermostat T-1000",
  "implements": [
    "urn:yourcompanyname:sample:Thermostat:1",
    "urn:azureiot:DeviceManagement:DeviceInformation:1"
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

VS Code에서 **디바이스 정보** 인터페이스를 보려면 다음을 수행합니다.

1. **Ctrl+Shift+P**를 사용하여 명령 팔레트를 엽니다.

1. **플러그 앤 플레이**를 입력한 다음, **IoT 플러그 앤 플레이 모델 리포지토리 열기** 명령을 선택합니다. **공용 모델 리포지토리 열기**를 선택합니다. VS Code에서 공용 모델 리포지토리가 열립니다.

1. 공용 모델 리포지토리에서 **인터페이스** 탭을 선택하고, 필터 아이콘을 선택하고, 필터 필드에 **디바이스 정보**를 입력합니다.

1. **디바이스 정보** 인터페이스의 로컬 복사본을 만들려면 필터링된 목록에서 해당 복사본을 선택한 다음, **다운로드**를 선택합니다. VS Code에 인터페이스 파일이 표시됩니다.

Azure CLI를 사용하여 **디바이스 정보** 인터페이스를 보려면 다음을 수행합니다.

1. [Azure IoT CLI 확장을 설치합니다](howto-install-pnp-cli.md).

1. 다음 Azure CLI 명령을 사용하여 디바이스 정보 인터페이스 ID가 있는 인터페이스를 표시합니다.

    ```cmd/sh
    az iot pnp interface show --interface urn:azureiot:DeviceManagement:DeviceInformation:1
    ```

자세한 내용은 [Azure CLI용 Azure IoT 확장 설치 및 사용](howto-install-pnp-cli.md)을 참조하세요.

## <a name="update-device-code"></a>디바이스 코드 업데이트

### <a name="enable-device-provisioning-through-the-azure-iot-device-provisioning-service-dps"></a>Azure IoT DPS(Device Provisioning Service)를 통해 디바이스 프로비저닝 사용

디바이스를 프로비저닝하려면 [Azure IoT DPS(Device Provisioning Service)](https://docs.microsoft.com/azure/iot-dps/about-iot-dps)를 통해 디바이스 프로비저닝을 사용하도록 설정해야 합니다. DPS를 사용하는 기능을 추가하려면 VS Code에서 C 코드 스텁을 생성하면 됩니다. 다음 단계를 수행하세요.

1. VS Code에서 DCM 파일이 들어 있는 폴더를 열고, **Ctrl+Shift+P**를 사용하여 명령 팔레트를 열고, **IoT 플러그 앤 플레이**를 입력하고, **디바이스 코드 스텁 생성**을 선택합니다.

1. 디바이스 코드 스텁을 생성하는 데 사용할 DCM 파일을 선택합니다.

1. 프로젝트 이름을 입력합니다. 이 이름이 디바이스 애플리케이션의 이름입니다.

1. 언어로 **ANSI C**를 선택합니다.

1. 연결 방법으로 **DPS(Device Provisioning Service) 대칭 키를 통해**를 선택합니다.

1. 디바이스 OS에 따라 프로젝트 템플릿으로 **Windows의 CMake 프로젝트** 또는 **Linux의 CMake 프로젝트**를 선택합니다.

1. 생성된 디바이스 코드 스텁 파일이 포함된 VS Code 새 창이 열립니다.

1. 코드를 빌드한 후 DPS 자격 증명(**DPS ID 범위**, **DPS 대칭 키** **디바이스 ID**)을 애플리케이션의 매개 변수로 입력합니다. 인증 포털에서 자격 증명을 가져오려면 [IoT 플러그 앤 플레이 디바이스 연결 및 테스트](tutorial-certification-test.md#connect-and-discover-interfaces)를 참조하세요.

    ```cmd/sh
    .\your_pnp_app.exe [DPS ID Scope] [DPS symmetric key] [device ID]
    ```

### <a name="implement-standard-interfaces"></a>표준 인터페이스 구현

#### <a name="implement-the-model-information-and-sdk-information-interfaces"></a>모델 정보 및 SDK 정보 인터페이스 구현

Azure IoT 디바이스 SDK는 모델 정보 및 SDK 정보 인터페이스를 구현합니다. VS Code의 코드 생성 함수를 사용하는 경우 디바이스 코드에서는 IoT 플러그 앤 플레이 디바이스 SDK를 사용합니다.

Azure IoT 디바이스 SDK를 사용하지 않기로 선택한 경우 SDK 소스 코드를 구현의 참조로 사용할 수 있습니다.

#### <a name="implement-the-device-information-interface"></a>디바이스 정보 인터페이스 구현

디바이스에서 **디바이스 정보** 인터페이스를 구현하고 런타임에 디바이스에서 디바이스 관련 정보를 제공합니다.

[Linux](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview)용 **디바이스 정보** 인터페이스의 구현 예제를 참조로 사용할 수 있습니다.

### <a name="implement-all-the-capabilities-defined-in-your-model"></a>모델에 정의된 모든 기능 구현

인증하는 동안 인터페이스에 정의된 기능을 구현할 수 있도록 디바이스를 프로그래밍 방식으로 테스트하게 됩니다. 디바이스에서 구현하지 않는 경우 HTTP 상태 코드 501을 사용하여 읽기-쓰기 속성 및 명령 요청에 응답합니다.

## <a name="next-steps"></a>다음 단계

인증 준비가 완료된 IoT 플러그 앤 플레이 디바이스를 빌드했으므로, 그 다음으로 수행할 단계는 다음과 같습니다.

> [!div class="nextstepaction"]
> [디바이스 인증 방법 알아보기](tutorial-certification-test.md)
