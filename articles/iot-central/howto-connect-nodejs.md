---
title: Azure IoT Central에 일반 Node.js 클라이언트 애플리케이션 연결 | Microsoft Docs
description: 장치 개발자는 일반 node.js 장치를 Azure IoT Central 응용 프로그램에 연결 하는 방법을 설명 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 09/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 75b900ecb37ae8d092d4e37129b7f39f801c470d
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066438"
---
# <a name="connect-a-generic-client-application-to-your-azure-iot-central-application-nodejs"></a>Azure IoT Central 애플리케이션에 일반 클라이언트 애플리케이션 연결(Node.js)

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

이 문서에서는 디바이스 개발자가 실제 디바이스를 나타내는 일반 Node.js 애플리케이션을 Microsoft Azure IoT Central 애플리케이션에 연결하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

이 문서의 단계를 완료하려면 다음이 필요합니다.

- Azure IoT Central 애플리케이션. 자세한 내용은 [애플리케이션 만들기 빠른 시작](quick-deploy-iot-central.md)을 참조하세요.
- [Node.js](https://nodejs.org/) 버전 4.0.0 이상이 설치된 개발 컴퓨터. 명령줄에서 `node --version` 명령을 실행하여 버전을 확인할 수 있습니다. Node.js는 다양한 운영 체제에 사용할 수 있습니다.

## <a name="create-a-device-template"></a>디바이스 템플릿 만들기

Azure IoT Central 응용 프로그램에서 다음 측정, 장치 속성, 설정 및 명령을 사용 하 여 장치 템플릿이 필요 합니다.

### <a name="telemetry-measurements"></a>원격 분석 측정값

**측정** 페이지에서 다음 원격 분석을 추가 합니다.

| 표시 이름 | 필드 이름  | 단위 | 최소 | 최대 | 소수 자릿수 |
| ------------ | ----------- | ----- | --- | --- | -------------- |
| 온도  | 온도 | F     | 60  | 110 | 0              |
| 습도     | humidity    | %     | 0   | 100 | 0              |
| 압력     | pressure    | kPa   | 80  | 110 | 0              |

> [!NOTE]
> 원격 분석 측정값의 데이터 형식은 부동 소수점 숫자입니다.

테이블에 표시된 필드 이름을 디바이스 템플릿에 똑같이 입력합니다. 필드 이름이 해당 장치 코드의 속성 이름과 일치 하지 않는 경우 응용 프로그램에 원격 분석을 표시할 수 없습니다.

### <a name="state-measurements"></a>상태 측정값

**측정** 페이지에서 다음 상태를 추가 합니다.

| 표시 이름 | 필드 이름  | 값 1 | 표시 이름 | 값 2 | 표시 이름 |
| ------------ | ----------- | --------| ------------ | ------- | ------------ | 
| 팬 모드     | fanmode     | 1       | 실행 중      | 0       | 중지됨      |

> [!NOTE]
> 상태 측정값의 데이터 형식은 문자열입니다.

테이블에 표시된 필드 이름을 디바이스 템플릿에 똑같이 입력합니다. 필드 이름이 해당 장치 코드의 속성 이름과 일치 하지 않는 경우에는 응용 프로그램에 상태를 표시할 수 없습니다.

### <a name="event-measurements"></a>이벤트 측정값

**측정** 페이지에서 다음 이벤트를 추가 합니다.

| 표시 이름 | 필드 이름  | Severity |
| ------------ | ----------- | -------- |
| 과열  | overheat    | 오류    |

> [!NOTE]
> 이벤트 측정값의 데이터 형식은 문자열입니다.

### <a name="location-measurements"></a>위치 측정

**측정** 페이지에서 다음 위치 측정을 추가 합니다.

| 표시 이름 | 필드 이름  |
| ------------ | ----------- |
| 위치     | 위치    |

위치 측정 데이터 형식은 경도 및 위도에 대 한 두 개의 부동 소수점 숫자와 고도의 부동 소수점 수로 구성 됩니다.

테이블에 표시된 필드 이름을 디바이스 템플릿에 똑같이 입력합니다. 필드 이름이 해당 장치 코드의 속성 이름과 일치 하지 않는 경우 응용 프로그램에 위치를 표시할 수 없습니다.

### <a name="device-properties"></a>디바이스 속성

**속성** 페이지에서 다음 장치 속성을 추가 합니다.

| 표시 이름        | 필드 이름        | 데이터 형식 |
| ------------------- | ----------------- | --------- |
| 일련 번호       | serialNumber      | text      |
| 디바이스 제조업체 | 제조업체      | text      |

테이블에 표시된 필드 이름을 디바이스 템플릿에 똑같이 입력합니다. 필드 이름이 해당 장치 코드의 속성 이름과 일치 하지 않는 경우에는 응용 프로그램에 속성을 표시할 수 없습니다.

### <a name="settings"></a>설정

**설정** 페이지에서 다음 **번호** 설정을 추가 합니다.

| 표시 이름    | 필드 이름     | 단위 | 10진수 | 최소 | 최대  | 초기 |
| --------------- | -------------- | ----- | -------- | --- | ---- | ------- |
| 팬 속도       | fanSpeed       | rpm   | 0        | 0   | 3000 | 0       |
| 온도 설정 | setTemperature | F     | 0        | 20  | 200  | 80      |

테이블에 표시된 필드 이름을 디바이스 템플릿에 똑같이 입력합니다. 필드 이름이 해당 장치 코드의 속성 이름과 일치 하지 않는 경우 장치는 설정 값을 받을 수 없습니다.

### <a name="commands"></a>명령

**명령** 페이지에서 다음 명령을 추가 합니다.

| 표시 이름    | 필드 이름     | 기본 시간 제한 | 데이터 형식 |
| --------------- | -------------- | --------------- | --------- |
| 다운       | 다운      | 30              | number    |

카운트다운 명령에 다음 입력 필드를 추가 합니다.

| 표시 이름    | 필드 이름     | 데이터 형식 | 값 |
| --------------- | -------------- | --------- | ----- |
| 개수      | countFrom      | number    | 10    |

테이블에 표시 된 대로 필드 이름을 장치 템플릿에 정확 하 게 입력 합니다. 필드 이름이 해당 장치 코드의 속성 이름과 일치 하지 않으면 장치에서 명령을 처리할 수 없습니다.

## <a name="add-a-real-device"></a>실제 디바이스 추가

Azure IoT Central 응용 프로그램에서 이전 섹션에서 만든 장치 템플릿에 실제 장치를 추가 합니다.

**장치 연결** 페이지에서 장치 연결 정보를 적어 둡니다. **범위 ID**, **장치 Id**및 **기본 키**입니다. 이 방법 가이드의 뒷부분에서 이러한 값을 장치 코드에 추가 합니다.

![장치 연결 정보](./media/howto-connect-nodejs/device-connection.png)

### <a name="create-a-nodejs-application"></a>Node.js 애플리케이션 만들기

다음 단계에서는 애플리케이션에 추가한 실제 디바이스를 구현하는 클라이언트 애플리케이션을 만드는 방법을 보여줍니다. Node.js 애플리케이션은 실제 디바이스를 나타냅니다.

1. 컴퓨터에 `connected-air-conditioner-adv`이라는 폴더를 만듭니다. 명령줄 환경에서 해당 폴더로 이동합니다.

1. Node.js 프로젝트를 초기화하기 위해 다음 명령을 실행합니다.

    ```cmd/sh
    npm init
    npm install azure-iot-device azure-iot-device-mqtt azure-iot-provisioning-device-mqtt azure-iot-security-symmetric-key --save
    ```

1. `connected-air-conditioner-adv` 폴더에 **connectedAirConditionerAdv.js**라는 파일을 만듭니다.

1. **connectedAirConditionerAdv.js** 파일의 시작 부분에 다음 `require` 문을 추가합니다.

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
    var symmetricKey = '{your Primary Key};
    var provisioningSecurityClient = new SymmetricKeySecurityClient(registrationId, symmetricKey);
    var provisioningClient = ProvisioningDeviceClient.create(provisioningHost, idScope, new ProvisioningTransport(), provisioningSecurityClient);
    var hubClient;

    var targetTemperature = 0;
    var locLong = -122.1215;
    var locLat = 47.6740;
    ```

    자리 표시자 `{your Scope ID}`, `{your Device ID}`및 `{your Primary Key}` 을 이전에 기록해 둔 값으로 업데이트 합니다. 이 샘플에서는를 0으로 `targetTemperature` 초기화 하 여 장치에서 현재 읽기 또는 장치 쌍의 값을 사용할 수 있습니다.

1. 원격 분석, 상태, 이벤트 및 위치 측정을 Azure IoT Central 응용 프로그램으로 보내려면 파일에 다음 함수를 추가 합니다.

    ```javascript
    // Send device measurements.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var humidity = 70 + (Math.random() * 10);
      var pressure = 90 + (Math.random() * 5);
      var fanmode = 0;
      var locationLong = locLong - (Math.random() / 100);
      var locationLat = locLat - (Math.random() / 100);
      var data = JSON.stringify({
        temperature: temperature,
        humidity: humidity,
        pressure: pressure,
        fanmode: (temperature > 25) ? "1" : "0",
        overheat: (temperature > 35) ? "ER123" : undefined,
        location: {
            lon: locationLong,
            lat: locationLat }
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

1. 디바이스가 응답하는 설정을 정의할 수 있도록 다음 정의를 추가합니다.

    ```javascript
    // Add any settings your device supports,
    // mapped to a function that is called when the setting is changed.
    var settings = {
      'fanSpeed': (newValue, callback) => {
          // Simulate it taking 1 second to set the fan speed.
          setTimeout(() => {
            callback(newValue, 'completed');
          }, 1000);
      },
      'setTemperature': (newValue, callback) => {
        // Simulate the temperature setting taking two steps.
        setTimeout(() => {
          targetTemperature = targetTemperature + (newValue - targetTemperature) / 2;
          callback(targetTemperature, 'pending');
          setTimeout(() => {
            targetTemperature = newValue;
            callback(targetTemperature, 'completed');
          }, 5000);
        }, 5000);
      }
    };
    ```

1. Azure IoT Central 애플리케이션에서 업데이트된 설정을 처리할 수 있도록 다음을 파일에 추가합니다.

    ```javascript
    // Handle settings changes that come from Azure IoT Central via the device twin.
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
              twin.properties.reported.update(patch, (err) => console.log(`Sent setting update for ${setting}; ` +
                (err ? `error: ${err.toString()}` : `status: success`)));
            });
          }
        }
      });
    }
    ```

1. 다음 코드를 추가 하 여 IoT Central 응용 프로그램에서 전송 된 카운트다운 명령을 처리 합니다.

    ```javascript
    // Handle countdown command
    function onCountdown(request, response) {
      console.log('Received call to countdown');
    
      var countFrom = (typeof(request.payload.countFrom) === 'number' && request.payload.countFrom < 100) ? request.payload.countFrom : 10;
    
      response.send(200, (err) => {
        if (err) {
          console.error('Unable to send method response: ' + err.toString());
        } else {
          hubClient.getTwin((err, twin) => {
            function doCountdown(){
              if ( countFrom >= 0 ) {
                var patch = {
                  countdown:{
                    value: countFrom
                  }
                };
                sendDeviceProperties(twin, patch);
                countFrom--;
                setTimeout(doCountdown, 2000 );
              }
            }
    
            doCountdown();
          });
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

        // Create handler for countdown command
        hubClient.onDeviceMethod('countdown', onCountdown);

        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);

        // Get device twin from Azure IoT Central.
        hubClient.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up.
            var properties = {
              serialNumber: '123-ABC',
              manufacturer: 'Contoso'
            };
            sendDeviceProperties(twin, properties);

            // Apply device settings and handle changes to device settings.
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

명령줄 환경에서 다음 명령을 실행합니다.

```cmd/sh
node connectedAirConditionerAdv.js
```

운영자로서 Azure IoT Central 애플리케이션에 실제 디바이스에 대해 다음과 같은 일을 할 수 있습니다.

* **측정값** 페이지에서 원격 분석 데이터 보기:

    ![원격 분석 보기](media/howto-connect-nodejs/viewtelemetry.png)

* **측정** 페이지에서 위치를 확인 합니다.

    ![위치 측정값 보기](media/howto-connect-nodejs/viewlocation.png)

* **속성** 페이지에서, 디바이스에서 보낸 디바이스 속성 값을 봅니다. 장치 속성 타일은 장치가 연결 될 때 업데이트 됩니다.

    ![디바이스 속성 보기](media/howto-connect-nodejs/viewproperties.png)

* **설정** 페이지에서 팬 속도와 대상 온도를 설정 합니다.

    ![팬 속도 설정](media/howto-connect-nodejs/setfanspeed.png)

* **명령** 페이지에서 카운트다운 명령을 호출 합니다.

    ![호출 카운트다운 명령](media/howto-connect-nodejs/callcountdown.png)

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램에 일반 node.js 클라이언트를 연결 하는 방법을 배웠으므로 제안 된 다음 단계는 고유한 IoT 장치에 대 한 [사용자 지정 장치 템플릿을 설정](howto-set-up-template.md) 하는 방법을 배우는 것입니다.
