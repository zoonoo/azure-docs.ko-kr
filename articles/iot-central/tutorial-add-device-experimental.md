---
title: Azure IoT Central 애플리케이션에 실제 장치 추가 | Microsoft Docs
description: 운영자로서 Azure IoT Central 애플리케이션에 실제 장치를 추가합니다.
author: sandeeppujar
ms.author: sandeepu
ms.date: 02/01/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5933f74dcedb579023d187061229cdd53bce6414
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819439"
---
# <a name="tutorial-add-a-real-device-to-your-azure-iot-central-application"></a>자습서: Azure IoT Central 애플리케이션에 실제 장치 추가

이 자습서에서는 Microsoft Azure IoT Central 애플리케이션에 실제 장치를 추가하고 구성하는 방법을 알려줍니다.

이 자습서는 2부로 구성되어 있습니다.

1. 먼저 운영자로서 Azure IoT Central 애플리케이션에서 실제 장치를 추가하고 구성하는 방법을 알아봅니다. 1부의 끝에 2부에서 사용할 연결 문자열을 검색합니다.
2. 그런 다음, 디바이스 개발자로서 실제 디바이스의 코드에 대해 알아봅니다. 1부의 연결 문자열을 샘플 코드에 추가합니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 새 실제 디바이스 추가
> * 실제 디바이스 구성
> * 애플리케이션에서 실제 장치에 대한 연결 문자열 가져오기
> * 클라이언트 코드가 애플리케이션에 매핑되는 방법 이해
> * 실제 디바이스에 대한 클라이언트 코드 구성

## <a name="prerequisites"></a>필수 조건

시작하기 전에 빌더는 최소 첫 번째 작성기 자습서를 완료해야 Azure IoT Central 애플리케이션을 만듭니다.

* [새 디바이스 유형 정의](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)(필수)
* [디바이스에 대한 규칙 및 작업 구성](tutorial-configure-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)(선택 사항)
* [연산자의 뷰 사용자 지정](tutorial-customize-operator-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)(선택 사항)

## <a name="add-a-real-device"></a>실제 디바이스 추가

애플리케이션에 실제 장치를 추가하려면 [새 장치 유형을 정의](tutorial-define-device-type-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) 자습서에서 만든 **연결 된 공조 장치** 템플릿을 사용합니다.

1. 운영자로서 새 디바이스를 추가하려면 왼쪽 탐색 메뉴에서 **Device Explorer**를 선택합니다.

   ![연결된 공조 디바이스를 표시하는 Device Explorer 페이지](media/tutorial-add-device-experimental/explorer.png)

   **Device Explorer**는 작성기가 디바이스 템플릿을 만들 때 자동으로 생성된 시뮬레이션된 디바이스 및 **연결된 공조 디바이스**를 표시합니다.

2. 실제 연결된 공조 디바이스에 연결을 시작하려면 **새로 만들기**, **실제 디바이스**를 차례로 선택합니다.

   ![실제 연결된 새 공조 디바이스 추가 시작](media/tutorial-add-device-experimental/newreal.png)

3. 디바이스 ID(**소문자여야 함**)를 입력하거나 제안된 디바이스 ID를 사용합니다. 새 디바이스의 이름을 입력하고 **만들기**를 선택할 수도 있습니다.  

   ![디바이스 이름 바꾸기](media/tutorial-add-device-experimental/rename.png)

## <a name="configure-a-real-device"></a>실제 디바이스 구성

**연결된 공조 디바이스** 템플릿에서 실제 디바이스를 만듭니다. 디바이스에 대한 정보를 기록하려면 **설정**을 사용하여 디바이스를 구성하고 속성 값을 설정합니다.

1. **설정** 페이지에서 **온도 설정** 설정 상태가 **업데이트가 되지 않습니다**. 실제 장치가 애플리케이션에 연결되고 설정 대로 작동하는 것을 인식할 때까지 이 상태를 계속 유지합니다. 

    ![설정 동기화 표시](media/tutorial-add-device-experimental/settingssyncing.png)

