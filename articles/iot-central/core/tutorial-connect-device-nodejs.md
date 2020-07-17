---
title: 자습서 - Azure IoT Central에 제네릭 Node.js 클라이언트 앱 연결 | Microsoft Docs
description: 이 자습서에서는 디바이스 개발자가 Node.js 클라이언트 앱을 실행하는 디바이스를 Azure IoT Central 애플리케이션에 연결하는 방법을 보여 줍니다. 디바이스 기능 모델을 가져와서 디바이스 템플릿을 만들고, 연결된 디바이스와 상호 작용할 수 있는 보기를 추가합니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/24/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mqtt
ms.openlocfilehash: 65f441425113d89010cc2d282758c5a042be9300
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417908"
---
# <a name="tutorial-create-and-connect-a-client-application-to-your-azure-iot-central-application-nodejs"></a>자습서: 클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결(Node.js)

[!INCLUDE [iot-central-selector-tutorial-connect](../../../includes/iot-central-selector-tutorial-connect.md)]

*이 문서는 솔루션 빌더 및 디바이스 개발자에게 적용됩니다.*

이 자습서에서는 디바이스 개발자가 Node.js 클라이언트 애플리케이션을 Azure IoT Central 애플리케이션에 연결하는 방법을 보여 줍니다. Node.js 애플리케이션은 환경 센서 디바이스의 동작을 시뮬레이션합니다. _디바이스 기능 모델_ 샘플을 사용하여 IoT Central에서 _디바이스 템플릿_을 만듭니다. 운영자가 디바이스와 상호 작용할 수 있도록 디바이스 템플릿에 보기를 추가합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 디바이스 기능 모델을 가져와서 디바이스 템플릿을 만듭니다.
> * 기본 및 사용자 지정 보기를 디바이스 템플릿에 추가합니다.
> * 디바이스 템플릿을 게시하고, 실제 디바이스를 IoT Central 애플리케이션에 추가합니다.
> * Node.js 디바이스 코드를 만들고 실행하며, IoT Central 애플리케이션에 연결되는지 확인합니다.
> * 디바이스에서 보낸 시뮬레이션된 원격 분석 데이터 보기
> * 보기를 사용하여 디바이스 속성을 관리합니다.
> * 디바이스를 제어하는 동기 및 비동기 명령을 호출합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료하려면 다음이 필요합니다.

