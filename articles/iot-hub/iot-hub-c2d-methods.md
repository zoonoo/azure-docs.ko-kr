<properties
 pageTitle="직접 메서드 사용 | Microsoft Azure"
 description="이 자습서는 직접 메서드를 사용하는 방법을 보여줍니다."
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>


# <a name="tutorial-use-direct-methods"></a>자습서: 직접 메서드 사용

## <a name="introduction"></a>소개

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 이전 자습서([IoT Hub 시작] 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기])는 IoT Hub의 기본적인 장치-클라우드 및 클라우드-장치 메시징 기능을 보여줍니다. IoT Hub는 클라우드의 장치에 비지속성 메서드를 호출할 수 있는 기능을 제공합니다. 메서드는 장치와의 request-reply 상호 작용을 나타내며, 사용자가 호출의 상태를 알 수 있도록 즉시(사용자가 지정한 제한 시간이 지난 후에) 성공하거나 실패한다는 점에서 HTTP 호출과 유사합니다. [장치에 직접 메서드 호출][lnk-devguide-methods](영문)은 메서드를 보다 자세히 설명하고 언제 메서드를 사용하고 언제 클라우드-장치 메시지를 사용할지에 대한 지침을 제공합니다.

이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

- Azure Portal을 사용하여 IoT Hub를 만들고 IoT Hub에 장치 ID를 만듭니다.
- 클라우드에서 호출할 수 있는 직접 메서드가 포함된 시뮬레이션된 장치를 만듭니다.
- IoT Hub를 통해 시뮬레이션된 장치에 직접 메서드를 호출하는 콘솔 응용 프로그램을 만듭니다.

> [AZURE.NOTE] 현재 직접 메서드는 MQTT 프로토콜을 사용하여 IoT Hub에 연결하는 장치에서만 액세스할 수 있습니다. 기존 장치 앱이 MQTT를 사용하도록 변환하는 방법에 관한 설명은 [MQTT 지원][lnk-devguide-mqtt] 문서를 참조하세요.

이 자습서를 마치면 두 가지 Node.js 콘솔 응용 프로그램이 만들어집니다.

* **CallMethodOnDevice.js**: 시뮬레이션된 장치에서 메서드를 호출하고 응답을 표시합니다.
* **SimulatedDevice.js**는 앞에서 만든 장치 ID로 IoT Hub에 연결하고 클라우드에서 호출한 메서드에 응답합니다.

> [AZURE.NOTE] [IoT Hub SDK][lnk-hub-sdks] 문서는 장치와 솔루션 백 엔드에서 실행할 두 응용 프로그램을 빌드하는 데 사용할 수 있는 다양한 SDK에 대한 정보를 제공합니다.

이 자습서를 완료하려면 다음이 필요합니다.

+ Node.js 버전 0.10.x 이상

+ 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][lnk-free-trial]을 참조하세요.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>시뮬레이션된 장치 앱 만들기

이 섹션에서는 클라우드에서 호출한 메서드에 응답하는 Node.js 콘솔 앱을 만듭니다.

1. **simulateddevice**라는 빈 폴더를 새로 만듭니다. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```
    npm init
    ```

2. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iot-device** 장치 SDK 패키지 및 **azure-iot-device-mqtt** 패키지를 설치합니다.

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. 텍스트 편집기를 사용하여 **simulateddevice** 폴더에 새 **SimulatedDevice.js** 파일을 만듭니다.

4. **SimulatedDevice.js** 파일 앞에 다음 `require` 문을 추가합니다.

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. **connectionString** 변수를 추가하고 이 변수를 사용하여 장치 클라이언트를 만듭니다. **{장치 연결 문자열}**을 *장치 ID 만들기* 섹션에서 생성한 연결 문자열로 변경합니다.

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. 장치에서 메서드를 실행하도록 다음 함수를 추가합니다.

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. IoT Hub에 대한 연결을 열고 메서드 수신기 초기화를 시작합니다.

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. **SimulatedDevice.js** 파일을 저장한 후 닫습니다.

> [AZURE.NOTE] 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에 MSDN 문서 [일시적인 오류 처리][lnk-transient-faults]에서 제시한 대로 다시 시도 정책(예: 연결 다시 시도)을 구현해야 합니다.

## <a name="call-a-method-on-a-device"></a>장치에 메서드 호출

이 섹션에서는 시뮬레이션된 장치에 메서드를 호출하고 응답을 표시하는 Node.js 콘솔 앱을 만듭니다.

1. **callmethodondevice**라는 빈 폴더를 새로 만듭니다. **callmethodondevice** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```
    npm init
    ```

2. **callmethodondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 패키지를 설치합니다.

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. 텍스트 편집기를 사용하여 **callmethodondevice** 폴더에 **CallMethodOnDevice.js** 파일을 만듭니다.

4. **CallMethodOnDevice.js** 파일의 시작 부분에 다음 `require` 문을 추가합니다.

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. 다음 변수 선언을 추가하고 자리 표시자 값을 IoT Hub에 대한 연결 문자열로 바꿉니다.

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. IoT hub에 대한 연결을 열도록 클라이언트를 만듭니다.

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. 장치 메서드를 호출하고 장치 응답을 콘솔에 출력하도록 다음 함수를 추가합니다.

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. **CallMethodOnDevice.js** 파일을 저장하고 닫습니다.

## <a name="run-the-applications"></a>응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1. **simulateddevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub의 메서드 호출에 대한 대기를 시작합니다.

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. **callmethodondevice** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 IoT Hub 모니터링을 시작합니다.

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. 장치가 메시지를 출력하여 메서드에 반응하는 것과 메서드를 호출한 응용 프로그램이 장치의 응답을 표시하는 것을 볼 수 있습니다.

    ![][9]
    
## <a name="next-steps"></a>다음 단계

이 자습서에서 포털에서 새 IoT Hub를 구성한 다음, 허브의 ID 레지스트리에서 장치 ID를 만들었습니다. 시뮬레이션된 장치 앱이 클라우드에서 호출한 메서드에 반응할 수 있도록 장치 ID를 사용했습니다. 장치에서 메서드를 호출하고 장치의 응답을 표시하는 앱도 만들었습니다. 

계속해서 IoT Hub을 시작하고 다른 IoT 시나리오를 탐색하려면 다음을 참조하세요.

- [IoT Hub 시작]
- [여러 장치에서 작업 예약][lnk-devguide-jobs]

IoT 솔루션을 확장하고 여러 장치에서 메서드 호출을 예약하는 방법을 알아보려면 [작업 예약 및 브로드캐스트][lnk-tutorial-jobs] 자습서를 참조하세요.

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[IoT Hub를 사용하여 클라우드-장치 메시지 보내기]: iot-hub-csharp-csharp-c2d.md
[장치-클라우드 메시지 처리]: iot-hub-csharp-csharp-process-d2c.md
[IoT Hub 시작]: iot-hub-node-node-getstarted.md



<!--HONumber=Oct16_HO2-->


