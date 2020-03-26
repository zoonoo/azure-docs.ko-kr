---
title: 자습서 - Azure IoT Central에 제네릭 Node.js 클라이언트 앱 연결 | Microsoft Docs
description: 이 자습서에서는 디바이스 개발자가 Node.js 클라이언트 앱을 실행하는 디바이스를 Azure IoT Central 애플리케이션에 연결하는 방법을 보여 줍니다. 디바이스 기능 모델을 가져와서 디바이스 템플릿을 만들고, 연결된 디바이스와 상호 작용할 수 있는 보기를 추가합니다.
author: dominicbetts
ms.author: dobett
ms.date: 02/26/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1bcfc949eff0639dd1b4a063687e2c198f480ea3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "77624339"
---
# <a name="tutorial-create-and-connect-a-nodejs-client-application-to-your-azure-iot-central-application-nodejs"></a>자습서: Node.js 클라이언트 애플리케이션을 만들어 Azure IoT Central 애플리케이션에 연결(Node.js)

이 자습서에서는 디바이스 개발자가 Node.js 클라이언트 애플리케이션을 Azure IoT Central 애플리케이션에 연결하는 방법을 보여 줍니다. Node.js 애플리케이션은 실제 디바이스의 동작을 시뮬레이션합니다. 환경 센서 디바이스에 대한 _디바이스 기능 모델_ 샘플을 사용하여 IoT Central에서 _디바이스 템플릿_을 만듭니다. 디바이스 원격 분석을 시각화하고, 디바이스 속성을 관리하며, 명령을 사용하여 디바이스를 제어할 수 있도록 보기를 디바이스 템플릿에 추가합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 디바이스 기능 모델을 가져와서 디바이스 템플릿을 만듭니다.
> * 기본 및 사용자 지정 보기를 디바이스 템플릿에 추가합니다.
> * 디바이스 템플릿을 게시하고, 실제 디바이스를 IoT Central 애플리케이션에 추가합니다.
> * Node.js 디바이스 코드를 만들고 실행하며, IoT Central 애플리케이션에 연결되는지 확인합니다.
> * 디바이스가 전송하는 시뮬레이션된 원격 분석 데이터 보기
> * 보기를 사용하여 디바이스 속성을 관리합니다.
> * 명령을 호출하여 디바이스를 제어합니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음이 필요합니다.

