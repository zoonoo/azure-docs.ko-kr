---
title: Azure IoT Hub(노드)를 사용한 메시지 라우팅 | Microsoft Docs
description: 다른 백 엔드 서비스에 메시지를 발송하기 위해 경로 규칙 및 사용자 지정 끝점을 사용하여 Azure IoT Hub 장치-클라우드 메시지를 처리하는 방법을 설명합니다.
services: iot-hub
documentationcenter: node
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: v-masebo;dobett
ms.openlocfilehash: 02edb06d2d871cffac717358e33a6720c444a9b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/05/2018
---
# <a name="routing-messages-with-iot-hub-node"></a>IoT Hub(노드)를 사용한 메시지 라우팅

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

이 자습서는 [SimulatedDevice] 자습서를 기반으로 합니다.  이 자습서의 내용은 다음과 같습니다.

* I라우팅 규칙을 사용하여 손쉬운 구성 기반 방식으로 장치-클라우드 메시지를 발송하는 방법을 보여 줍니다.
* 추가 처리를 위해 솔루션 백 엔드의 즉각적인 작업을 요구하는 대화형 메시지를 격리하는 방법을 보여 줍니다.  예를 들어 장치는 CRM 시스템으로의 티켓 삽입을 트리거하는 경보 메시지를 보낼 수 있습니다.  반면 온도 원격 분석과 같은 데이터 요소 메시지는 분석 엔진으로 전달됩니다.

이 자습서의 끝 부분에서 다음의 세 가지 Node.js 콘솔 앱을 생성합니다.

* [SimulatedDevice] 자습서에서 만든 수정된 버전의 앱인 **SimulatedDevice**는 매초 데이터 요소 장치-클라우드 메시지를 보내고 임의 간격마다 대화형 장치-클라우드 메시지를 보냅니다. 이 앱에서는 IoT Hub와 통신하는 데 MQTT 프로토콜을 사용합니다.
* **ReadDeviceToCloudMessages**는 장치 앱에서 보낸 원격 분석을 표시합니다.
* **ReadCriticalQueue.js**는 IoT Hub에 연결된 Service Bus 큐에서 중요한 메시지를 큐에서 제거합니다.

> [!NOTE]
> IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 JavaScript 포함)에 SDK를 지원합니다. 물리적 장치를 사용하여 이 자습서의 장치를 바꾸는 방법 및 장치를 IoT Hub에 연결하는 방법에 대한 지침은 [Azure IoT 개발자 센터]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* [SimulatedDevice] 자습서의 전체 작업 버전
* Node.js 버전 4.0.x 이상
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

또한 [Azure Storage] 및 [Azure Service Bus]에 대해서도 읽어보는 것이 좋습니다.

## <a name="send-interactive-messages-from-a-device-app"></a>장치 앱에서 대화형 메시지 보내기
이 섹션에서는 [SimulatedDevice] 자습서에서 만든 장치 앱을 수정하여 즉시 처리해야 하는 메시지를 가끔씩 보낼 수 있습니다.

1. 텍스트 편집기를 사용하여 **simulateddevice\SimulatedDevice.js** 파일을 엽니다. 이 파일에는 **IoT Hub 시작** 자습서에서 만든 [SimulatedDevice] 앱이 포함되어 있습니다.

2. **connectCallback** 함수를 다음 코드로 바꿉니다.

    ```nodejs
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
    
        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
        var data, message;
        if (Math.random() > 0.7) {
            if (Math.random() > 0.5) {
                data = "This is a critical message.";
                message = new Message(data);
                message.properties.add("level", "critical");
            } else {
                data = "This is a storage message.";
                message = new Message(data);
                message.properties.add("level", "storage");
            }
            console.log("Sending message: " + message.getData());
        }
        else {
                var temperature = 20 + (Math.random() * 15);
                var humidity = 60 + (Math.random() * 20);            
                data = JSON.stringify({ deviceId: 'myFirstNodeDevice', temperature: temperature, humidity: humidity });
                message = new Message(data);
                message.properties.add('temperatureAlert', (temperature > 30) ? 'true' : 'false');
                console.log("Sending message: " + message.getData());
            }
        client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```
   
    이 메서드는 장치에서 보낸 메시지에 `"level": "critical"` 및 `"level": "storage"` 속성을 임의로 추가합니다. 그러면 응용 프로그램 백 엔드에 의한 즉각적인 작업을 요구하거나 영구 저장해야 하는 메시지를 시뮬레이트합니다. 응용 프로그램은 메시지 본문에 따라 메시지 라우팅을 지원합니다.
   
   > [!NOTE]
   > 메시지 속성을 사용하면 여기서 보여 주는 실행 부하 과다 경로(hot path) 예제 외에도 실행 부하 과소 경로(cold path) 처리를 포함하여 다양한 시나리오의 메시지를 라우팅할 수 있습니다.

