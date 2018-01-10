---
title: "원격 모니터링 솔루션에서 장치 시뮬레이션 - Azure | Microsoft Docs"
description: "이 자습서는 미리 구성된 원격 모니터링 솔루션으로 장치 시뮬레이터를 사용하는 방법을 보여 줍니다."
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 12/12/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 7550748c496f4e5c671ab49f9b139d2d4926d497
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/13/2017
---
# <a name="test-your-solution-with-simulated-devices"></a>시뮬레이트된 장치로 솔루션 테스트

이 자습서는 미리 구성된 원격 모니터링 솔루션에서 장치 시뮬레이터 마이크로 서비스를 사용자 지정하는 방법을 보여 줍니다. 장치 시뮬레이터의 기능을 표시하기 위해 이 자습서에서는 Contoso IoT 응용 프로그램에서 두 가지 시나리오를 사용합니다.

첫 번째 시나리오에서 Contoso는 새 스마트 전구 장치를 테스트하려고 합니다. 테스트를 수행하려면 다음 특징을 가진 시뮬레이트된 새 장치를 만듭니다.

*속성*

| 이름                     | 값                      |
| ------------------------ | --------------------------- |
| 색                    | 흰색, 빨강, 파랑            |
| 밝기               | 0~100                    |
| 남은 예상 수명 | 10,000시간에서 카운트다운 |

*원격 분석*

다음 표는 전구에서 데이터 스트림으로 클라우드에 보고하는 데이터를 보여 줍니다.

| 이름   | 값      |
| ------ | ----------- |
| 가동 상태 | "켜짐", "꺼짐" |
| 온라인 | true, false |

> [!NOTE]
> **온라인** 원격 분석 값은 모든 시뮬레이트된 형식에 필수입니다.

*메서드*

다음 표는 새 장치에서 지원하는 작업을 보여 줍니다.

| 이름        |
| ----------- |
| 켜짐   |
| 꺼짐  |

*초기 상태*

다음 표는 장치의 초기 상태를 보여 줍니다.

| 이름                     | 값 |
| ------------------------ | -------|
| 초기 색상            | 흰색  |
| 초기 밝기       | 75     |
| 초기 남은 수명   | 10000 |
| 초기 원격 분석 상태 | "켜짐"   |

두 번째 시나리오에서 Contoso의 기존 **냉각기** 장치에 새 원격 분석 유형을 추가합니다.

이 자습서는 미리 구성된 원격 모니터링 솔루션으로 장치 시뮬레이터를 사용하는 방법을 보여 줍니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

>[!div class="checklist"]
> * 새 장치 유형 만들기
> * 사용자 지정 장치 동작 시뮬레이트
> * 대시보드에 새 장치 유형 추가
> * 기존의 장치 유형에서 사용자 지정 원격 분석 보내기

## <a name="prerequisites"></a>필수 조건

이 자습서를 수행하려면 Azure 구독에서 원격 모니터링 솔루션의 배포된 인스턴스가 필요합니다.

원격 모니터링 솔루션을 아직 배포하지 않은 경우 [미리 구성된 원격 모니터링 솔루션 배포](iot-suite-remote-monitoring-deploy.md) 자습서를 완료해야 합니다.

<!-- Dominic please this use as your reference https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models -->

## <a name="the-device-simulation-service"></a>장치 시뮬레이션 서비스

미리 구성된 솔루션의 장치 시뮬레이션 서비스를 사용하면 시뮬레이트된 기본 제공 장치 유형을 변경하고 시뮬레이트된 새 장치 유형을 만들 수 있습니다. 솔루션에 물리적 장치를 연결하기 전에 사용자 지정 장치 유형을 사용하여 원격 모니터링 솔루션의 동작을 테스트할 수 있습니다.

## <a name="create-a-simulated-device-type"></a>시뮬레이트된 장치 유형 만들기

시뮬레이션 마이크로 서비스에서 새 장치 유형을 만드는 가장 쉬운 방법은 기존 유형을 복사하고 수정하는 것입니다. 다음 단계에서는 기본 제공 **냉각기** 장치를 복사하여 새 **전구** 장치를 만드는 방법을 보여 줍니다.

