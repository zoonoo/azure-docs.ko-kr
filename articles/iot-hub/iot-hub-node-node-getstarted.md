<properties
	pageTitle="Node.js용 Azure IoT Hub 시작 | Microsoft Azure"
	description="Node.js용 Azure IoT Hub 시작 자습서입니다. Microsoft Azure IoT SDK를 포함한 Azure IoT Hub 및 Node.js를 사용하여 사물 인터넷의 솔루션을 구현합니다."
	services="iot-hub"
	documentationCenter="nodejs"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/22/2016"
     ms.author="dobett"/>

# Node.js용 Azure IoT Hub 시작

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

## 소개

Azure IoT Hub는 수백만의 IoT(사물 인터넷) 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. IoT 프로젝트가 직면하는 큰 과제 중 하나는 장치를 안정적이고 안전하게 솔루션 백 엔드에 연결하는 방법입니다. 이러한 문제를 해결하기 위해 IoT Hub는 다음을 수행합니다.

- 신뢰할 수 있는 장치-클라우드 및 클라우드-장치 하이퍼 스케일 메시징을 제공합니다.
- 장치 단위 보안 자격 증명 및 액세스 제어를 사용하여 통신을 보호합니다.
- 가장 인기 있는 언어 및 플랫폼에 대한 장치 라이브러리를 포함합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

- Azure 포털을 사용하여 IoT Hub를 만듭니다.
- IoT Hub에서 장치 ID를 만듭니다.
- 클라우드 백 엔드에 원격 분석을 전송하는 시뮬레이션된 장치를 만듭니다.

이 자습서의 끝 부분에서 다음의 세 가지 Node.js 콘솔 응용 프로그램이 만들어집니다.

* **CreateDeviceIdentity.js**는 장치 ID 및 시뮬레이션된 보안 키를 만들어 시뮬레이션된 장치에 연결합니다.
* **ReadDEviceToCloudMessages.js**는 시뮬레이션된 장치에서 보낸 원격 분석을 표시합니다.
* **SimulatedDevice.js**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 AMQPS 프로토콜을 사용하여 매초마다 원격 분석 메시지를 보냅니다.

> [AZURE.NOTE] [IoT Hub Sdk][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행하기 위해 두 응용 프로그램을 빌드하는 데 사용할 수 있는 다양한 SDK에 관한 정보를 제공합니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Node.js 버전 0.12.x 이상 <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Node.js을 설치하는 방법을 설명합니다.

+ 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

마지막 단계로 IoT Hub 블레이드에서 **설정**을 클릭한 다음 **설정** 블레이드에서 **메시징**을 클릭합니다. **메시징** 블레이드에서 **이벤트 허브 호환 이름** 및 **이벤트 허브 호환 끝점**을 기록해 둡니다. **read-d2c-messages** 응용 프로그램을 만들 때 이러한 값이 필요합니다.

![][6]

이제 IoT Hub를 만들었고 이 자습서의 나머지 부분을 완료해야 할 IoT Hub 호스트 이름, IoT Hub 연결 문자열, 이벤트 허브 호환 이름 및 이벤트 허브 호환 끝점 값이 있습니다.

## 장치 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에서 새 장치 ID를 만드는 Node.js 콘솔 앱을 작성합니다. 장치 ID 레지스트리에 항목이 없는 경우 장치를 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]의 **장치 ID 레지스트리** 섹션을 참조하세요. 이 콘솔 응용 프로그램을 실행하면 장치-클라우드 메시지를 IoT Hub로 보낼 때 장치가 자체적으로 ID를 식별할 수 있는 고유한 장치 ID와 키를 생성합니다.

1. **createdeviceidentity**라는 새 빈 폴더를 만듭니다. **createdeviceidentity** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 새 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```
    npm init
    ```

2. **createdeviceidentity** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 패키지를 설치합니다.

    ```
    npm install azure-iothub --save
    ```

3. 텍스트 편집기를 사용하여 **createdeviceidentity** 폴더에 새 **CreateDeviceIdentity.js** 파일을 만듭니다.

4. **CreateDeviceIdentity.js** 파일 앞에 다음 `require` 문을 추가합니다.

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. **CreateDeviceIdentity.js** 파일에 다음 코드를 추가하고 자리 표시자 값을 이전 섹션에서 만든 IoT Hub의 연결 문자열로 바꿉니다.

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. 다음 코드를 추가하여 IoT Hub의 장치 ID 레지스트리에서 새 장치 정의를 만듭니다. 이 코드는 장치 ID가 레지스트리에 없는 경우 새 장치를 만들고, 있으면 기존 장치의 키를 반환합니다.

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstDevice';
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
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. **CreateDeviceIdentity.js** 파일을 저장하고 닫습니다.

8. **createdeviceidentity** 응용 프로그램을 실행하려면 createdeviceidentity 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.

    ```
    node CreateDeviceIdentity.js 
    ```

9. **장치 ID**와 **장치 키**를 기록해 둡니다. 나중에 장치로 IoT Hub에 연결하는 응용 프로그램을 만들 때 필요합니다.

> [AZURE.NOTE] IoT Hub ID 레지스트리는 장치 ID만 저장하여 허브에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 장치 ID 및 키와 개별 장치에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 응용 프로그램이 다른 장치별 메타데이터를 저장해야 할 경우 응용 프로그램별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]를 참조하세요.

