---
title: 포함 파일
description: 포함 파일
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: c6e57d5094f455983b8b474b6930f628d654e457
ms.sourcegitcommit: e45b2aa85063d33853560ec4bc867f230c1c18ce
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43371011"
---
첫 번째 시나리오에서 Contoso의 기존 **냉각기** 장치 유형에 새 원격 분석 유형을 추가합니다.

두 번째 시나리오에서 Contoso는 새 스마트 전구 장치를 테스트하려고 합니다. 테스트를 실행하려면 다음 특징을 가진 시뮬레이션된 새 장치를 만듭니다.

*속성*

| Name                     | 값                      |
| ------------------------ | --------------------------- |
| 색                    | 흰색, 빨강, 파랑            |
| 밝기               | 0~100                    |
| 남은 예상 수명 | 10,000시간에서 카운트다운 |

*원격 분석*

다음 표는 전구에서 데이터 스트림으로 클라우드에 보고하는 데이터를 보여 줍니다.

| Name   | 값      |
| ------ | ----------- |
| 상태 | "켜짐", "꺼짐" |
| 온도 | 화씨 도 |
| 온라인 | true, false |

> [!NOTE]
> **온라인** 원격 분석 값은 모든 시뮬레이트된 형식에 필수입니다.

*메서드*

다음 표는 새 장치에서 지원하는 작업을 보여 줍니다.

| Name        |
| ----------- |
| 켜짐   |
| 꺼짐  |

*초기 상태*

다음 표는 장치의 초기 상태를 보여 줍니다.

| Name                     | 값 |
| ------------------------ | -------|
| 초기 색상            | 흰색  |
| 초기 밝기       | 75     |
| 초기 남은 수명   | 10000 |
| 초기 원격 분석 상태 | "켜짐"   |
| 초기 원격 분석 온도 | 200   |

이 방법 가이드의 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites"></a>필수 조건

이 방법 가이드를 수행하려면 다음이 필요합니다.