* **사용자 지정 애플리케이션** 템플릿을 사용하여 만든 Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
* [Node.js](https://nodejs.org/) 버전 10.0.0 이상이 설치된 개발 머신. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. 이 자습서의 지침에서는 Windows 명령 프롬프트에서 **node** 명령을 실행한다고 가정합니다. 하지만 여러 운영 체제에서 Node.js를 사용할 수 있습니다.

[!INCLUDE [iot-central-add-environmental-sensor](../../../includes/iot-central-add-environmental-sensor.md)]

### <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기

다음 단계에서는 애플리케이션에 추가한 실제 디바이스에 연결하는 Node.js 클라이언트 애플리케이션을 만드는 방법을 보여줍니다. 이 Node.js 애플리케이션은 실제 디바이스의 동작을 시뮬레이션합니다.

1. 명령줄 환경에서 이전에 만든 `environmental-sensor` 폴더로 이동합니다.

1. Node.js 프로젝트를 초기화하고 필요한 종속성을 설치하려면 다음 명령을 실행합니다. `npm init`가 실행되면 모든 기본 옵션을 그대로 적용합니다.

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. **environmentalSensor.js**라는 파일을 `environmental-sensor` 폴더에 만듭니다.

1. 다음 `require` 문을 **environmentalSensor.js** 파일의 시작 부분에 추가합니다.

    ```javascript
    "use strict";

    // Use the Azure IoT device SDK for devices that connect to Azure IoT Central.
    var iotHubTransport = require('azure-iot-device-mqtt').Mqtt;
    var Client = require('azure-iot-device').Client;
    var Message = require('azure-iot-device').Message;
    var ProvisioningTransport = require('azure-iot-provisioning-device-mqtt').Mqtt;
    var SymmetricKeySecurityClient = require('azure-iot-security-symmetric-key').SymmetricKeySecurityClient;
    var ProvisioningDeviceClient = require('azure-iot-provisioning-device').ProvisioningDeviceClient;
    ```

1. 다음 변수 선언을 파일에 추가합니다.

    ```javascript
    var provisioningHost = 'global.azure-devices-provisioning.net';
    var idScope = '{your Scope ID}';
    var registrationId = '{your Device ID}';
    var symmetricKey = '{your Primary Key}';
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var ledOn = true;
    ```

    `{your Scope ID}`, `{your Device ID}` 및 `{your Primary Key}` 자리 표시자를 이전에 적어 둔 값으로 업데이트합니다. 이 샘플에서 `targetTemperature`를 0으로 초기화하면 디바이스의 현재 판독 값 또는 디바이스 쌍의 값을 사용할 수 있습니다.

1. 시뮬레이션한 원격 분석 데이터를 Azure IoT Central 애플리케이션에 보내려면 다음 함수를 파일에 추가합니다.

    ```javascript
    // Send simulated device telemetry.
    function sendTelemetry() {
      var temp = targetTemperature + (Math.random() * 15);
      var humid = 70 + (Math.random() * 10);
      var data = JSON.stringify({
        temp: temp,
        humid: humid,
        });
      var message = new Message(data);
      hubClient.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    원격 분석 항목의 이름(`temp` 및 `humid`)이 디바이스 템플릿에 사용된 이름과 일치해야 합니다.

1. Azure IoT Central 애플리케이션에 디바이스 쌍 속성을 보낼 수 있도록 다음 함수를 파일에 추가합니다.

    ```javascript
    // Send device twin reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

    IoT Central은 디바이스 쌍을 사용하여 디바이스와 IoT Central 애플리케이션 간에 속성 값을 동기화합니다. 디바이스 속성 값은 디바이스 쌍 reported 속성을 사용합니다. 쓰기 가능한 속성은 디바이스 쌍 reported 속성과 desired 속성을 모두 사용합니다.

1. 디바이스에서 응답하는 쓰기 가능한 속성을 정의하고 처리하려면 다음 코드를 추가합니다.

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that's called when the writeable property
    // is updated in the IoT Central application.
    var writeableProperties = {
      'name': (newValue, callback) => {
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'brightness': (newValue, callback) => {
        setTimeout(() => {
            callback(newValue, 'completed');
        }, 5000);
      }
    };

    // Handle writeable property updates that come from IoT Central via the device twin.
    function handleWriteablePropertyUpdates(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (writeableProperties[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            writeableProperties[setting](desiredChange[setting].value, (newValue, status) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

    운영자가 IoT Central 애플리케이션에서 쓰기 가능 속성을 설정하면 애플리케이션에서는 디바이스 쌍 desired 속성을 사용하여 디바이스에 값을 보냅니다. 그러면 디바이스는 디바이스 쌍 reported 속성속성을 사용하여 응답합니다. IoT Central은 reported 속성 값을 받으면 속성 보기를 **동기화됨** 상태로 업데이트합니다.

    속성 이름(`name` 및 `brightness`)은 디바이스 템플릿에 사용된 이름과 일치해야 합니다.

1. IoT Central 애플리케이션에서 보낸 명령을 처리하려면 다음 코드를 추가합니다.

    ```javascript
    // Setup command handlers
    function setupCommandHandlers(twin) {

      // Handle synchronous LED blink command with request and response payload.
      function onBlink(request, response) {
        console.log('Received synchronous call to blink');
        var responsePayload = {
          status: 'Blinking LED every ' + request.payload  + ' seconds'
        }
        response.send(200, responsePayload, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            console.log('Blinking LED every ' + request.payload  + ' seconds');
          }
        });
      }

      // Handle synchronous LED turn on command
      function turnOn(request, response) {
        console.log('Received synchronous call to turn on LED');
        if(!ledOn){
          console.log('Turning on the LED');
          ledOn = true;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle synchronous LED turn off command
      function turnOff(request, response) {
        console.log('Received synchronous call to turn off LED');
        if(ledOn){
          console.log('Turning off the LED');
          ledOn = false;
        }
        response.send(200, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          }
        });
      }

      // Handle asynchronous sensor diagnostics command with response payload.
      function diagnostics(request, response) {
        console.log('Starting asynchronous diagnostics run...');
        response.send(202, (err) => {
          if (err) {
            console.error('Unable to send method response: ' + err.toString());
          } else {
            var repetitions = 3;
            var intervalID = setInterval(() => {
              console.log('Generating diagnostics...');
              if (--repetitions === 0) {
                clearInterval(intervalID);
                var properties = {
                  rundiagnostics: {
                    value: 'Diagnostics run complete at ' + new Date().toLocaleString()
                  }
                };
                sendDeviceProperties(twin, properties);
              }
            }, 2000);
          }
        });
      }

      hubClient.onDeviceMethod('blink', onBlink);
      hubClient.onDeviceMethod('turnon', turnOn);
      hubClient.onDeviceMethod('turnoff', turnOff);
      hubClient.onDeviceMethod('rundiagnostics', diagnostics);
    }
    ```

    명령 이름(`blink`, `turnon`, `turnoff` 및 `rundiagnostics`)은 디바이스 템플릿에 사용된 이름과 일치해야 합니다.

    현재 IoT Central은 디바이스 기능 모델에 정의된 응답 스키마를 사용하지 않습니다. 동기 명령의 경우 유효한 모든 JSON은 응답 페이로드가 될 수 있습니다. 비동기 명령의 경우 디바이스에서 즉시 202 응답을 반환한 후, 작업이 완료되면 reported 속성 업데이트를 실행해야 합니다. reported 속성 업데이트의 형식은 다음과 같습니다.

    ```json
    {
      [command name] : {
        value: 'response message'
      }
    }
    ```

    운영자는 명령 기록에서 응답 페이로드를 볼 수 있습니다.

1. Azure IoT Central에 대한 연결을 완료하고 클라이언트 코드의 함수를 연결하려면 다음 코드를 추가합니다.

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');

        // Send telemetry to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);

            handleWriteablePropertyUpdates(twin);

            setupCommandHandlers(twin);
          }
        });
      }
    };

    // Start the device (register and connect to Azure IoT Central).
    provisioningClient.register((err, result) => {
      if (err) {
        console.log('Error registering device: ' + err);
      } else {
        console.log('Registration succeeded');
        console.log('Assigned hub=' + result.assignedHub);
        console.log('DeviceId=' + result.deviceId);
        var connectionString = 'HostName=' + result.assignedHub + ';DeviceId=' + result.deviceId + ';SharedAccessKey=' + symmetricKey;
        hubClient = Client.fromConnectionString(connectionString, iotHubTransport);

        hubClient.open(connectCallback);
      }
    });
    ```

## <a name="run-your-nodejs-application"></a>Node.js 애플리케이션 실행

디바이스 클라이언트 애플리케이션을 시작하려면 명령줄 환경에서 다음 명령을 실행합니다.

```cmd/sh
node environmentalSensor.js
```

디바이스가 Azure IoT Central 애플리케이션에 연결되어 원격 분석을 보내기 시작하는 것을 볼 수 있습니다.

![클라이언트 애플리케이션 실행](media/tutorial-connect-device-nodejs/run-application.png)

[!INCLUDE [iot-central-monitor-environmental-sensor](../../../includes/iot-central-monitor-environmental-sensor.md)]

다음과 같이 디바이스가 명령 및 속성 업데이트에 응답하는 방식을 확인할 수 있습니다.

![클라이언트 애플리케이션 관찰](media/tutorial-connect-device-nodejs/run-application-2.png)

## <a name="next-steps"></a>다음 단계

디바이스 개발자로서, Node.js를 사용하여 디바이스를 만드는 방법의 기본 사항을 배웠으므로 이제 다음 단계를 수행하는 것이 좋습니다.

* [Azure IoT Central 애플리케이션에 MXChip IoT DevKit 디바이스 연결](./howto-connect-devkit.md) 방법 문서에서 실제 디바이스를 IoT Central에 연결하는 방법에 대해 알아봅니다.
* 디바이스 코드를 구현할 때 디바이스 템플릿의 역할을 자세히 알아보려면 [디바이스 템플릿이란?](./concepts-device-templates.md)을 참조하세요.
* IoT Central에 디바이스를 등록하는 방법과 IoT Central에서 디바이스 연결을 보호하는 방법에 대한 자세한 내용은 [Azure IoT Central에 연결](./concepts-get-connected.md)을 참조하세요.

IoT Central 자습서 세트를 계속 진행하고 IoT Central 솔루션 빌드에 대해 자세히 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [게이트웨이 디바이스 템플릿 만들기](./tutorial-define-gateway-device-type.md)