## 장치-클라우드 메시지 받기

이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 읽는 Node.js 콘솔 앱을 만듭니다. IoT Hub가 [이벤트 허브][lnk-event-hubs-overview]와 호환되는 끝점을 노출하여 장치-클라우드 메시지를 읽을 수 있습니다. 작업을 단순화하기 위해 이 자습서에서는 처리량이 높은 배포용이 아닌 기본적인 판독기를 만듭니다. [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial] 자습서는 대량의 장치-클라우드 메시지를 처리하는 방법을 보여줍니다. [이벤트 허브 시작][lnk-eventhubs-tutorial] 자습서는 이벤트 허브의 메시지를 처리하는 방법에 대해 추가 정보를 제공하며 IoT Hub 이벤트 허브 호환 끝점에 적용됩니다.

> [AZURE.NOTE] 장치-클라우드 메시지를 읽는 이벤트 허브와 호환 가능한 끝점은 항상 AMQPS 프로토콜을 사용합니다.

1. **readdevicetocloudmessages**라는 빈 폴더를 새로 만듭니다. **readdevicetocloudmessages** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 새 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```
    npm init
    ```

2. **readdevicetocloudmessages** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **amqp10** 및 **bluebird** 패키지를 설치합니다.

    ```
    npm install amqp10 bluebird --save
    ```

3. 텍스트 편집기를 사용하여 **readdevicetocloudmessages** 폴더에 새 **ReadDeviceToCloudMessages.js** 파일을 만듭니다.

4. **ReadDeviceToCloudMessages.js** 파일 앞에 다음 `require` 문을 추가합니다.

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. 자리 표시자를 앞서 기록해 둔 갚으로 대체하여 다음 변수 선언을 추가합니다. **{이벤트 허브와 호환되는 네임스페이스}** 자리 표시자 값은 포털의 **이벤트 허브 호환 끝점** 필드 값이 적용되며 **namespace.servicebus.windows.net** 형식을 사용합니다(**sb://* 접두사를 사용하지 않음).

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] 이 코드는 F1(무료) 계층에서 IoT Hub를 만들었다고 가정합니다. 무료 IoT Hub에는 "0" 및 "1"이라는 두 개의 파티션이 있습니다. 다음 중 한 가지 가격 책정 계층을 사용하여 IoT Hub를 만든 경우 코드를 조정하여 각 파티션에 대해 **MessageReceiver**를 만들어야 합니다.

6. 다음 필터 정의를 추가합니다. 이 응용 프로그램은 수신기를 만들 때 필터를 사용하여 수신기가 수신기 실행이 시작된 후 IoT Hub에 전송된 메시지만을 읽습니다. 테스트 환경에서는 현재 메시지 집합을 볼 수 있어 유용하지만 프로덕션 환경에서는 코드가 모든 메시지를 처리하는지 확인해야 합니다. 자세한 내용은 [IoT Hub 장치-클라우드 메시지 처리 방법][lnk-process-d2c-tutorial] 자습서를 참조하세요.

    ```
    var filterOffset = new Date().getTime();
    var filterOption;
    if (filterOffset) {
      filterOption = {
      attach: { source: { filter: {
      'apache.org:selector-filter:string': translator(
        ['described', ['symbol', 'apache.org:selector-filter:string'], ['string', "amqp.annotation.x-opt-enqueuedtimeutc > " + filterOffset + ""]])
        } } }
      };
    }
    ```

7. 다음 코드를 추가하여 수신 주소 및 AMQP 클라이언트를 만듭니다.

    ```
    var uri = protocol + '://' + encodeURIComponent(sasName) + ':' + encodeURIComponent(sasKey) + '@' + eventHubHost;
    var recvAddr = eventHubName + '/ConsumerGroups/$default/Partitions/';
    
    var client = new AMQPClient(Policy.EventHub);
    ```