2. **simulateddevice\SimulatedDevice.js** 파일을 저장한 후 닫습니다.

    > [!NOTE]
    > MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현하는 것이 좋습니다.

## <a name="add-service-bus-queue-to-your-iot-hub-and-route-messages-to-it"></a>IoT Hub에 Service Bus 큐 추가 및 메시지 라우팅

이 섹션에서는 Service Bus 큐를 만들고, IoT Hub에 연결하고, 메시지에 속성이 존재하는지 여부에 따라 큐에 메시지를 보내도록 IoT Hub를 구성합니다. Service Bus 큐에서 메시지를 처리하는 방법에 대한 자세한 내용은 [큐 시작][lnk-sb-queues-node]을 참조하세요.

1. [큐 시작][lnk-sb-queues-node]에서 설명한 대로 Service Bus 큐를 만듭니다. 네임스페이스 및 큐 이름을 적어둡니다.

    > [!NOTE]
    > IoT Hub으로 사용되는 Service Bus 큐 및 토픽에는 **세션** 또는 **중복 검색**이 사용하도록 설정되어 있어서는 안 됩니다. 두 옵션 중 하나가 사용하도록 설정되어 있으면 Azure Portal에서 끝점이 **연결할 수 없음**으로 표시됩니다.

2. Azure Portal에서 IoT Hub를 열고 **끝점**을 클릭합니다.

    ![IoT Hub의 끝점][30]

3. **끝점** 블레이드 위쪽에서 **추가**를 클릭하여 IoT Hub에 큐를 추가합니다. 끝점 이름을 **CriticalQueue**로 지정하고 드롭다운을 사용하여 **Service Bus 큐**, 큐가 있는 Service Bus 네임스페이스 및 큐 이름을 선택합니다. 완료되면 아래쪽의 **확인**을 클릭합니다.  

    ![끝점 추가][31]

