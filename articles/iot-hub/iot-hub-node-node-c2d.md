---
title: Azure IoT Hub(노드)를 사용한 클라우드-디바이스 메시지 | Microsoft Docs
description: Node.js용 Azure IoT SDK를 사용하여 Azure IoT Hub에서 클라우드-디바이스 메시지를 보내는 방법입니다. 클라우드-디바이스 메시지를 수신하도록 시뮬레이션된 디바이스 앱을 수정하고 클라우드-디바이스 메시지를 보내도록 백 엔드 앱을 수정합니다.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: javascript
ms.topic: conceptual
ms.date: 06/16/2017
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: a1e0e3623692321e5c69e4b9c5a26ff82a1c47a0
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732350"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-nodejs"></a>IoT 허브(Node.js)를 사용하여 클라우드-장치 메시지 보내기

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub는 수백만 개의 디바이스와 솔루션 백 엔드 간에 안정적이고 안전한 양방향 통신이 가능하도록 지원하는 완전히 관리되는 서비스입니다. [장치에서 IoT 허브로 원격 분석 보내기](quickstart-send-telemetry-node.md) 퀵스타트는 IoT 허브를 만들고, IoT 허브에 장치 ID를 프로비전하고, 디바이스-클라우드 메시지를 보내는 시뮬레이션된 장치 앱을 코딩하는 방법을 보여 주었습니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 자습서는 [장치에서 IoT 허브로 원격 분석 보내기를](quickstart-send-telemetry-node.md)기반으로 합니다. 이 항목에서는 다음 방법을 설명합니다.

* 솔루션 백 엔드에서 IoT Hub를 통해 클라우드-디바이스 메시지를 단일 디바이스로 보냅니다.
* 디바이스에서 클라우드-디바이스 메시지를 받습니다.
* 솔루션 백 엔드에서 IoT Hub에서 장치로 전송된 메시지에 대한 배달*승인(피드백)을*요청합니다.

클라우드-디바이스 메시지에 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide-messaging.md)에서 찾아볼 수 있습니다.

이 자습서의 끝 부분에서는 다음 두 개의 Node.js 콘솔 앱을 실행합니다.

* **SimulatedDevice**- IoT 허브에 연결하고 클라우드-장치 메시지를 수신하는 [장치에서 IoT 허브로 원격 분석 보내기에서](quickstart-send-telemetry-node.md)만든 앱의 수정된 버전입니다.

* **SendCloudToDeviceMessage는**IoT Hub를 통해 시뮬레이션된 장치 앱에 클라우드-장치 메시지를 전송한 다음 배달 승인을 받습니다.

> [!NOTE]
> IoT Hub는 Azure IoT 장치 SDK를 통해 많은 장치 플랫폼 및 언어(C, Java, 파이썬 및 자바스크립트 포함)에 대한 SDK 지원을 제공합니다. 이 자습서의 코드 및 일반적으로 Azure IoT Hub에 디바이스를 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot)를 참조하세요.
>

## <a name="prerequisites"></a>사전 요구 사항

* Node.js 버전 10.0.x 이상. Windows 또는 Linux에서 이 자습서를 위해 Node.js를 설치하는 방법에 대해서는 [개발 환경 준비](https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md)에서 설명합니다.

* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/pricing/free-trial)을 만들 수 있습니다.

* 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 문서의 장치 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

## <a name="receive-messages-in-the-simulated-device-app"></a>시뮬레이션된 디바이스 앱에서 메시지 수신

이 섹션에서는 원격 분석 보내기에서 만든 시뮬레이션된 장치 앱을 [장치에서 IoT 허브로](quickstart-send-telemetry-node.md) 수정하여 IoT 허브에서 클라우드-장치 메시지를 수신합니다.

1. 텍스트 편집기에서 **시뮬레이션Device.js** 파일을 엽니다. 이 파일은 [장치에서 IoT hub](quickstart-send-telemetry-node.md) 빠른 시작으로 전송 원격 분석에서 다운로드한 Node.js 샘플 코드의 루트 폴더에서 **iot-hub\Quickstarts\시뮬레이션 장치** 폴더에 있습니다.

