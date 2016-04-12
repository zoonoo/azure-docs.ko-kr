## 장치 ID 만들기

이 섹션에서는 IoT Hub의 ID 레지스트리에서 새 장치 ID를 만드는 Node.js 콘솔 앱을 작성합니다. 장치 ID 레지스트리에 항목이 없는 경우 장치를 IoT Hub에 연결할 수 없습니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]의 **장치 ID 레지스트리** 섹션을 참조하세요. 이 콘솔 응용 프로그램을 실행하면 장치-클라우드 메시지를 IoT Hub로 보낼 때 장치가 자체적으로 ID를 식별할 수 있는 고유한 장치 ID와 키를 생성합니다.

1. **createdeviceidentity**라는 새로운 빈 폴더를 만듭니다. **createdeviceidentity** 폴더에서 명령 프롬프트에 다음 명령을 사용하여 새 package.json 패키지 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```
    npm init
    ```

2. **createdeviceidentity** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 패키지를 설치합니다.

    ```
    npm install azure-iothub --save
    ```

3. 텍스트 편집기를 사용하여 **createdeviceidentity** 폴더에 새 **CreateDeviceIdentity.js** 파일을 만듭니다.

4. **CreateDeviceIdentity.js** 파일의 시작 부분에 다음 `require` 코드를 추가합니다.

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. **CreateDeviceIdentity.js** 파일에 다음 코드를 추가하고, 자리 표시자 값을 이전 섹션에서 만든 IoT Hub의 연결 문자열로 대체합니다.

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

9. **장치 ID** 및 **장치 키**를 적어 둡니다. 나중에 장치로 IoT Hub에 연결하는 응용 프로그램을 만들 때 필요합니다.

> [AZURE.NOTE] IoT Hub ID 레지스트리는 장치 ID만 저장하여 허브에 보안 액세스를 사용합니다. 보안 자격 증명으로 사용하기 위해 장치 ID 및 키와 개별 장치에 대해 액세스하지 못하도록 설정할 수 있는 사용/사용 안 함 플래그를 저장합니다. 응용 프로그램이 다른 장치별 메타데이터를 저장해야 하는 경우 응용 프로그램별 저장소를 사용해야 합니다. 자세한 내용은 [IoT Hub 개발자 가이드][lnk-devguide-identity]를 참조하세요.

## 장치-클라우드 메시지 받기

이 섹션에서는 IoT Hub에서 장치-클라우드 메시지를 읽는 Node.js 콘솔 앱을 만듭니다. IoT Hub가 [이벤트 허브][lnk-event-hubs-overview]와 호환되는 끝점을 노출하여 장치-클라우드 메시지를 읽을 수 있습니다. 작업을 단순화하기 위해 이 자습서에서는 처리량이 높은 배포용이 아닌 기본적인 판독기를 만듭니다. [장치-클라우드 메시지 처리][lnk-processd2c-tutorial] 자습서에서는 규모로 장치-클라우드 메시지를 처리하는 방법을 보여 줍니다. [이벤트 허브 시작][lnk-eventhubs-tutorial] 자습서는 이벤트 허브에서 메시지를 처리하는 방법에 대한 추가 정보를 제공하고 IoT Hub 이벤트 허브 호환 끝점에 적용됩니다.

1. 이름이 **readdevicetocloudmessages**인 새 빈 폴더를 만듭니다. **readdevicetocloudmessages** 폴더에서 명령 프롬프트에 다음 명령을 사용하여 새 package.json 패키지 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```
    npm init
    ```

2. **readdevicetocloudmessages** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **amqp10** 및 **bluebird** 패키지를 설치합니다.

    ```
    npm install amqp10 bluebird --save
    ```

3. 텍스트 편집기를 사용하여 **readdevicetocloudmessages** 폴더에 새**ReadDeviceToCloudMessages.js** 파일을 만듭니다.

4. **ReadDeviceToCloudMessages.js** 파일의 첫 부분에 다음 `require` 코드를 추가합니다.

    ```
    'use strict';

    var AMQPClient = require('amqp10').Client;
    var Policy = require('amqp10').Policy;
    var translator = require('amqp10').translator;
    var Promise = require('bluebird');
    ```

5. 자리 표시자를 앞서 기록해 둔 갚으로 대체하여 다음 변수 선언을 추가합니다. **{이벤트 허브 호환 네임스페이스}** 자리 표시자 값은 포털의 **이벤트 허브 호환 끝점** 필드에서 나오며 **namespace.servicebus.windows.net**(**sb://* 접두사가 없음) 형태입니다.

    ```
    var protocol = 'amqps';
    var eventHubHost = '{your event hub-compatible namespace}';
    var sasName = 'iothubowner';
    var sasKey = '{your iot hub key}';
    var eventHubName = '{your event hub-compatible name}';
    var numPartitions = 2;
    ```

    > [AZURE.NOTE] 이 코드는 F1(무료) 계층에서 IoT Hub를 만들었다고 가정합니다. 무료 IoT Hub에는 "0" 및 "1"이라는 두 개의 파티션이 있습니다. 다른 가격 책정 계층 중 하나를 사용하여 IoT Hub를 만든 경우 각 파티션에 대해 **MessageReceiver**를 만들도록 코드를 조정해야 합니다.

6. 다음 필터 정의를 추가합니다. 이 응용 프로그램은 수신기를 만들 때 필터를 사용하여 수신기가 수신기 실행이 시작된 후 IoT Hub에 전송된 메시지만을 읽습니다. 테스트 환경에서 유용하므로 현재 메시지 집합을 볼 수 있지만 프로덕션 환경에서 코드가 모든 메시지를 처리하고 있는지 확인해야 합니다. 자세한 정보는 [IoT Hub가 장치-클라우드 메시지를 처리하는 방법][lnk-processd2c-tutorial] 자습서를 참조하세요.

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

11. **ReadDeviceToCloudMessages.js** 파일을 저장하고 닫습니다.

<!-- Links -->

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide.md#identityregistry
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-processd2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

<!---HONumber=AcomDC_0323_2016-->