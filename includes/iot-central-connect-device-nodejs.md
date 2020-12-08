---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/24/2020
ms.openlocfilehash: 4b19ae1b584a2d300e4144e79ef76245c71035cf
ms.sourcegitcommit: b8a175b6391cddd5a2c92575c311cc3e8c820018
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96126139"
---
## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 다음이 필요합니다.

* **사용자 지정 애플리케이션** 템플릿을 사용하여 만든 Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](../articles/iot-central/core/quick-deploy-iot-central.md)을 참조하세요. 애플리케이션은 2020년 7월 14일 이후에 생성되어야 합니다.
* [Node.js](https://nodejs.org/) 버전 6 이상이 설치된 개발 머신. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. 이 자습서의 지침에서는 Windows 명령 프롬프트에서 **node** 명령을 실행한다고 가정합니다. 하지만 여러 운영 체제에서 Node.js를 사용할 수 있습니다.
* 샘플 코드가 포함된 [Node.js용 Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdk-node) GitHub 리포지토리의 로컬 복사본입니다. 다음 링크를 사용하여 리포지토리의 복사본을 다운로드합니다. [ZIP 다운로드](https://github.com/Azure/azure-iot-sdk-node/archive/master.zip). 그런 다음, 로컬 머신의 적절한 위치에 파일의 압축을 풉니다.

## <a name="review-the-code"></a>코드 검토

이전에 다운로드한 Node.js용 Microsoft Azure IoT SDK 복사본의 텍스트 편집기에서 *azure-iot-sdk-node/device/samples/pnp/simple_thermostat.js* 파일을 엽니다.

샘플을 실행하여 IoT Central에 연결하면 DPS(Device Provisioning Service)를 사용하여 디바이스를 등록하고 연결 문자열을 생성합니다. 이 샘플은 명령줄 환경에서 필요한 DPS 연결 정보를 검색합니다.

`main` 메서드는 다음 작업을 수행합니다.

* 연결을 열기 전에 `client` 개체를 만들고 `dtmi:com:example:Thermostat;1` 모델 ID를 설정합니다.
* 명령 처리기를 만듭니다.
* 10초마다 온도 원격 분석을 전송하는 루프를 시작합니다.
* `maxTempSinceLastReboot` 속성을 IoT Central로 보냅니다. IoT Central은 디바이스 모델의 일부가 아니므로 `serialNumber` 속성을 무시합니다.
* 쓰기 가능한 속성 처리기를 만듭니다.

```javascript
async function main() {

  // ...

  // fromConnectionString must specify a transport, coming from any transport package.
  const client = Client.fromConnectionString(deviceConnectionString, Protocol);

  let resultTwin;
  try {
    // Add the modelId here
    await client.setOptions(modelIdObject);
    await client.open();

    client.onDeviceMethod(commandMaxMinReport, commandHandler);

    // Send Telemetry every 10 secs
    let index = 0;
    intervalToken = setInterval(() => {
      sendTelemetry(client, index).catch((err) => console.log('error', err.toString()));
      index += 1;
    }, telemetrySendInterval);

    // attach a standard input exit listener
    attachExitHandler(client);

    // Deal with twin
    try {
      resultTwin = await client.getTwin();
      const patchRoot = createReportPropPatch({ serialNumber: deviceSerialNum });
      const patchThermostat = createReportPropPatch({
        maxTempSinceLastReboot: deviceTemperatureSensor.getMaxTemperatureValue()
      });

      // the below things can only happen once the twin is there
      updateComponentReportedProperties(resultTwin, patchRoot);
      updateComponentReportedProperties(resultTwin, patchThermostat);

      // Setup the handler for desired properties
      desiredPropertyPatchHandler(resultTwin);

    } catch (err) {
      console.error('could not retrieve twin or report twin properties\n' + err.toString());
    }
  } catch (err) {
    console.error('could not connect Plug and Play client or could not attach interval function for telemetry\n' + err.toString());
  }
}
```

`provisionDevice` 함수는 디바이스에서 DPS를 사용하여 IoT Central에 등록하고 연결하는 방법을 보여줍니다. 페이로드에는 모델 ID가 포함됩니다.

```javascript
async function provisionDevice(payload) {
  var provSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
  var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvProtocol(), provSecurityClient);

  if (!!(payload)) {
    provisioningClient.setProvisioningPayload(payload);
  }

  try {
    let result = await provisioningClient.register();
    deviceConnectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
  } catch (err) {
    console.error("error registering device: " + err.toString());
  }
}
```

`sendTelemetry` 함수는 디바이스에서 IoT Central에 온도 원격 분석을 보내는 방법을 보여줍니다. `getCurrentTemperatureObject` 메서드는 `{ temperature: 45.6 }`과 같은 개체를 반환합니다.

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

`main` 메서드는 다음 두 가지 메서드를 사용하여 `maxTempSinceLastReboot` 속성을 IoT Central로 보냅니다. `main` 메서드는 `{maxTempSinceLastReboot: 80.9}`와 같은 개체로 `createReportPropPatch`를 호출합니다.

```javascript
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

const updateComponentReportedProperties = (deviceTwin, patch) => {
  deviceTwin.properties.reported.update(patch, function (err) {
    if (err) throw err;
    console.log('Properties have been reported for component');
  });
};
```

`main` 메서드는 다음 두 가지 메서드를 사용하여 IoT Central에서 쓰기 가능한 _대상 온도_ 속성에 대한 업데이트를 처리합니다. `propertyUpdateHandle`이 버전 및 상태 코드를 사용하여 응답을 빌드하는 방법을 확인합니다.

```javascript
const desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};

const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  console.log('Received an update for property: ' + propertyName + ' with value: ' + JSON.stringify(desiredValue));
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
  console.log('updated the property');
};
```

`main` 메서드는 다음 두 가지 메서드를 사용하여 `getMaxMinReport` 명령에 대한 호출을 처리합니다. `getMaxMinReportObject` 메서드는 보고서를 JSON 개체로 생성합니다.

```javascript
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await sendCommandResponse(request, response, 200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await sendCommandResponse(request, response, 404, 'unknown method');
    break;
  }
};

const sendCommandResponse = async (request, response, status, payload) => {
  try {
    await response.send(status, payload);
    console.log('Response to method \'' + request.methodName +
              '\' sent successfully.' );
  } catch (err) {
    console.error('An error ocurred when sending a method response:\n' +
              err.toString());
  }
};
```

## <a name="get-connection-information"></a>연결 정보 가져오기

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>코드 실행

샘플 애플리케이션을 실행하려면 명령줄 환경을 열고 *simple_thermostat.js* 샘플 파일이 포함된 *azure-iot-sdk-node/device/samples/pnp* 폴더로 이동합니다.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

필요한 패키지를 설치합니다.

```cmd/sh
npm install
```

샘플을 실행합니다.

```cmd/sh
node simple_thermostat.js
```

다음 출력은 IoT Central에 등록하고 연결하는 디바이스를 보여줍니다. 그런 다음, 샘플은 원격 분석 전송을 시작하기 전에 `maxTempSinceLastReboot` 속성을 보냅니다.

```output
registration succeeded
assigned hub=iotc-.......azure-devices.net
deviceId=sample-device-01
payload=undefined
Connecting using connection string HostName=iotc-........azure-devices.net;DeviceId=sample-device-01;SharedAccessKey=Ci....=
Enabling the commands on the client
Please enter q or Q to exit sample.
The following properties will be updated for root interface:
{ maxTempSinceLastReboot: 55.20309427428496 }
Properties have been reported for component
Sending telemetry message 0...
Sending telemetry message 1...
Sending telemetry message 2...
Sending telemetry message 3...
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

다음과 같이 디바이스가 명령 및 속성 업데이트에 응답하는 방식을 확인할 수 있습니다.

```output
MaxMinReport 2020-10-15T12:00:00.000Z
Response to method 'getMaxMinReport' sent successfully.

...

Received an update for property: targetTemperature with value: {"value":86.3}
The following properties will be updated for root interface:
{
  targetTemperature: {
    value: { value: 86.3 },
    ac: 200,
    ad: 'Successfully executed patch for targetTemperature',
    av: 2
  }
}
```