1. 다음 명령을 사용하여 로컬 컴퓨터에 **장치-시뮬레이션** GitHub 리포지토리를 복제합니다.

    ```cmd/sh
    git clone https://github.com/Azure/device-simulation-dotnet.git
    ```

1. 각 장치 유형에는 `Services/data/devicemodels` 폴더에 JSON 모델 파일 및 관련된 스크립트가 있습니다. **냉각기** 파일을 복사하여 다음 표에 나와 있는 것처럼 **전구** 파일을 만듭니다.

    | 원본                      | 대상                   |
    | --------------------------- | ----------------------------- |
    | chiller-01.json             | lightbulb-01.json             |
    | scripts/chiller-01-state.js | scripts/lightbulb-01-state.js |
    | scripts/reboot-method.js    | scripts/SwitchOn-method.js    |

### <a name="define-the-characteristics-of-the-new-device-type"></a>새 장치 유형의 특성 정의

`lightbulb-01.json` 파일은 생성하는 원격 분석 및 지원하는 메서드와 같은 유형의 특성을 정의합니다. 다음 단계는 `lightbulb-01.json` 파일을 업데이트하여 **전구** 장치를 정의합니다.

1. `lightbulb-01.json` 파일에서 다음 코드 조각과 같이 장치 메타데이터를 업데이트합니다.

    ```json
    "SchemaVersion": "1.0.0",
    "Id": "lightbulb-01",
    "Version": "0.0.1",
    "Name": "Lightbulb",
    "Description": "Smart lightbulb device.",
    "Protocol": "MQTT",
    ```

1. `lightbulb-01.json` 파일에서 다음 코드 조각과 같이 시뮬레이션 정의를 업데이트합니다.

    ```json
    "Simulation": {
      "InitialState": {
        "online": true,
        "status": "on"
      },
      "Script": {
        "Type": "javascript",
        "Path": "lightbulb-01-state.js",
        "Interval": "00:00:20"
      }
    },
    ```

1. `lightbulb-01.json` 파일에서 다음 코드 조각과 같이 장치 유형 속성을 업데이트합니다.

    ```json
    "Properties": {
      "Type": "Lightbulb",
      "Color": "White",
      "Brightness": 75,
      "EstimatedRemainingLife": 10000
    },
    ```

1. `lightbulb-01.json` 파일에서 다음 코드 조각과 같이 장치 유형 원격 분석 정의를 업데이트합니다.

    ```json
    "Telemetry": [
      {
        "Interval": "00:00:20",
        "MessageTemplate": "{\"status\":\"${status}\"}",
        "MessageSchema": {
          "Name": "lightbulb-status;v1",
          "Format": "JSON",
          "Fields": {
            "status": "text"
          }
        }
      }
    ],
    ```

1. `lightbulb-01.json` 파일에서 다음 코드 조각과 같이 장치 유형 메서드를 업데이트합니다.

    ```json
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
    ```

1. `lightbulb-01.json` 파일을 저장합니다.

### <a name="simulate-custom-device-behavior"></a>사용자 지정 장치 동작 시뮬레이트

`scripts/lightbulb-01-state.js` 파일은 **전구** 유형의 시뮬레이션 동작을 정의합니다. 다음 단계는 `scripts/lightbulb-01-state.js` 파일을 업데이트하여 **전구** 장치를 동작을 정의합니다.

1. 다음 코드 조각과 같이 `scripts/lightbulb-01-state.js` 파일에서 상태 정의를 편집합니다.

    ```js
    // Default state
    var state = {
      online: true,
      status: "on"
    };
    ```

1. **vary** 함수를 다음 **flip** 함수로 바꿉니다.

    ```js
    /**
    * Simple formula that sometimes flips the status of the lightbulb
    */
    function flip(value) {
      if (Math.random() < 0.2) {
        return (value == "on") ? "off" : "on"
      }
      return value;
    }
    ```

1. 다음 코드 조각과 같이 **main** 함수를 편집하여 동작을 구현합니다.

    ```js
    function main(context, previousState) {

      // Restore the global state before generating the new telemetry, so that
      // the telemetry can apply changes using the previous function state.
      restoreState(previousState);

      // Make this flip every so often
      state.status = flip(state.status);

      return state;
    }
    ```