4. 이제 IoT Hub에서 **경로**를 클릭합니다. 블레이드 위쪽에서 **추가**를 클릭하여 방금 추가한 큐로 메시지를 라우팅하는 라우팅 규칙을 만듭니다. 데이터 원본으로 **장치 메시지**를 선택합니다. 조건으로 `level="critical"`을 입력하고 사용자 지정 끝점과 같은 **CriticalQueue**를 라우팅 규칙 끝점으로 선택합니다. 아래쪽에서 **저장**을 클릭합니다.  

    ![경로 추가][32]

    대체(fallback) 경로가 **ON**으로 설정되어 있는지 확인합니다. 이 설정은 IoT Hub의 기본 구성입니다.

    ![대체(fallback) 경로][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(선택 사항) 큐 끝점에서 읽기

이 섹션에서는 IoT Service Bus에서 중요한 메시지를 읽는 Node.js 콘솔 앱을 만듭니다. [큐 시작][lnk-sb-queues-node]에서 자세한 내용을 참조하세요. 

1. `readcriticalqueue`라는 빈 폴더를 만듭니다. `readcriticalqueue` 폴더의 명령 프롬프트에서 다음 명령을 사용하여 package.json 파일을 만듭니다. 모든 기본값을 수락합니다.

    ```cmd/sh
    npm init
    ```

2. `readcriticalqueue` 폴더의 명령 프롬프트에서 다음 명령을 실행하여 **azure** 패키지를 설치합니다.

    ```cmd/sh
    npm install azure --save
    ```

3. 텍스트 편집기를 사용하여 `readcriticalqueue` 폴더에 **ReadCriticalQueue.js**를 만듭니다.

4. **ReadCriticalQueue.js** 파일 앞에 다음 `require` 문을 추가합니다.

    ```nodejs
    'use strict';

    var azure = require('azure');
    ```

5. 다음 변수 선언을 추가하고 자리 표시자 값을 IoT Service Bus 연결 문자열 및 큐 이름으로 바꿉니다.

    ```nodejs
    var connectionString = '{iotservicebus connection string}';
    var queueName = '{iotservice bus queue name}';
    ```

6. 콘솔에 출력하는 다음 두 함수를 추가합니다.

    ```nodejs
    var serviceBusService = azure.createServiceBusService(connectionString);

    setInterval(function() {serviceBusService.receiveQueueMessage(queueName, function(error, receivedMessage) {
        if(!error){
            // Message received and deleted
            console.log(receivedMessage);
        } else { console.log(error); }
        });
    }, 3000);
    ```

7. **ReadCriticalQueue.js** 파일을 저장한 후 닫습니다.

## <a name="run-the-applications"></a>응용 프로그램 실행

이제 세 개의 응용 프로그램을 실행할 준비가 되었습니다.

1. **ReadDeviceToCloudMessages.js** 응용 프로그램을 실행하려면 명령 프롬프트 또는 셸에서 readdevicetocloudmessages 폴더로 이동한 후 다음 명령을 실행합니다.

   ```cmd/sh
   node ReadDeviceToCloudMessages.js
   ```

   ![read-d2c-messages 실행][readd2c]

2. **ReadCriticalQueue.js** 응용 프로그램을 실행하려면 명령 프롬프트 또는 셸에서 readcriticalqueue 폴더로 이동한 후 다음 명령을 실행합니다.

   ```cmd/sh
   node ReadCriticalQueue.js
   ```
   
   ![read-critical-messages 실행][readqueue]

3. **SimulatedDevice.js** 앱을 실행하려면 명령 프롬프트 또는 셸에서 simulateddevice 폴더로 이동한 후 다음 명령을 실행합니다.

   ```cmd/sh
   node SimulatedDevice.js
   ```
   
   ![simulated-device 실행][simulateddevice]

## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(선택 사항) 저장소 컨테이너를 IoT Hub에 추가 및 메시지 라우팅

이 섹션에서는 저장소 계정을 만들고, IoT Hub에 연결하고, 메시지에 속성이 존재하는지 여부에 따라 계정에 메시지를 보내도록 IoT Hub를 구성합니다. 저장소를 관리하는 방법에 대한 자세한 내용은 [Azure Storage 시작][Azure Storage]를 참조하세요.

 > [!NOTE]
   > 하나의 **끝점**으로 제한되지 않는 경우 **CriticalQueue** 외에 **StorageContainer**를 설정하고 두 끝점을 동시에 실행할 수 있습니다.

1. [Azure Storage 설명서][lnk-storage]에 설명된 대로 저장소 계정을 만듭니다. 계정 이름을 기록해 둡니다.

2. Azure Portal에서 IoT Hub를 열고 **끝점**을 클릭합니다.

3. **끝점** 블레이드에서 **CriticalQueue** 끝점을 선택하고 **삭제**를 클릭합니다. **예**를 클릭한 후 **추가**를 클릭합니다. 끝점 이름을 **StorageContainer**로 지정하고 드롭다운 메뉴를 사용하여 **Azure Storage 컨테이너**를 선택한 후 **저장소 계정** 및 **저장소 컨테이너**를 만듭니다.  이름을 기록해 둡니다.  완료되면 아래쪽의 **확인**을 클릭합니다. 

 > [!NOTE]
   > 하나의 **끝점**으로 제한되지 않는 경우 **CriticalQueue**를 삭제할 필요가 없습니다.

4. IoT Hub에서 **경로**를 클릭합니다. 블레이드 위쪽에서 **추가**를 클릭하여 방금 추가한 큐로 메시지를 라우팅하는 라우팅 규칙을 만듭니다. 데이터 원본으로 **장치 메시지**를 선택합니다. 조건으로 `level="storage"`를 입력하고 사용자 지정 끝점과 같은 **StorageContainer**를 라우팅 규칙 끝점으로 선택합니다. 아래쪽에서 **저장**을 클릭합니다.  

    대체(fallback) 경로가 **ON**으로 설정되어 있는지 확인합니다. 이 설정은 IoT Hub의 기본 구성입니다.

1. 이전 응용 프로그램 **SimulatedDevice.js**가 여전히 실행되고 있는지 확인합니다. 

1. Azure Portal에서 저장소 계정으로 이동한 후 **Blob 서비스** 아래에서 **blob 찾아보기...**를 클릭합니다.  컨테이너를 선택하고 JSON 파일로 이동한 후 클릭하고 **다운로드**를 클릭하여 데이터를 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Hub의 메시지 라우팅 기능을 사용하여 장치-클라우드 메시지를 안정적으로 전달하는 방법을 살펴보았습니다.

[IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법][lnk-c2d]에서는 솔루션 백 엔드에서 장치로 메시지를 보내는 방법을 보여줍니다.

IoT Hub를 사용하는 전체 종단 간 솔루션의 예를 보려면 [Azure IoT Suite][lnk-suite]를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드]를 참조하세요.

IoT Hub의 메시지 라우팅에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기][lnk-devguide-messaging]를 참조하세요.

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-node-node-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-node-node-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-node-node-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-node-node-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-node-node-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-node-node-process-d2c/route-creation.png
[33]: ./media/iot-hub-node-node-process-d2c/fallback-route.png

<!-- Links -->

[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-nodejs-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[SimulatedDevice]: iot-hub-node-node-getstarted.md
[Azure IoT 개발자 센터]: https://azure.microsoft.com/develop/iot
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-c2d]: iot-hub-node-node-c2d.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/