* Visual Studio Code. [Mac, Linux 및 Windows용 Visual Studio Code를 다운로드](https://code.visualstudio.com/download)할 수 있습니다.
* .NET Core [Mac, Linux 및 Windows용 .NET Core를 다운로드](https://www.microsoft.com/net/download)할 수 있습니다.
* Postman [Mac, Windows 또는 Linux용 Postman](https://www.getpostman.com/apps)을 다운로드할 수 있습니다.
* [Azure 구독에 배포된 IoT 허브](../articles/iot-hub/iot-hub-create-through-portal.md) 이 가이드의 단계를 완료하려면 IoT 허브의 연결 문자열이 필요합니다. Azure Portal에서 연결 문자열을 가져올 수 있습니다.
* SQL API를 사용하고 [강력한 일관성](../articles/cosmos-db/manage-account.md)에 대해 구성된 Cosmos DB 데이터베이스 이 가이드의 단계를 완료하려면 Cosmos DB 데이터베이스의 연결 문자열이 필요합니다. Azure Portal에서 연결 문자열을 가져올 수 있습니다.

## <a name="prepare-your-development-environment"></a>개발 환경 준비

개발 환경을 준비하려면 다음 작업을 완료합니다.

* 장치 시뮬레이션 마이크로 서비스에 대한 소스를 다운로드합니다.
* 저장소 어댑터 마이크로 서비스에 대한 소스를 다운로드합니다.
* 저장소 어댑터 마이크로 서비스를 로컬로 실행합니다.

이 문서의 지침에서는 Windows를 사용한다고 가정합니다. 다른 운영 체제를 사용하는 경우 일부 파일 경로 및 명령을 사용자 환경에 맞게 조정해야 합니다.

### <a name="download-the-microservices"></a>마이크로 서비스 다운로드

GitHub에서 [저장소 어댑터 마이크로 서비스](https://github.com/Azure/pcs-storage-adapter-dotnet/archive/master.zip)를 로컬 머신의 적합한 위치에 다운로드하고 압축을 풉니다.

GitHub에서 [장치 시뮬레이션 마이크로 서비스](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)를 로컬 머신의 적합한 위치에 다운로드하고 압축을 풉니다.

### <a name="run-the-storage-adapter-microservice"></a>저장소 어댑터 마이크로 서비스 실행

Visual Studio Code에서 **pcs-storage-adapter-dotnet-master** 폴더를 엽니다. **복원** 단추를 클릭하여 확인할 수 없는 종속성을 수정합니다.

**.vscode/launch.json** 파일을 열고 Cosmos DB 연결 문자열을 **PCS_STORAGEADAPTER_DOCUMENTDB_CONNSTRING** 환경 변수에 할당합니다.

<!-- Open the **WebService/appsettings.ini** file and assign your Cosmos DB connection string to the **documentdb_connstring** configuration setting.-->

마이크로 서비스를 로컬로 실행하려면 **디버그 > 디버그 시작**을 클릭합니다.

Visual Studio Code에서 **터미널** 창은 웹 서비스 상태 확인에 대한 URL을 포함하여 실행 중인 마이크로 서비스에서 출력을 표시합니다. [http://127.0.0.1:9022/v1/status](http://127.0.0.1:9022/v1/status) 이 주소로 이동하면 상태는 "OK: 활성 및 양호"이어야 합니다.

다음 단계를 완료하는 동안 Visual Studio Code의 이 인스턴스에서 실행되는 저장소 어댑터 마이크로 서비스를 유지합니다.

## <a name="modify-the-chiller"></a>냉각기 수정

이 섹션에서는 새 **내부 온도** 원격 분석 유형을 기존 **냉각기** 장치 유형에 추가합니다.

1. 로컬 머신에 새 폴더 **C:\temp\devicemodels**를 만듭니다.

1. 장치 시뮬레이션 마이크로 서비스의 다운로드된 복사본에서 새 폴더로 다음 파일을 복사합니다.

    | 원본 | 대상 |
    | ------ | ----------- |
    | Services\Data\devicemodels\chiller-01.json | C:\temp\devicemodels\chiller-01.json |
    | Services\Data\devicemodels\scripts\chiller-01-state.js | C:\temp\devicemodels\scripts\chiller-01-state.js |
    | Services\Data\devicemodels\scripts\Reboot-method.js | C:\temp\devicemodels\scripts\Reboot-method.js |
    | Services\Data\devicemodels\scripts\FirmwareUpdate-method.js | C:\temp\devicemodels\scripts\FirmwareUpdate-method.js |
    | Services\Data\devicemodels\scripts\EmergencyValveRelease-method.js | C:\temp\devicemodels\scripts\EmergencyValveRelease-method.js |
    | Services\Data\devicemodels\scripts\IncreasePressure-method.js | C:\temp\devicemodels\scripts\IncreasePressure-method.js |

1. **C:\temp\devicemodels\chiller-01.json** 파일을 엽니다.

1. 다음과 같이 **SchemaVersion** 값을 업데이트합니다.

    ```json
    "SchemaVersion": "1.0.0",
    ```

1. **InitialState** 섹션에서 다음 두 정의를 추가합니다.

    ```json
    "internal_temperature": 65.0,
    "internal_temperature_unit": "F",
    ```

1. **Telemetry** 배열에서 다음 정의를 추가합니다.

    ```json
    {
      "Interval": "00:00:05",
      "MessageTemplate": "{\"internal_temperature\":${internal_temperature},\"internal_temperature_unit\":\"${internal_temperature_unit}\"}",
      "MessageSchema": {
        "Name": "chiller-internal-temperature;v1",
        "Format": "JSON",
        "Fields": {
          "temperature": "double",
          "temperature_unit": "text"
        }
      }
    },
    ```

1. **C:\temp\devicemodels\chiller-01.json** 파일을 저장합니다.

1. **C:\temp\devicemodels\scripts\chiller-01-state.js** 파일을 엽니다.

1. **state** 변수에 다음 필드를 추가합니다.

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. **주** 함수를 다음과 같이 업데이트합니다.

    ```js
    function main(context, previousState, previousProperties) {

        // Restore the global state before generating the new telemetry, so that
        // the telemetry can apply changes using the previous function state.
        restoreSimulation(previousState, previousProperties);

        // 75F +/- 5%,  Min 25F, Max 100F
        state.temperature = vary(75, 5, 25, 100);

        // 70% +/- 5%,  Min 2%, Max 99%
        state.humidity = vary(70, 5, 2, 99);

        // 65F +/- 2%,  Min 15F, Max 125F
        state.internal_temperature = vary(65, 2, 15, 125);

        log("Simulation state: " + state.simulation_state);
        if (state.simulation_state === "high_pressure") {
            // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
            state.pressure = vary(250, 25, 50, 300);
        } else {
            // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
            state.pressure = vary(150, 10, 50, 300);
        }

        updateState(state);
        return state;
    }
    ```

1. **C:\temp\devicemodels\scripts\chiller-01-state.js** 파일을 저장합니다.

## <a name="create-the-lightbulb"></a>전구 만들기

이 섹션에서는 새 **전구** 장치 유형을 정의합니다.

1. **C:\temp\devicemodels\lightbulb-01.json** 파일을 만들고 다음 콘텐츠를 추가합니다.

    ```json
    {
      "SchemaVersion": "1.0.0",
      "Id": "lightbulb-01",
      "Version": "0.0.1",
      "Name": "Lightbulb",
      "Description": "Smart lightbulb device.",
      "Protocol": "MQTT",
      "Simulation": {
        "InitialState": {
          "online": true,
          "temperature": 200.0,
          "temperature_unit": "F",
          "status": "on"
        },
        "Interval": "00:00:20",
        "Scripts": [
          {
            "Type": "javascript",
            "Path": "lightbulb-01-state.js"
          }
        ]
      },
      "Properties": {
        "Type": "Lightbulb",
        "Color": "White",
        "Brightness": 75,
        "EstimatedRemainingLife": 10000
      },
      "Tags": {
        "Location": "Building 2",
        "Floor": "2",
        "Campus": "Redmond"
      },
      "Telemetry": [
        {
          "Interval": "00:00:20",
          "MessageTemplate": "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"status\":\"${status}\"}",
          "MessageSchema": {
            "Name": "lightbulb-status;v1",
            "Format": "JSON",
            "Fields": {
              "temperature": "double",
              "temperature_unit": "text",
              "status": "text"
            }
          }
        }
      ],
      "CloudToDeviceMethods": {
        "SwitchOn": {
          "Type": "javascript",
          "Path": "SwitchOn-method.js"
        },
        "SwitchOff": {
          "Type": "javascript",
          "Path": "SwitchOff-method.js"
        }
      }
    }
    ```

    **C:\temp\devicemodels\lightbulb-01.json**에 변경 내용을 저장합니다.

1. **C:\temp\devicemodels\scripts\lightbulb-01-state.js** 파일을 만들고 다음 콘텐츠를 추가합니다.

    ```javascript
    "use strict";

    // Default state
    var state = {
      online: true,
      temperature: 200.0,
      temperature_unit: "F",
      status: "on"
    };

    // Default device properties
    var properties = {};

    /**
     * Restore the global state using data from the previous iteration.
     *
     * @param previousState device state from the previous iteration
     * @param previousProperties device properties from the previous iteration
     */
    function restoreSimulation(previousState, previousProperties) {
      // If the previous state is null, force a default state
      if (previousState) {
        state = previousState;
      } else {
        log("Using default state");
      }

      if (previousProperties) {
        properties = previousProperties;
      } else {
        log("Using default properties");
      }
    }

    /**
     * Simple formula generating a random value around the average
     * in between min and max
     *
     * @returns random value with given parameters
     */
    function vary(avg, percentage, min, max) {
      var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
      value = Math.max(value, min);
      value = Math.min(value, max);
      return value;
    }

    /**
     * Simple formula that sometimes flips the status of the lightbulb
     */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }

    /**
     * Entry point function called by the simulation engine.
     * Returns updated simulation state.
     * Device property updates must call updateProperties() to persist.
     *
     * @param context             The context contains current time, device model and id
     * @param previousState       The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState, previousProperties) {

      // Restore the global device properties and the global state before
      // generating the new telemetry, so that the telemetry can apply changes
      // using the previous function state.
      restoreSimulation(previousState, previousProperties);

      state.temperature = vary(200, 5, 150, 250);

      // Make this flip every so often
      state.status = flip(state.status);

      updateState(state);

      return state;
    }
    ```

    **C:\temp\devicemodels\scripts\lightbulb-01-state.js**에 변경 내용을 저장합니다.

1. **C:\temp\devicemodels\scripts\SwitchOn-method.js** 파일을 만들고 다음 콘텐츠를 추가합니다.

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch On method.");
      state.status = "on";
      updateState(state);
    }
    ```

    **C:\temp\devicemodels\scripts\SwitchOn-method.js**에 변경 내용을 저장합니다.

1. **C:\temp\devicemodels\scripts\SwitchOff-method.js** 파일을 만들고 다음 콘텐츠를 추가합니다.

    ```javascript
    "use strict";

    // Default state
    var state = {
      status: "on"
    };

    /**
     * Entry point function called by the method.
     *
     * @param context        The context contains current time, device model and id
     * @param previousState  The device state since the last iteration
     * @param previousProperties  The device properties since the last iteration
     */
    function main(context, previousState) {
      log("Executing lightbulb Switch Off method.");
      state.status = "off";
      updateState(state);
    }
    ```

    **C:\temp\devicemodels\scripts\SwitchOff-method.js**에 변경 내용을 저장합니다.

**냉각기** 장치 유형의 사용자 지정된 버전을 만들고 새 **전구** 장치 유형을 만들었습니다.

## <a name="test-the-devices"></a>장치 테스트

이 섹션에서는 이전 섹션에서 만든 장치 유형을 로컬로 테스트합니다.

### <a name="run-the-device-simulation-microservice"></a>장치 시뮬레이션 마이크로 서비스 실행

Visual Studio Code의 새 인스턴스에 GitHub에서 다운로드한 **device-simulation-dotnet-master** 폴더를 엽니다. **복원** 단추를 클릭하여 확인할 수 없는 종속성을 수정합니다.

**.vscode/launch.json** 파일을 열고 IoT Hub 연결 문자열을 **PCS_IOTHUB_CONNSTRING** 환경 변수에 할당합니다.

**WebService/Properties/launchSettings.json** 파일을 열고 IoT Hub 연결 문자열을 **PCS_IOTHUB_CONNSTRING** 환경 변수에 할당합니다.

**WebService/appsettings.ini** 파일을 열고 다음과 같이 설정을 수정합니다.

```ini
device_models_folder = C:\temp\devicemodels\

device_models_scripts_folder = C:\temp\devicemodels\scripts\
```

마이크로 서비스를 로컬로 실행하려면 **디버그 > 디버그 시작**을 클릭합니다.

Visual Studio Code에서 **터미널** 창은 실행 중인 마이크로 서비스에서 출력을 표시합니다.

다음 단계를 완료하는 동안 Visual Studio Code의 이 인스턴스에서 실행되는 장치 시뮬레이션 마이크로 서비스를 유지합니다.

### <a name="set-up-a-monitor-for-device-events"></a>장치 이벤트에 대한 모니터 설정

이 섹션에서는 Azure CLI를 사용하여 IoT 허브에 연결된 장치에서 보낸 원격 분석을 보도록 이벤트 모니터를 설정합니다.

다음 스크립트에서는 IoT 허브의 이름이 **device-simulation-test**라고 가정합니다.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-cli-iot-ext

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

시뮬레이션된 장치를 테스트하는 동안 이벤트 모니터가 실행되도록 둡니다.

### <a name="create-a-simulation-with-the-updated-chiller-device-type"></a>업데이트된 냉각기 장치 유형으로 시뮬레이션 만들기

이 섹션에서는 Postman 도구를 사용하여 업데이트된 냉각기 장치 유형을 사용하여 시뮬레이션을 실행하도록 장치 시뮬레이션 마이크로 서비스를 요청합니다. Postman은 웹 서비스에 REST 요청을 보낼 수 있는 도구입니다. 필요한 Postman 구성 파일은 **device-simulation-dotnet** 리포지토리의 로컬 복사본에 있습니다.

Postman을 설정하려면:

1. 로컬 머신에서 Postman을 엽니다.

1. **파일 > 가져오기**를 클릭합니다. 그런 다음, **파일 선택**을 클릭합니다.

1. **device-simulation-dotnet/docs/postman** 폴더로 이동합니다. **Azure IoT Device Simulation solution accelerator.postman_collection** 및 **Azure IoT Device Simulation solution accelerator.postman_environment**를 선택하고 **열기**를 클릭합니다.

1. **Azure IoT 장치 시뮬레이션 솔루션 가속기**를 전송할 수 있는 요청으로 확장합니다.

1. **환경 없음**을 클릭하고 **Azure IoT 장치 시뮬레이션 솔루션 가속기**를 선택합니다.

이제 장치 시뮬레이션 마이크로 서비스와 상호 작용하는 데 사용할 수 있는 Postman 작업 영역에 로드된 컬렉션 및 환경이 있습니다.

시뮬레이션을 구성 및 실행하려면:

1. Postman 컬렉션에서 **수정된 냉각기 시뮬레이션 만들기**를 선택하고 **보내기**를 클릭합니다. 이 요청에서는 네 개의 시뮬레이션된 냉각기 장치 유형의 인스턴스를 만듭니다.

1. Azure CLI 창의 이벤트 모니터 출력은 새 **internal_temperature** 값을 포함하여 시뮬레이션된 장치의 원격 분석을 보여줍니다.

시뮬레이션을 중지하려면 Postman에서 **시뮬레이션 중지** 요청을 선택하고 **보내기**를 클릭합니다.

### <a name="create-a-simulation-with-the-lightbulb-device-type"></a>전구 장치 유형으로 시뮬레이션 만들기

이 섹션에서는 Postman 도구를 사용하여 전구 장치 유형을 사용하여 시뮬레이션을 실행하도록 장치 시뮬레이션 마이크로 서비스를 요청합니다. Postman은 웹 서비스에 REST 요청을 보낼 수 있는 도구입니다.

시뮬레이션을 구성 및 실행하려면:

1. Postman 컬렉션에서 **전구 시뮬레이션 만들기**를 선택하고 **보내기**를 클릭합니다. 이 요청에서는 두 개의 시뮬레이션된 전구 장치 유형의 인스턴스를 만듭니다.

1. Azure CLI 창의 이벤트 모니터 출력은 시뮬레이션된 전구의 원격 분석을 보여줍니다.

시뮬레이션을 중지하려면 Postman에서 **시뮬레이션 중지** 요청을 선택하고 **보내기**를 클릭합니다.

## <a name="clean-up-resources"></a>리소스 정리

해당 Visual Studio Code 인스턴스에서 로컬로 실행 중인 두 마이크로 서비스를 중지할 수 있습니다(**디버그 > 디버그 중지**).

IoT Hub 및 Cosmos DB 인스턴스가 더 이상 필요하지 않은 경우 불필요한 요금 청구를 방지하기 위해 Azure 구독에서 삭제합니다.