2. 실제 연결된 새 공조 디바이스에 대한 **속성** 페이지에서 서비스의 위치와 마지막 서비스 날짜는 모두 편집 가능한 디바이스 속성입니다. 일련 번호 및 펌웨어 버전 필드는 장치가 애플리케이션에 연결될 때까지 비어 있습니다. 이들은 디바이스에서 보내는 읽기 전용 값으로, 편집할 수 없습니다.

    ![실제 디바이스에 대한 디바이스 속성](media/tutorial-add-device-experimental/setproperties1.png)

3. 실제 디바이스에 대한 **측정**, **규칙** 및 **대시보드** 페이지를 볼 수 있습니다.

## <a name="generate-connection-string-for-real-device-from-application"></a>애플리케이션에서 실제 장치에 대한 연결 문자열 생성

디바이스 개발자는 디바이스에서 실행되는 코드에서 실제 디바이스에 대한 *연결 문자열*을 포함해야 합니다. 연결 문자열을 사용하면 장치를 Azure IoT Central 애플리케이션에 안전하게 연결할 수 있습니다. 다음 단계에서는 Node.js로 작성한 클라이언트 노드 준비의 일환으로 연결 문자열을 생성합니다. Node.js 애플리케이션은 실제 연결된 공조를 나타냅니다. 

## <a name="prepare-the-client-code"></a>클라이언트 코드 준비

