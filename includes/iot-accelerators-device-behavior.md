---
title: 포함 파일
description: 포함 파일
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 0b2b6814ad0b55d8f56998f6d4c9b6bb88663e04
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444248"
---
## <a name="state-behavior"></a>상태 동작

디바이스 모델 스키마의 [시뮬레이션](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) 섹션에서는 시뮬레이션된 디바이스의 내부 상태를 정의합니다.

- `InitialState`는 디바이스 상태 개체의 모든 속성에 대한 초기 값을 정의합니다.
- `Script`는 디바이스 상태를 업데이트하기 위해 일정에 따라 실행되는 JavaScript 파일을 식별합니다.

다음 예제에서는 시뮬레이션된 냉각기 디바이스에 대한 디바이스 상태 개체의 정의를 보여줍니다.

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

`InitialState` 섹션에 정의된 시뮬레이션된 디바이스의 상태는 시뮬레이션 서비스에 의해 메모리에 보관됩니다. 상태 정보는 **chiller-01-state.js**에 정의된 `main` 함수에 입력으로 전달됩니다. 이 예제에서는 시뮬레이션 서비스가 5초마다 **chiller-01-state.js** 파일을 실행합니다. 스크립트는 시뮬레이션된 디바이스의 상태를 수정할 수 있습니다.

다음은 일반적인 `main` 함수의 개요를 보여 줍니다.

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context` 매개 변수에는 다음 속성이 있습니다.

- `currentTime` - `yyyy-MM-dd'T'HH:mm:sszzz` 형식의 문자열
- `deviceId`(예: `Simulated.Chiller.123`)
- `deviceModel`(예: `Chiller`)

`state` 매개 변수에는 디바이스 시뮬레이션 서비스에서 유지 관리되는 디바이스 상태가 포함됩니다. 이 값은 이전 `main` 호출에서 반환된 `state` 개체입니다.

다음 예제에서는 시뮬레이션 서비스에서 유지 관리하는 디바이스 상태를 처리하기 위한 `main` 메서드의 일반적인 구현을 보여줍니다.

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

다음 예제에서는 `main` 메서드가 시간에 따라 달라지는 원격 분석 값을 시뮬레이트할 수 있는 방법을 보여 줍니다.

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

GitHub에서 전체 [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js)를 볼 수 있습니다.

## <a name="method-behavior"></a>메서드 동작

디바이스 모델 스키마의 [CloudToDeviceMethods](../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) 섹션에서는 시뮬레이션된 디바이스가 응답하는 메서드를 정의합니다.

다음 예제에서는 시뮬레이션된 냉각기 디바이스가 지원하는 메서드 목록을 보여줍니다.

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

각 메서드에는 메서드의 동작을 구현하는 관련 JavaScript 파일이 있습니다.

스키마의 `InitialState` 섹션에 정의된 시뮬레이션된 디바이스의 상태는 시뮬레이션 서비스에 의해 메모리에 보관됩니다. 상태 정보는 메서드가 호출될 때 JavaScript 파일에 정의된 `main` 함수에 입력으로 전달됩니다. 스크립트는 시뮬레이션된 디바이스의 상태를 수정할 수 있습니다.

다음은 일반적인 `main` 함수의 개요를 보여 줍니다.

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` 매개 변수에는 다음 속성이 있습니다.

- `currentTime` - `yyyy-MM-dd'T'HH:mm:sszzz` 형식의 문자열
- `deviceId`(예: `Simulated.Chiller.123`)
- `deviceModel`(예: `Chiller`)

`state` 매개 변수에는 디바이스 시뮬레이션 서비스에서 유지 관리되는 디바이스 상태가 포함됩니다.

`properties` 매개 변수는 IoT Hub 디바이스 쌍에 보고된 속성으로 기록된 디바이스의 속성을 포함합니다.

메서드의 동작을 구현하는 데 사용할 수 있는 세 가지 전역 함수가 있습니다.

- `updateState` - 시뮬레이션 서비스에 보유된 상태를 업데이트합니다.
- `updateProperty` - 단일 장치 속성을 업데이트합니다.
- `sleep` - 장기 실행 작업을 시뮬레이트하기 위해 실행을 일시 중지합니다.

다음 예제에서는 시뮬레이션된 냉각기 디바이스가 사용하는 **IncreasePressure-method.js** 스크립트의 축약된 버전을 보여줍니다.

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>스크립트 파일 디버깅

디바이스 시뮬레이션 서비스에서 상태 및 메서드 스크립트를 실행하는 데 사용하는 Javascript 인터프리터에 디버거를 연결할 수는 없습니다. 그러나 서비스 로그에 정보를 기록할 수 있습니다. 기본 제공 `log()` 함수를 사용하면 정보를 저장하여 함수 실행을 추적 및 디버그할 수 있습니다.

구문 오류가 있으면 인터프리터가 실패하고 서비스 로그에 `Jint.Runtime.JavaScriptException` 항목을 씁니다.

GitHub의 [서비스를 로컬로 실행](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) 문서에서는 디바이스 시뮬레이션 서비스를 로컬로 실행하는 방법을 보여 줍니다. 서비스를 로컬로 실행하면 시뮬레이션된 디바이스를 클라우드에 배포하기 전에 쉽게 디버그할 수 있습니다.