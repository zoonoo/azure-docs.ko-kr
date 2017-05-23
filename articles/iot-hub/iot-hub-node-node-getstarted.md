---
title: "Azure IoT Hub 시작(Node) | Microsoft Docs"
description: "Node.js용 Azure IoT SDK를 사용하여 장치에서 Azure IoT Hub로 장치-클라우드 메시지를 보내는 방법입니다. 메시지를 보내는 시뮬레이션된 장치 앱, ID 레지스트리에서 장치를 등록할 서비스 앱 및 IoT Hub에서 장치-클라우드로 메시지를 읽는 서비스 앱을 만듭니다."
services: iot-hub
documentationcenter: nodejs
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 56618522-9a31-42c6-94bf-55e2233b39ac
ms.service: iot-hub
ms.devlang: javascript
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2017
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 259477f26686eb43c1a06d8a72544d4ca8a72c71
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-node"></a>Node를 사용하여 IoT Hub에 시뮬레이션된 장치 연결
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

이 자습서의 끝 부분에서 다음의 세 가지 Node.js 콘솔 앱이 만들어집니다.

* **CreateDeviceIdentity.js**는 장치 ID 및 시뮬레이션된 보안 키를 만들어 시뮬레이션된 장치 앱에 연결합니다.
* **ReadDeviceToCloudMessages.js**는 시뮬레이션된 장치 앱에서 보낸 원격 분석을 표시합니다.
* **SimulatedDevice.js**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 MQTT 프로토콜을 사용하여 매초마다 원격 분석 메시지를 보냅니다.

> [!NOTE]
> [Azure IoT SDKs][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 Azure IoT SDK에 관한 정보를 제공합니다.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* Node.js 버전 0.10.x 이상
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

IoT Hub를 만들었습니다. 이 자습서 나머지 부분을 완료하는 데 필요한 IoT Hub 호스트 이름 및 IoT Hub 연결 문자열을 갖게 되었습니다.

## <a name="create-a-device-identity"></a>장치 ID 만들기
이 섹션에서는 IoT Hub의 ID 레지스트리에서 장치 ID를 만드는 Node.js 콘솔 앱을 만듭니다. ID 레지스트리에 항목이 있는 경우에만 장치를 IoT Hub에 연결할 수 있습니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]의 **ID 레지스트리** 섹션을 참조하세요. 이 콘솔 앱을 실행하면 장치-클라우드 메시지를 IoT Hub로 보낼 때 장치가 자체적으로 ID를 식별하는 데 사용할 수 있는 고유한 장치 ID와 키를 생성합니다.

1. **createdeviceidentity**라는 새 빈 폴더를 만듭니다. **createdeviceidentity** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **createdeviceidentity** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 서비스 SDK 패키지를 설치합니다.
   
    ```
    npm install azure-iothub --save
    ```
3. 텍스트 편집기를 사용하여 **createdeviceidentity** 폴더에 **CreateDeviceIdentity.js** 파일을 만듭니다.
4. **CreateDeviceIdentity.js** 파일 앞에 다음 `require` 문을 추가합니다.
   
    ```
    'use strict';
   
    var iothub = require('azure-iothub');
    ```
5. **CreateDeviceIdentity.js** 파일에 다음 코드를 추가하고 자리 표시자 값을 이전 섹션에서 만든 허브의 IoT Hub 연결 문자열로 바꿉니다. 
   
    ```
    var connectionString = '{iothub connection string}';
   
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```
6. 다음 코드를 추가하여 IoT Hub의 ID 레지스트리에서 장치 정의를 만듭니다. 이 코드는 장치 ID가 ID 레지스트리에 없는 경우 장치를 만들고, 있으면 기존 장치의 키를 반환합니다.
   
    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });
   
    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device ID: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.symmetricKey.primaryKey);
      }
    }
    ```
7. **CreateDeviceIdentity.js** 파일을 저장하고 닫습니다.
8. **createdeviceidentity** 응용 프로그램을 실행하려면 createdeviceidentity 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.
   
    ```
    node CreateDeviceIdentity.js 
    ```
9. **장치 ID**와 **장치 키**를 기록해 둡니다. 나중에 장치로 IoT Hub에 연결하는 응용 프로그램을 만들 때 이러한 값이 필요합니다.

> [!NOTE]
> IoT Hub ID 레지스트리는 장치 ID만 저장하여 IoT Hub에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 장치 ID 및 키와 개별 장치에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 응용 프로그램이 다른 장치별 메타데이터를 저장해야 할 경우 응용 프로그램별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]를 참조하세요.
> 
> 

<a id="D2C_node"></a>
## <a name="receive-device-to-cloud-messages"></a>장치-클라우드 메시지 받기
이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 읽는 Node.js 콘솔 앱을 만듭니다. IoT Hub가 [Event Hubs][lnk-event-hubs-overview]와 호환되는 끝점을 노출하여 장치-클라우드 메시지를 읽을 수 있습니다. 작업을 단순화하기 위해 이 자습서에서는 처리량이 높은 배포용이 아닌 기본적인 판독기를 만듭니다. [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial] 자습서는 대량의 장치-클라우드 메시지를 처리하는 방법을 보여 줍니다. [Event Hubs 시작][lnk-eventhubs-tutorial] 자습서는 Event Hubs의 메시지를 처리하는 방법에 대해 추가 정보를 제공하며 IoT Hub Event Hub 호환 끝점에 적용됩니다.

> [!NOTE]
> 장치-클라우드 메시지를 읽는 Event Hub와 호환 가능한 끝점은 항상 AMQP 프로토콜을 사용합니다.
> 
> 

1. **readdevicetocloudmessages**라는 빈 폴더를 만듭니다. **readdevicetocloudmessages** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **readdevicetocloudmessages** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-event-hubs** 패키지를 설치합니다.
   
    ```
    npm install azure-event-hubs --save
    ```
3. 텍스트 편집기를 사용하여 **readdevicetocloudmessages** 폴더에 **ReadDeviceToCloudMessages.js** 파일을 만듭니다.
4. **ReadDeviceToCloudMessages.js** 파일 앞에 다음 `require` 문을 추가합니다.
   
    ```
    'use strict';
   
    var EventHubClient = require('azure-event-hubs').Client;
    ```
5. 다음 변수 선언을 추가하고 자리 표시자 값을 허브의 IoT Hub 연결 문자열로 바꿉니다.
   
    ```
    var connectionString = '{iothub connection string}';
    ```
6. 콘솔에 출력하는 다음 두 함수를 추가합니다.
   
    ```
    var printError = function (err) {
      console.log(err.message);
    };
   
    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```
7. 다음 코드를 추가하여 **EventHubClient**를 만들고 IoT Hub에 대한 연결을 열고 각 파티션에 대한 수신기를 만듭니다. 이 응용 프로그램은 수신기를 만들 때 필터를 사용하여 수신기가 수신기 실행이 시작된 후 IoT Hub에 전송된 메시지만을 읽습니다. 이 필터는 테스트 환경에서 현재 메시지 집합을 볼 수 있어 유용합니다. 프로덕션 환경에서는 코드가 모든 메시지를 처리하는지 확인해야 합니다. 자세한 내용은 [IoT Hub 장치-클라우드 메시지를 처리하는 방법][lnk-process-d2c-tutorial] 자습서를 참조하세요.
   
    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```