이 문서의 예제 코드는 [Node.js](https://nodejs.org/)에서 작성되고 다음을 하기에 충분한 코드를 보여줍니다.

* 장치로서 Azure IoT Central 애플리케이션에 연결합니다.
* 연결된 공조 디바이스로써 온도 원격 분석을 보냅니다.
* Azure IoT Central 애플리케이션에 장치 속성을 보냅니다.
* **온도 설정** 설정을 사용하는 운영자에게 응답합니다.
* Azure IoT Central 애플리케이션에서 Echo 명령을 처리합니다.

[다음 단계](#next-steps)에서 참조하는 "방법" 문서는 더 완벽한 샘플을 제공하고 다른 프로그래밍 언어의 사용을 보여줍니다. 디바이스를 Azure IoT Central에 연결하는 방법에 대한 자세한 내용은 [디바이스 연결](concepts-connectivity-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) 문서를 참조하세요.

다음 단계에서는 [Node.js](https://nodejs.org/) 샘플을 준비하는 방법을 보여줍니다.

1. 컴퓨터에 [Node.js](https://nodejs.org/) 버전 4.0.x 이상을 설치합니다. Node.js는 다양한 운영 체제에 사용할 수 있습니다.

1. 컴퓨터에 `connectedairconditioner`이라는 폴더를 만듭니다.

1. 명령줄 환경에서 만든 `connectedairconditioner` 폴더로 이동합니다.

1. 다음 명령을 실행하여 DPS 키 생성기를 설치합니다.

    ```cmd/sh
    npm i -g dps-keygen
    ```

   [여기에서 명령줄 도구](https://www.npmjs.com/package/dps-keygen)에 대해 자세히 알아보세요.

1. IoT Central이 제공한 장치 정보로부터 애플리케이션의 장치 인스턴스에 대한 연결 문자열이 생성됩니다.

   IoT Central 포털로 돌아갑니다. 실제 연결된 공조의 디바이스 화면에서 **연결**을 선택합니다.

   ![연결 정보 링크를 보여주는 디바이스 페이지](media/tutorial-add-device-experimental/connectionlink.png)

1. 디바이스 연결 페이지에서 범위 ID, 디바이스 ID 및 기본 키를 복사하여 텍스트 편집기에 붙여 넣은 다음, 저장합니다. 다음 단계에서 이 값을 사용합니다.

   ![연결 정보](media/tutorial-add-device-experimental/device-connect.png)

1. 명령줄 환경으로 돌아가 다음을 실행하여 연결 문자열을 생성합니다.

    ```cmd/sh
    dps_keygen <scope_id> <device_id> <Primary Key>
    ```

   출력을 복사하여 새 파일(예: connection.txt)에 저장합니다.

1. Node.js 프로젝트를 초기화하려면 모든 기본값을 허용하는 다음 명령을 실행합니다.

    ```cmd/sh
    npm init
      ```

1. 필요한 패키지를 설치하려면 다음 명령을 실행하십시오.

    ```cmd/sh
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```

1. 텍스트 편집기를 사용하여 `connectedairconditioner` 폴더에서 **ConnectedAirConditioner.js**이라는 파일을 만듭니다.

1. **ConnectedAirConditioner.js** 파일의 시작 부분에 다음 `require` 문을 추가합니다.

    ```javascript
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    var ConnectionString = require('azure-iot-device').ConnectionString;
    ```

1. 다음 변수 선언을 파일에 추가합니다.

    ```javascript
    var connectionString = '{your device connection string}';
    var targetTemperature = 0;
    var client = clientFromConnectionString(connectionString);
    ```

    > [!NOTE]
    > 이후 단계에서 자리 표시자 `{your device connection string}`을 업데이트합니다.

1. 지금까지 한 변경을 저장하지만 파일을 열어 놓습니다.

## <a name="understand-how-client-code-maps-to-the-application"></a>클라이언트 코드가 애플리케이션에 매핑되는 방법 이해

이전 섹션에서는 Azure IoT Central 애플리케이션에 연결하는 애플리케이션에 대한 기본 Node.js 프로젝트를 만들었습니다. 이 섹션에서는 다음에 코드를 추가합니다.

* Azure IoT Central 애플리케이션에 연결합니다.
* Azure IoT Central 애플리케이션에 원격 분석을 보냅니다.
* Azure IoT Central 애플리케이션에 장치 속성을 보냅니다.
* Azure IoT Central 애플리케이션에서 설정을 받습니다.
* Azure IoT Central 애플리케이션에서 Echo 명령을 처리합니다.

1. Azure IoT Central 애플리케이션에 온도 원격 분석을 보내려면 다음 코드를 **ConnectedAirConditioner.js** 파일에 추가합니다.

    ```javascript
    // Send device telemetry.
    function sendTelemetry() {
      var temperature = targetTemperature + (Math.random() * 15);
      var data = JSON.stringify({ temperature: temperature });
      var message = new Message(data);
      client.sendEvent(message, (err, res) => console.log(`Sent message: ${message.getData()}` +
        (err ? `; error: ${err.toString()}` : '') +
        (res ? `; status: ${res.constructor.name}` : '')));
    }
    ```

    보내는 JSON의 필드 이름이 디바이스 템플릿에서 온도 원격 분석에 대해 지정한 필드 이름과 일치해야 합니다. 이 예제에서는 필드 이름이 **온도**합니다.

1. **firmwareVersion** 및 **serialNumber** 같은 디바이스 속성을 보내려면 다음 정의를 추가합니다.

    ```javascript
    // Send device properties
    function sendDeviceProperties(twin) {
      var properties = {
        firmwareVersion: "9.75",
        serialNumber: "10001"
      };
      twin.properties.reported.update(properties, (errorMessage) => 
      console.log(` * Sent device properties ` + (errorMessage ? `Error: ${errorMessage.toString()}` : `(success)`)));
    }
    ```

1. **setTemperature**와 같이 디바이스가 지원하는 설정을 정의하려면 다음 정의를 추가합니다.

    ```javascript
    // Add any settings your device supports
    // mapped to a function that is called when the setting is changed.
    var settings = {
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

1. Azure IoT Central에서 전송된 설정을 처리하려면 적절한 디바이스 코드를 찾아 실행하는 다음 함수를 추가합니다.

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

    이 함수:

    * 원하는 속성을 보내는 Azure IoT Central을 지켜봅니다.
    * 설정 변경 처리를 위해 호출하려면 적절한 함수를 찾습니다.
    * Azure IoT Central 애플리케이션으로 승인을 돌려 보냅니다.

1. Azure IoT Central 애플리케이션에서 **echo** 같은 명령에 응답하려면 다음 정의를 추가합니다.

    ```javascript
    // Respond to the echo command
    function onCommandEcho(request, response) {
      // Display console info
      console.log(' * Echo command received');
      // Respond
      response.send(10, 'Success', function (errorMessage) {});
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
        // Send telemetry measurements to Azure IoT Central every 1 second.
        setInterval(sendTelemetry, 1000);
        // Setup device command callbacks
        client.onDeviceMethod('echo', onCommandEcho);
        // Get device twin from Azure IoT Central.
        client.getTwin((err, twin) => {
          if (err) {
            console.log(`Error getting device twin: ${err.toString()}`);
          } else {
            // Send device properties once on device start up
            sendDeviceProperties(twin);
            // Apply device settings and handle changes to device settings.
            handleSettings(twin);
          }
        });
      }
    };

    client.open(connectCallback);
    ```

1. 지금까지 한 변경을 저장하지만 파일을 열어 놓습니다.

## <a name="configure-client-code-for-the-real-device"></a>실제 디바이스에 대한 클라이언트 코드 구성

<!-- Add the connection string to the sample code, build, and run --> Azure IoT Central 애플리케이션에 연결할 클라이언트 코드를 구성하려면 이 자습서의 앞부분에서 언급한 실제 장치에 대한 연결 문자열을 추가해야 합니다.

1. **ConnectedAirConditioner.js** 파일에서 다음 코드 줄을 찾습니다.

    ```javascript
    var connectionString = '{your device connection string}';
    ```

1. `{your device connection string}`을 실제 디바이스의 연결 문자열로 바꿉니다. 앞서 텍스트 편집기에서 연결 문자열을 저장했습니다.

1. **ConnectedAirConditioner.js** 파일에 변경 내용을 저장합니다.

1. 샘플을 실행하려면 명령줄 환경에서 다음 명령을 실행합니다.

    ```cmd/sh
    node ConnectedAirConditioner.js
    ```

    > [!NOTE]
    > 이 명령을 실행하는 경우 `connectedairconditioner` 폴더에 있는지 확인합니다.

1. 애플리케이션은 출력을 콘솔에 인쇄합니다.

   ![클라이언트 애플리케이션 출력](media/tutorial-add-device-experimental/output.png)

1. 약 30초 후 디바이스 **측정** 페이지에서 원격 분석을 확인합니다.

   ![실제 원격 분석](media/tutorial-add-device-experimental/realtelemetry.png)

1. **설정** 페이지에서 설정이 동기화됐는지 확인할 수 있습니다. 디바이스가 처음 연결된 경우 설정 값을 수신하고 변경 내용을 승인했습니다.

   ![동기화됨 설정](media/tutorial-add-device-experimental/settingsynced.png)

1. **설정** 페이지에서 디바이스 온도를 **95**로 설정하고 **디바이스 업데이트**를 선택합니다. 샘플 애플리케이션이 이 변경 내용을 수신하고 처리합니다.

   ![수신 및 처리 설정](media/tutorial-add-device-experimental/receivesetting.png)

   > [!NOTE]
   > 두 개의 "업데이트 설정" 메시지가 있습니다. `pending` 상태가 전송될 경우의 메시지 및 `completed` 상태가 전송될 경우의 메시지입니다.

1. **측정** 페이지에서 디바이스가 더 높은 온도 값을 전송하는 것을 확인할 수 있습니다.

    ![온도 원격 분석이 더 높습니다.](media/tutorial-add-device-experimental/highertemperature.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="nextstepaction"]
> * 새 실제 디바이스 추가
> * 새 디바이스 구성
> * 애플리케이션에서 실제 장치에 대한 연결 문자열 가져오기
> * 클라이언트 코드가 애플리케이션에 매핑되는 방법 이해
> * 실제 디바이스에 대한 클라이언트 코드 구성

Azure IoT Central 애플리케이션에 실제 장치를 연결했으므로 제안된 다음 단계는 다음과 같습니다.

운영자로서 방법을 알아볼 수 있습니다.

* [디바이스 관리](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [디바이스 설정 사용](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [사용자 지정 분석 만들기](howto-use-device-sets-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)

디바이스 개발자로서 다음 작업 방법을 알아볼 수 있습니다.

* [DevKit 준비 및 연결](howto-connect-devkit-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Raspberry Pi 준비 및 연결](howto-connect-raspberry-pi-python.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [Azure IoT Central 애플리케이션에 일반 Node.js 클라이언트 연결](howto-connect-nodejs-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
* [코드 사용자 지정][lnk-nodejs-device-ref]


[lnk-nodejs-device-ref]: /javascript/api/azure-iot-device/?view=azure-iot-typescript-latest