* **사용자 지정 애플리케이션 **템플릿을 사용하여 만든 Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
* [Node.js](https://nodejs.org/) 버전 10.0.0 이상이 설치된 개발 머신. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. Node.js는 다양한 운영 체제에 사용할 수 있습니다. 이 자습서의 지침에서는 Windows 명령 프롬프트에서 **node** 명령을 실행한다고 가정합니다. Node.js는 다양한 운영 체제에서 사용할 수 있습니다.

## <a name="create-a-device-template"></a>디바이스 템플릿 만들기

`environmental-sensor`라는 폴더를 로컬 머신에 만듭니다.

[환경 센서 기능 모델](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) JSON 파일을 다운로드하여 `environmental-sensor` 폴더에 저장합니다.

텍스트 편집기를 사용하여 다운로드한 `EnvironmentalSensorInline.capabilitymodel.json` 파일에서 `{YOUR_COMPANY_NAME_HERE}`의 두 인스턴스를 회사 이름으로 바꿉니다.

Azure IoT Central 애플리케이션에서 `EnvironmentalSensorInline.capabilitymodel.json` 디바이스 기능 모델 파일을 가져와서 *환경 센서*라는 디바이스 템플릿을 만듭니다.

![가져온 디바이스 기능 모델을 사용하는 디바이스 템플릿](./media/tutorial-connect-device/device-template.png)

디바이스 기능 모델에는 표준 **디바이스 정보** 인터페이스와 사용자 지정 **환경 센서** 인터페이스라는 두 개의 인터페이스가 포함되어 있습니다. **환경 센서** 인터페이스에서 정의하는 기능은 다음과 같습니다.

| Type | 표시 이름 | Description |
| ---- | ------------ | ----------- |
| 속성 | 디바이스 상태     | 디바이스 상태입니다. 온라인/오프라인이라는 두 가지 상태를 사용할 수 있습니다. |
| 속성 | 고객 이름    | 현재 디바이스를 작동하는 고객의 이름입니다. |
| 속성 | 밝기 수준 | 디바이스의 표시등에 대한 밝기 수준입니다. 1(높음), 2(중간), 3(낮음)으로 지정할 수 있습니다. |
| 원격 분석 | 온도 | 디바이스의 현재 온도입니다. |
| 원격 분석 | 습도    | 디바이스의 현재 습도입니다. |
| 명령 | blink          | 지정된 시간 간격 동안 LED 표시등을 깜박이기 시작합니다. |
| 명령 | turnon         | 디바이스의 LED 표시등을 켭니다. |
| 명령 | turnoff        | 디바이스의 LED 표시등을 끕니다. |
| 명령 | rundiagnostics | 진단 실행을 시작합니다. |

IoT Central 애플리케이션에서 **디바이스 상태** 속성이 표시되는 방식을 사용자 지정하려면 디바이스 템플릿에서 **사용자 지정**을 선택합니다. **디바이스 상태** 항목을 펼치고, **True 이름**으로 _온라인_을 입력하고, **False 이름**으로 _오프라인_을 입력합니다. 그런 다음, 변경 내용을 저장합니다.

![디바이스 템플릿 사용자 지정](media/tutorial-connect-device/customize-template.png)

## <a name="create-views"></a>뷰 만들기

보기를 통해 IoT Central 애플리케이션에 연결된 디바이스와 상호 작용할 수 있습니다. 예를 들어 원격 분석을 표시하는 보기, 속성을 표시하는 보기, 쓰기 가능 및 클라우드 속성을 편집할 수 있는 보기를 사용할 수 있습니다. 보기는 디바이스 템플릿의 일부입니다.

일부 기본 보기를 **환경 센서** 디바이스 템플릿에 추가하려면 디바이스 템플릿으로 이동하여 **보기**, **기본 보기 생성** 타일을 차례로 선택합니다. **개요** 및 **정보**가 **켜기**인지 확인한 다음, **기본 대시보드 보기 생성**을 선택합니다. 이제 두 개의 기본 보기가 템플릿에 정의됩니다.

**환경 센서** 인터페이스에는 **고객 이름** 및 **밝기 수준**의 쓰기 가능한 속성이 포함되어 있습니다. 보기를 만들려면 다음 속성을 사용하여 편집할 수 있습니다.

1. **보기**, **디바이스 및 클라우드 데이터를 편집하는 중** 타일을 차례로 선택합니다.

1. 양식 이름으로 _속성_을 입력합니다.

1. **밝기 수준** 및 **고객 이름** 속성을 선택합니다. 그런 다음, **섹션 추가**를 선택합니다.

1. 변경 내용을 저장합니다.

![속성을 편집하는 데 사용할 수 있는 보기 추가](media/tutorial-connect-device/properties-view.png)

## <a name="publish-the-template"></a>템플릿 게시

**환경 센서** 디바이스 템플릿을 사용하는 IoT Central 애플리케이션에 디바이스를 추가하려면 먼저 템플릿을 게시해야 합니다.

디바이스 템플릿에서 **게시**를 선택합니다. 게시할 변경 내용을 표시하는 패널에서 **게시**를 선택합니다.

템플릿을 사용할 준비가 되었는지 확인하려면 IoT Central 애플리케이션에서 **디바이스** 페이지로 이동합니다. **디바이스** 섹션에는 애플리케이션에 게시된 디바이스의 목록이 표시됩니다.

![디바이스 페이지에 게시된 템플릿](media/tutorial-connect-device/published-templates.png)

## <a name="add-a-real-device"></a>실제 디바이스 추가

Azure IoT Central 애플리케이션에서 실제 디바이스를 이전 섹션에서 만든 디바이스 템플릿에 추가합니다.

1. **디바이스** 페이지에서 **환경 센서** 디바이스 템플릿을 선택합니다.

1. **+새로 만들기**를 선택합니다.

1. **시뮬레이션됨**이 **끄기**인지 확인합니다. 그런 다음 **만들기**를 선택합니다.

디바이스 이름을 클릭한 다음, **연결**을 선택합니다. **디바이스 연결** 페이지에서 디바이스 연결 정보(**ID 범위**, **디바이스 ID** 및 **기본 키** )를 적어 둡니다. 이러한 값은 디바이스 코드를 만들 때 필요합니다.

![디바이스 연결 정보](./media/tutorial-connect-device/device-connection.png)

### <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기

다음 단계에서는 애플리케이션에 추가한 실제 디바이스를 구현하는 Node.js 클라이언트 애플리케이션을 만드는 방법을 보여 줍니다. 이 Node.js 애플리케이션은 실제 디바이스의 동작을 시뮬레이션합니다.

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

1. 원격 분석을 Azure IoT Central 애플리케이션에 보내려면 다음 함수를 파일에 추가합니다.

    ```javascript
    // Send device measurements.
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

1. Azure IoT Central 애플리케이션에 디바이스 속성을 보낼 수 있도록 다음 함수를 파일에 추가합니다.

    ```javascript
    // Send device reported properties.
    function sendDeviceProperties(twin, properties) {
      twin.properties.reported.update(properties, (err) => console.log(`Sent device properties: ${JSON.stringify(properties)}; ` +
        (err ? `error: ${err.toString()}` : `status: success`)));
    }
    ```

1. 디바이스에서 응답하는 쓰기 가능한 속성을 정의하고 처리하려면 다음 코드를 추가합니다.

    ```javascript
    // Add any writeable properties your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

    // Handle writeable property changes that come from Azure IoT Central via the device twin.
    function handleSettings(twin) {
      twin.on('properties.desired', function (desiredChange) {
        for (let setting in desiredChange) {
          if (settings[setting]) {
            console.log(`Received setting: ${setting}: ${desiredChange[setting].value}`);
            settings[setting](desiredChange[setting].value, (newValue, status, message) => {
              var patch = {
                [setting]: {
                  value: newValue,
                  status: status,
                  desiredVersion: desiredChange.$version,
                  message: message
                }
              }
              sendDeviceProperties(twin, patch);
            });
          }
        }
      });
    }
    ```

1. IoT Central 애플리케이션에서 보낸 명령을 처리하려면 다음 코드를 추가합니다.

    ```javascript
    // Handle blink command
    function onBlink(request, response) {
      console.log('Received asynchronous call to blink');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Blinking LED every ' + request.payload  + ' seconds');
        }
      });
    }

    // Handle LED turn on command
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

    // Handle LED turn off command
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

    // Handle sensor diagnostics command
    function diagnostics(request, response) {
      console.log('Received asynchronous call to run diagnostics');
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          console.log('Running diagnostics...');
        }
      });
    }
    ```

1. Azure IoT Central에 대한 연결을 완료하고 클라이언트 코드의 함수를 연결하려면 다음 코드를 추가합니다.

    ```javascript
    // Handle device connection to Azure IoT Central.
    var connectCallback = (err) => {
      if (err) {
        console.log(`Device could not connect to Azure IoT Central: ${err.toString()}`);
      } else {
        console.log('Device successfully connected to Azure IoT Central');
        // Create handlers for commands
        hubClient.onDeviceMethod('blink', onBlink);
        hubClient.onDeviceMethod('turnon', turnOn);
        hubClient.onDeviceMethod('turnoff', turnOff);
        hubClient.onDeviceMethod('rundiagnostics', diagnostics);
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send Environmental Sensor device properties once on device start up.
            var properties = {
              state: 'true'
            };
            sendDeviceProperties(twin, properties);
            handleSettings(twin);
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

![클라이언트 애플리케이션 실행](media/tutorial-connect-device/run-application.png)

Azure IoT Central 애플리케이션의 운영자는 다음을 수행할 수 있습니다.

* **개요** 페이지에서 디바이스에서 보낸 원격 분석을 봅니다.

    ![원격 분석 보기](media/tutorial-connect-device/view-telemetry.png)

* **속성** 페이지에서 쓰기 가능한 속성 값을 업데이트합니다.

    ![속성 업데이트](media/tutorial-connect-device/update-properties.png)

    ![속성을 업데이트하는 디바이스](media/tutorial-connect-device/update-properties-device.png)

* **명령** 페이지에서 명령을 호출합니다.

    ![blink 명령 호출](media/tutorial-connect-device/call-command.png)

    ![blink 명령을 호출하는 디바이스](media/tutorial-connect-device/call-command-device.png)

* **정보**페이지에서 디바이스 속성을 봅니다.

    ![속성 보기](media/tutorial-connect-device/about-properties.png)

## <a name="next-steps"></a>다음 단계

디바이스 기능 모델 및 사용자 고유의 디바이스 템플릿을 만드는 방법에 대해 자세히 알아보려면 방법 가이드로 계속 진행하세요.

> [!div class="nextstepaction"]
> [새 IoT 디바이스 유형 정의](./howto-set-up-template.md)