8. **ReadDeviceToCloudMessages.js** 파일을 저장한 후 닫습니다.

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기
이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 Node.js 콘솔 앱을 작성합니다.

1. **simulateddevice**라는 빈 폴더를 만듭니다. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.
   
    ```
    npm init
    ```
2. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.
   
    ```
    npm install azure-iot-device azure-iot-device-mqtt --save
    ```
3. 텍스트 편집기를 사용하여 **simulateddevice** 폴더에 **SimulatedDevice.js** 파일을 만듭니다.
4. **SimulatedDevice.js** 파일 앞에 다음 `require` 문을 추가합니다.
   
    ```
    'use strict';
   
    var clientFromConnectionString = require('azure-iot-device-mqtt').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```
5. **connectionString** 변수를 추가하고 이 변수를 사용하여 **클라이언트** 인스턴스를 만듭니다. **{youriothostname}** 을 *IoT Hub 만들기* 섹션에서 만든 IoT Hub의 이름으로 바꿉니다. **{yourdevicekey}** 를 *장치 ID 만들기* 섹션에서 만든 장치 키 값으로 바꿉니다.
   
    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
   
    var client = clientFromConnectionString(connectionString);
    ```
6. 응용 프로그램에서 출력을 표시하도록 다음 함수를 추가합니다.
   
    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```
7. 다음과 같이 콜백을 만들고 **setInterval** 함수를 사용하여 메시지를 초당 하나씩 IoT Hub로 전송합니다.
   
    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
   
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var temperature = 20 + (Math.random() * 15);
            var humidity = 60 + (Math.random() * 20);            
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
            var message = new Message(data);
            message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
8. IoT Hub에 대한 연결을 열고 메시지를 보내기 시작합니다.
   
    ```
    client.open(connectCallback);
    ```
9. **SimulatedDevice.js** 파일을 저장한 후 닫습니다.

> [!NOTE]
> 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.
> 
> 

## <a name="run-the-apps"></a>앱 실행
이제 앱을 실행할 준비가 되었습니다.

1. **readdevicetocloudmessages** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub 모니터링을 시작합니다.
   
    ```
    node ReadDeviceToCloudMessages.js 
    ```
   
    ![장치-클라우드 메시지를 모니터링하는 Node.js IoT Hub 서비스 앱][7]
2. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 원격 분석 데이터를 IoT Hub로 전송을 시작합니다.
   
    ```
    node SimulatedDevice.js
    ```
   
    ![장치-클라우드 메시지를 보내는 Node.js IoT Hub 장치 앱][8]
3. [Azure Portal][lnk-portal]의 **사용량** 타일에 IoT Hub로 전송된 메시지 수가 표시됩니다.
   
    ![IoT Hub에 전송된 메시지의 수를 보여주는 Azure Portal 사용량 타일][43]

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Portal에서 새 IoT Hub를 구성한 다음, IoT Hub의 ID 레지스트리에서 장치 ID를 만들었습니다. 장치-클라우드 메시지를 IoT Hub로 보내기 위해 시뮬레이션된 장치 앱을 사용하는 이 장치 ID를 사용했습니다. IoT Hub에서 받은 메시지를 표시하는 앱도 만들었습니다. 

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

* [장치 연결][lnk-connect-device]
* [장치 관리 시작][lnk-device-management]
* [Azure IoT Hub 시작][lnk-gateway-SDK]

IoT 솔루션을 확장하고 대량의 장치-클라우드 메시지를 처리하는 방법을 알아보려면 [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial] 자습서를 참조하세요.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