8. 콘솔에 출력하는 다음 두 함수를 추가합니다.

    ```
    var messageHandler = function (partitionId, message) {
      console.log('Received(' + partitionId + '): ', message.body);
    };
    
    var errorHandler = function(partitionId, err) {
      console.warn('** Receive error: ', err);
    };
    ```

9. 필터를 사용하는 특정 파티션에 대한 수신기 역할을 하는 다음 함수를 추가합니다.

    ```
    var createPartitionReceiver = function(partitionId, receiveAddress, filterOption) {
      return client.createReceiver(receiveAddress, filterOption)
        .then(function (receiver) {
          console.log('Listening on partition: ' + partitionId);
          receiver.on('message', messageHandler.bind(null, partitionId));
          receiver.on('errorReceived', errorHandler.bind(null, partitionId));
        });
    };
    ```

10. 이벤트 허브 호환 끝점에 연결하는 다음 코드를 추가하고 수신기를 시작합니다.

    ```
    client.connect(uri)
      .then(function () {
        var partitions = [];
        for (var i = 0; i < numPartitions; ++i) {
          partitions.push(createPartitionReceiver(i, recvAddr + i, filterOption));
        }
        return Promise.all(partitions);
    })
    .error(function (e) {
        console.warn('Connection error: ', e);
    });
    ```

11. **ReadDeviceToCloudMessages.js** 파일을 저장한 후 닫습니다.

## 시뮬레이션된 장치 앱 만들기

이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 Node.js 콘솔 앱을 작성합니다.

1. **simulateddevice**라는 빈 폴더를 새로 만듭니다. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 새 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```
    npm init
    ```

2. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device-amqp** 패키지를 설치합니다.

    ```
    npm install azure-iot-device-amqp --save
    ```

3. 텍스트 편집기를 사용하여 **simulateddevice** 폴더에 새 **SimulatedDevice.js** 파일을 만듭니다.

4. **SimulatedDevice.js** 파일 앞에 다음 `require` 문을 추가합니다.

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. **connectionString** 변수를 추가하고 이 변수를 사용하여 장치 클라이언트를 만듭니다. **{youriothubname}**을 IoT Hub 이름으로 바꾸고 **{yourdeviceid}** 및 **{yourdevicekey}**를 *장치 ID 만들기* 섹션에서 만든 장치 값으로 바꿉니다.

    ```
    var connectionString = 'HostName={youriothubname}.azure-devices.net;DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    
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

7. 콜백을 만들고 **setInterval** 함수를 사용하여 초당 하나의 새 메시지를 IoT Hub로 전송합니다.

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'mydevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 2000);
      }
    };
    ```

8. IoT Hub에 대한 연결을 열고 메시지를 보내기 시작합니다.

    ```
    client.open(connectCallback);
    ```

9. **SimulatedDevice.js** 파일을 저장한 후 닫습니다.

> [AZURE.NOTE] 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.


## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1. **readdevicetocloudmessages** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub 모니터링을 시작합니다.

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![][7]

2. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 원격 분석 데이터를 IoT Hub로 전송을 시작합니다.

    ```
    node SimulatedDevice.js
    ```

    ![][8]

3. [Azure 포털][lnk-portal]의 **사용량** 타일에 허브로 전송된 메시지 수가 표시됩니다.

    ![][43]

## 다음 단계

이 자습서에서 포털에서 새 IoT Hub를 구성한 다음, 허브의 ID 레지스트리에서 장치 ID를 만들었습니다. 장치-클라우드 메시지를 허브로 보내기 위해 시뮬레이션된 장치 앱을 사용하는 이 장치 ID를 사용하고 허브에서 받은 메시지를 표시하는 앱을 만들었습니다. IoT Hub 기능 및 다른 IoT 시나리오는 다음의 자습서에서 계속해서 탐색할 수 있습니다.

- [IoT Hub를 사용하여 클라우드-장치 메시지 보내기][lnk-c2d-tutorial]는 장치에 메시지를 보내고 IoT Hub에서 생성된 전달 피드백을 처리하는 방법을 보여줍니다.
- [장치-클라우드 메시지 처리][lnk-process-d2c-tutorial]는 장치에서 들어오는 대화형 메시지 및 원격 분석을 안정적으로 처리하는 방법을 보여 줍니다.
- [장치에서 파일 업로드][lnk-upload-tutorial]는 장치에서 파일을 쉽게 업로드하기 위해 클라우드-장치 메시지를 사용하는 패턴을 설명합니다.

<!-- Images. -->
[6]: ./media/iot-hub-node-node-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/doc/devbox_setup.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-upload-tutorial]: iot-hub-csharp-csharp-file-upload.md

[lnk-hub-sdks]: iot-hub-sdks-summary.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0511_2016-->