2. 장치 클라이언트에 처리기를 등록하여 IoT Hub에서 보낸 메시지를 수신합니다. 다음 코드 `client.on` 조각에서와 같이 장치 클라이언트를 만드는 줄 바로 다음에 호출을 추가합니다.

    ```javascript
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);

    client.on('message', function (msg) {
      console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
      client.complete(msg, function (err) {
        if (err) {
          console.error('complete error: ' + err.toString());
        } else {
          console.log('complete sent');
        }
      });
    });
    ```

    이 예제에서 장치는 메시지를 처리했다는 것을 IoT Hub에 알리는 **전체** 기능을 호출합니다. MQTT 전송을 사용하는 경우 **완료호출이** 필요하지 않으며 생략할 수 있습니다. HTTPS 및 AMQP에 필요합니다.
  
   > [!NOTE]
   > 전송으로 MQTT 또는 AMQP 대신 HTTPS를 사용하는 경우 **DeviceClient** 인스턴스는 IoT Hub의 메시지를 자주(25분 미만 간격으로) 확인합니다. MQTT, AMQP 및 HTTPS 지원과 IoT Hub 제한 간의 차이점에 대한 자세한 내용은 [IoT Hub 개발자 가이드](iot-hub-devguide-messaging.md)를 참조하세요.
   >

## <a name="get-the-iot-hub-connection-string"></a>IoT 허브 연결 문자열 받기

이 문서에서는 [장치에서 원격 분석](quickstart-send-telemetry-node.md)보내기에서 만든 IoT 허브를 통해 클라우드-장치 메시지를 보내는 백 엔드 서비스를 만듭니다. 클라우드-장치 메시지를 보내려면 서비스에 서비스 **연결** 권한이 필요합니다. 기본적으로 모든 IoT 허브는 이 권한을 부여하는 **서비스라는** 공유 액세스 정책으로 만들어집니다.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>클라우드-디바이스 메시지 보내기

이 섹션에서는 클라우드-디바이스 메시지를 시뮬레이트된 디바이스 앱으로 보내는 Node.js 콘솔 앱을 만듭니다. 장치에서 IoT 허브 빠른 시작으로 [원격 분석 보내기에](quickstart-send-telemetry-node.md) 추가한 장치의 장치 ID가 필요합니다. 또한 이전에 복사한 IoT 허브 연결 [문자열이](#get-the-iot-hub-connection-string)필요합니다.

1. **sendcloudtodevicemessage**라는 빈 폴더를 만듭니다. **sendcloudtodevicemessage** 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```shell
    npm init
    ```

2. **sendcloudtodevicemessage** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure-iothub** 패키지를 설치합니다.

    ```shell
    npm install azure-iothub --save
    ```

3. 텍스트 편집기를 사용하여 **sendcloudtodevicemessage** 폴더에 **SendCloudToDeviceMessage.js** 파일을 만듭니다.

4. **SendCloudToDeviceMessage.js** 파일 앞에 다음 `require` 문을 추가합니다.

    ```javascript
    'use strict';

    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. **SendCloudToDeviceMessage.js** 파일에 다음 코드를 추가합니다. "{iot 허브 연결 문자열}" 및 "{장치 ID}" 자리 표시자 값을 이전에 언급한 IoT 허브 연결 문자열 및 장치 ID로 바꿉꿉입니다.

    ```javascript
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. 다음 함수를 추가하여 작업 결과를 콘솔에 출력합니다.

    ```javascript
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. 다음 함수를 추가하여 배달 피드백 메시지를 콘솔에 출력합니다.

    ```javascript
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. 다음 코드를 추가하여 디바이스에 메시지를 보내고 디바이스가 클라우드-디바이스 메시지를 승인할 때 피드백 메시지를 처리합니다.

    ```javascript
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

9. **SendCloudToDeviceMessage.js** 파일을 저장한 후 닫습니다.

## <a name="run-the-applications"></a>애플리케이션 실행

이제 애플리케이션을 실행할 준비가 되었습니다.

1. **시뮬레이션된 장치** 폴더의 명령 프롬프트에서 다음 명령을 실행하여 원격 분석을 IoT Hub로 보내고 클라우드-장치 메시지를 수신합니다.

    ```shell
    node SimulatedDevice.js
    ```

    ![시뮬레이션된 디바이스 앱 실행](./media/iot-hub-node-node-c2d/receivec2d.png)

2. 명령 프롬프트의 **sendcloudtodevicemessage** 폴더에서 다음 명령을 실행하여 클라우드-장치 메시지를 보내고 승인 피드백을 대기합니다.

    ```shell
    node SendCloudToDeviceMessage.js
    ```

    ![앱을 실행하여 클라우드-디바이스 명령 보내기](./media/iot-hub-node-node-c2d/sendc2d.png)

   > [!NOTE]
   > 간단히 하기 위해 이 자습서에서는 다시 시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 문서 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.
   >

## <a name="next-steps"></a>다음 단계

이 자습서에서 클라우드-디바이스 메시지를 보내고 받는 방법을 알아보았습니다.

IoT Hub를 사용하는 완전한 엔드투엔드 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기](https://azure.microsoft.com/documentation/suites/iot-suite/)를 참조하세요.

IoT Hub를 사용하여 솔루션 개발에 대해 자세히 알아보려면 [IoT Hub 개발자 가이드를](iot-hub-devguide.md)참조하십시오.
