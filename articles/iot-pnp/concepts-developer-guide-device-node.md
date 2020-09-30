---
title: 장치 개발자 가이드 (Node.js)-IoT 플러그 앤 플레이 | Microsoft Docs
description: Node.js 장치 개발자를 위한 IoT 플러그 앤 플레이 설명
author: rido-min
ms.author: rmpablos
ms.date: 09/24/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 12b09cd76a3bda265a3eb610c95fb008af1f3914
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580082"
---
# <a name="iot-plug-and-play-device-developer-guide-nodejs"></a>IoT 플러그 앤 플레이 장치 개발자 가이드 (Node.js)

[!INCLUDE [iot-pnp-device-devguide-selector.md](../../includes/iot-pnp-device-devguide-selector.md)]

[!INCLUDE [iot-pnp-device-devguide-intro.md](../../includes/iot-pnp-device-devguide-intro.md)]

## <a name="model-id-announcement"></a>모델 ID 알림

모델 ID를 알리기 위해 장치는 연결 정보에 해당 ID를 포함 해야 합니다.

```nodejs
const modelIdObject = { modelId: 'dtmi:com:example:Thermostat;1' };
const client = Client.fromConnectionString(deviceConnectionString, Protocol);
await client.setOptions(modelIdObject);
await client.open();
```

> [!TIP]
> 모듈 및 IoT Edge의 경우 대신를 사용 `ModuleClient` `Client` 합니다.

## <a name="dps-payload"></a>DPS 페이로드

[DPS (장치 프로 비전 서비스)](../iot-dps/about-iot-dps.md) 를 사용 하는 장치에는 `modelId` 다음 JSON 페이로드를 사용 하 여 프로 비전 프로세스 중에 사용할가 포함 될 수 있습니다.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>원격 분석, 속성 및 명령 구현

[IoT 플러그 앤 플레이 모델의 구성 요소 이해](concepts-components.md)에서 설명한 대로 장치 빌더는 구성 요소를 사용 하 여 장치를 설명 하는지 결정 해야 합니다. 구성 요소를 사용 하는 경우 장치는이 섹션에 설명 된 규칙을 따라야 합니다.

### <a name="telemetry"></a>원격 분석

기본 구성 요소에는 특별 한 속성이 필요 하지 않습니다.

중첩 된 구성 요소를 사용 하는 경우 장치는 다음과 같은 구성 요소 이름을 가진 메시지 속성을 설정 해야 합니다.

```nodejs
async function sendTelemetry(deviceClient, data, index, componentName) {
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

### <a name="read-only-properties"></a>읽기 전용 속성

기본 구성 요소에서 속성을 보고 하는 경우 특별 한 구문이 필요 하지 않습니다.

```nodejs
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat = createReportPropPatch({
  maxTempSinceLastReboot: 38.7
});

deviceTwin.properties.reported.update(patchThermostat, function (err) {
  if (err) throw err;
});
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

중첩 된 구성 요소를 사용 하는 경우 구성 요소 이름 내에 속성을 만들어야 합니다.

```nodejs
helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat1Info = helperCreateReportedPropertiesPatch({
  maxTempSinceLastReboot: 38.7,
}, 'thermostat1');

deviceTwin.properties.reported.update(patchThermostat1Info, function (err) {
  if (err) throw err;
});
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>쓰기 가능 속성

이러한 속성은 장치에서 설정 하거나 솔루션에서 업데이트할 수 있습니다. 솔루션이 속성을 업데이트 하는 경우 클라이언트는 또는의 콜백으로 알림을 받습니다 `Client` `ModuleClient` . IoT 플러그 앤 플레이 규칙을 따르려면 장치는 속성이 성공적으로 수신 되었음을 서비스에 알려야 합니다.

#### <a name="report-a-writable-property"></a>쓰기 가능한 속성 보고

장치에서 쓰기 가능한 속성을 보고 하는 경우 규칙에 정의 된 값을 포함 해야 합니다 `ack` .

기본 구성 요소에서 쓰기 가능한 속성을 보고 하려면 다음을 수행 합니다.

```nodejs
patch = {
  targetTemperature:
    {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

중첩 된 구성 요소에서 쓰기 가능한 속성을 보고 하려면 쌍에 표식을 포함 해야 합니다.

```nodejs
patch = {
  thermostat1: {
    '__t' : 'c',
    targetTemperature: {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
  }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

장치 쌍은 다음에 보고 된 속성으로 업데이트 됩니다.

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Desired 속성 업데이트를 구독 합니다.

서비스는 연결 된 장치에 대 한 알림을 트리거하는 desired 속성을 업데이트할 수 있습니다. 이 알림에는 업데이트를 식별 하는 버전 번호를 포함 하 여 업데이트 된 desired 속성이 포함 됩니다. 장치는 보고 된 속성과 동일한 메시지를 사용 하 여 응답 해야 합니다 `ack` .

기본 구성 요소는 단일 속성을 확인 하 고 수신 된 버전으로 보고 된를 만듭니다 `ack` .

```nodejs
const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
};

desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};
```

장치 쌍은 원하는 및 보고 된 섹션의 속성을 보여 줍니다.

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

중첩 된 구성 요소는 구성 요소 이름을 사용 하 여 래핑된 desired 속성을 받아 보고 된 속성을 다시 보고 해야 합니다 `ack` .

```nodejs
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

구성 요소에 대 한 장치 쌍은 다음과 같이 원하는 및 보고 된 섹션을 보여 줍니다.

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>명령

기본 구성 요소는 서비스에서 호출 된 명령 이름을 받습니다.

중첩 된 구성 요소는 구성 요소 이름 및 구분 기호를 접두사로 하는 명령 이름을 받습니다 `*` .

```nodejs
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  
  // ...

  case 'thermostat1*reboot': {
    await response.send(200, 'reboot response');
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};

client.onDeviceMethod('thermostat1*reboot', commandHandler);
```

#### <a name="request-and-response-payloads"></a>요청 및 응답 페이로드

명령은 형식을 사용 하 여 요청 및 응답 페이로드를 정의 합니다. 장치는 들어오는 입력 매개 변수를 deserialize 하 고 응답을 직렬화 해야 합니다. 다음 예제에서는 페이로드에 정의 된 복합 형식을 사용 하 여 명령을 구현 하는 방법을 보여 줍니다.

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

다음 코드 조각에서는 serialization 및 deserialization을 활성화 하는 데 사용 되는 형식을 포함 하 여 장치에서이 명령 정의를 구현 하는 방법을 보여 줍니다.

```nodejs
class TemperatureSensor {

  // ...

  getMaxMinReportObject() {
    return {
      maxTemp: this.maxTemp,
      minTemp: this.minTemp,
      avgTemp: this.cumulativeTemperature / this.numberOfTemperatureReadings,
      endTime: (new Date(Date.now())).toISOString(),
      startTime: this.startTime
    };
  }
}

// ...

const deviceTemperatureSensor = new TemperatureSensor();

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await response.send(200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};
```

> [!Tip]
> 요청 및 응답 이름이 네트워크를 통해 전송 되는 직렬화 된 페이로드에 없습니다.

[!INCLUDE [iot-pnp-device-devguide-summary.md](../../includes/iot-pnp-device-devguide-summary.md)]