1. `scripts/lightbulb-01-state.js` 파일을 저장합니다.

`scripts/SwitchOn-method.js` 파일은 **전구** 장치에서 **Switch On** 메서드를 구현합니다. 다음 단계는 `scripts/SwitchOn-method.js` 파일을 업데이트합니다.

1. 다음 코드 조각과 같이 `scripts/SwitchOn-method.js` 파일에서 상태 정의를 편집합니다.

    ```js
    var state = {
       status: "on"
    };
    ```

1. 전구를 켜려면 다음과 같이 **main** 함수를 편집합니다.

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch On method.");
        state.status = "on";
        updateState(state);
    }
    ```

1. `scripts/SwitchOn-method.js` 파일을 저장합니다.

1. `scripts/SwitchOff-method.js`라는 `scripts/SwitchOn-method.js` 파일의 본사본을 만듭니다.

1. 전구를 끄려면 다음과 같이 `scripts/SwitchOff-method.js` 파일에서 **main** 함수를 편집합니다.

    ```js
    function main(context, previousState) {
        log("Executing lightbulb Switch Off method.");
        state.status = "off";
        updateState(state);
    }
    ```

1. `scripts/SwitchOff-method.js` 파일을 저장합니다.

### <a name="test-the-lightbulb-device-type"></a>전구 장치 유형 테스트

**전구** 장치 유형을 테스트하기 위해 먼저 **장치-시뮬레이션** 서비스의 로컬 복사본을 실행하여 장치 유형이 예상대로 동작하는지 테스트할 수 있습니다. 새 장치 유형을 로컬로 테스트 및 디버그하면 컨테이너를 다시 빌드하고 **장치-시뮬레이션** 서비스를 Azure로 다시 배포할 수 있습니다.

변경 내용을 로컬로 테스트 및 디버그하려면 [장치 시뮬레이션 개요](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md)를 참조하세요.

출력 디렉터리에 새 **전구** 장치 파일을 복사하도록 프로젝트를 구성합니다.

* Visual Studio를 사용하는 경우 이전 섹션에서 만든 4개의 새 전구 파일을 솔루션의 **서비스** 프로젝트에 추가해야 합니다. 그런 다음 **솔루션 탐색기**를 사용하여 이를 출력 디렉터리에 복사하도록 표시합니다.

* Visual Studio Code를 사용하는 경우 **Services.csproj** 파일을 열고 이전 섹션에서 만든 4개의 새 전구 파일을 추가합니다. 예제로 **Services.csproj** 파일의 기존 장치 모델 파일 항목을 참조하세요.

배포된 솔루션에서 새 장치를 테스트하려면 다음 중 하나를 참조하세요.

* [사용자 지정 docker 허브 계정에서 컨테이너 배포](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [수동 복사를 통해 배포된 컨테이너 업데이트](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

**장치** 페이지에서 새 유형의 인스턴스를 프로비전할 수 있습니다.

![사용 가능한 시뮬레이션의 목록 보기](media/iot-suite-remote-monitoring-test/devicesmodellist.png)

시뮬레이트된 장치에서 원격 분석을 볼 수 있습니다.

![전구 원격 분석 보기](media/iot-suite-remote-monitoring-test/devicestelemetry.png)

장치에서 **SwitchOn** 및 **SwitchOff** 메서드를 호출할 수 있습니다.

![전구 메서드 호출](media/iot-suite-remote-monitoring-test/devicesmethods.png)

Azure로 배포를 위해 새 장치 유형으로 Docker 이미지를 작성하려면 [사용자 지정된 Docker 이미지 작성](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image)을 참조하세요.

## <a name="add-a-new-telemetry-type"></a>새 원격 분석 유형 추가

이 섹션에서는 시뮬레이트된 기존 장치 유형을 수정하여 새 원격 분석 유형을 지원하는 방법을 설명합니다.

### <a name="locate-the-chiller-device-type-files"></a>냉각기 장치 유형 파일 찾기

다음 단계에서는 기본 제공 **냉각기** 장치를 정의하는 파일을 찾는 방법을 보여 줍니다.

1. 아직 그렇게 하지 않은 경우 다음 명령을 사용하여 로컬 컴퓨터에 **장치-시뮬레이션** GitHub 리포지토리를 복제합니다.

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git
    ```

