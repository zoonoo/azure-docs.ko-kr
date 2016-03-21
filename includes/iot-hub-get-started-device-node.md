## 시뮬레이션된 장치 앱 만들기

이 섹션에서는 IoT Hub로 장치-클라우드 메시지를 전송하는 장치를 시뮬레이션하는 Node.js 콘솔 앱을 작성합니다.

1. **simulateddevice**라는 새로운 빈 폴더를 만듭니다. **simulateddevice** 폴더에서 명령 프롬프트에 다음 명령을 사용하여 새 package.json 패키지 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```
    npm init
    ```

2. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device-amqp** 패키지를 설치합니다.

    ```
    npm install azure-iot-device-amqp --save
    ```

3. 텍스트 편집기를 사용하여 **simulateddevice** 폴더에 새 **SimulatedDevice.js** 파일을 만듭니다.

4. **SimulatedDevice.js** 파일의 첫 부분에 다음 `require` 코드를 추가합니다.

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. **connectionString** 변수를 추가하여 장치 클라이언트를 만드는 데 사용합니다. **{youriothubname}**을 해당 IoT 허브 이름으로, **{yourdeviceid}** 및 **{yourdevicekey}**를 *장치 ID 만들기* 섹션에서 생성한 장치 값으로 대체합니다.

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

7. 콜백을 생성하고 **setInterval** 함수를 사용하여 새 메시지를 1초마다 IoT Hub에 보냅니다.

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

9. **SimulatedDevice.js** 파일을 저장하고 닫습니다.

> [AZURE.NOTE] 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 지수 백오프)을 구현해야 합니다.

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

<!---HONumber=AcomDC_0309_2016-->