1. 각 장치 유형에는 `data/devicemodels` 폴더에 JSON 모델 파일 및 관련된 스크립트가 있습니다. 시뮬레이트된 **냉각기** 장치 유형을 정의하는 파일은 다음과 같습니다.
    * `data/devicemodels/chiller-01.json`
    * `data/devicemodels/scripts/chiller-01-state.js`

### <a name="specify-the-new-telemetry-type"></a>새 원격 분석 유형 지정

다음 단계에서는 **냉각기** 장치 유형에 새 **내부 온도** 유형을 추가하는 방법을 보여 줍니다.

1. `chiller-01.json` 파일을 엽니다.

1. 다음과 같이 **SchemaVersion** 값을 업데이트합니다.

    ```json
    "SchemaVersion": "1.1.0",
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

1. `chiller-01.json` 파일을 저장합니다.

1. `scripts/chiller-01-state.js` 파일을 엽니다.

1. **state** 변수에 다음 필드를 추가합니다.

    ```js
    internal_temperature: 65.0,
    internal_temperature_unit: "F",
    ```

1. **main** 함수에 다음 줄을 추가합니다.

    ```js
    state.internal_temperature = vary(65, 2, 15, 125);
    ```

1. `scripts/chiller-01-state.js` 파일을 저장합니다.

### <a name="test-the-chiller-device-type"></a>냉각기 장치 유형 테스트

업데이트된 **냉각기** 장치 유형을 테스트하기 위해 먼저 **장치-시뮬레이션** 서비스의 로컬 복사본을 실행하여 장치 유형이 예상대로 동작하는지 테스트할 수 있습니다. 업데이트된 장치 유형을 로컬로 테스트 및 디버그하면 컨테이너를 다시 빌드하고 **장치-시뮬레이션** 서비스를 Azure로 다시 배포할 수 있습니다.

**장치-시뮬레이션** 서비스를 로컬로 실행하는 경우 원격 분석 솔루션에 원격 분석을 보냅니다. **장치** 페이지에서 업데이트된 유형의 인스턴스를 프로비전할 수 있습니다.

변경 내용을 로컬로 테스트 및 디버그하려면 [Visual Studio로 서비스 실행](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#running-the-service-with-visual-studio) 또는 [명령줄에서 빌드 및 실행](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#build-and-run-from-the-command-line)을 참조하세요.

배포된 솔루션에서 새 장치를 테스트하려면 다음 중 하나를 참조하세요.

* [사용자 지정 docker 허브 계정에서 컨테이너 배포](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#deploying-containers-from-custom-docker-hub-account)
* [수동 복사를 통해 배포된 컨테이너 업데이트](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#update-a-deployed-container-via-manual-copy)

**장치** 페이지에서 업데이트된 유형의 인스턴스를 프로비전할 수 있습니다.

![업데이트된 냉각기 추가](media/iot-suite-remote-monitoring-test/devicesupdatedchiller.png)

시뮬레이트된 장치에서 **내부 온도** 원격 분석을 볼 수 있습니다.

Azure로 배포를 위해 새 장치 유형으로 Docker 이미지를 작성하려면 [사용자 지정된 Docker 이미지 작성](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md#building-a-customized-docker-image)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서는 다음 방법을 보여 줬습니다.

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 새 장치 유형 만들기
> * 사용자 지정 장치 동작 시뮬레이트
> * 대시보드에 새 장치 유형 추가
> * 기존의 장치 유형에서 사용자 지정 원격 분석 보내기

이제 장치 시뮬레이션 서비스를 사용하는 방법을 배웠으며 제안된 다음 단계는 [물리적 장치를 원격 모니터링 솔루션에 연결](iot-suite-connecting-devices-node.md)하는 방법을 알아보기 위한 것입니다.

원격 모니터링 솔루션에 대한 자세한 개발자 정보는 다음을 참조하세요.

* [개발자 참조 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [개발자 문제 해결 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->