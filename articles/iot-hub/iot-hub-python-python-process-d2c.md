---
title: Azure IoT Hub(Python)를 사용한 메시지 라우팅 | Microsoft Docs
description: 다른 백 엔드 서비스에 메시지를 발송하기 위해 경로 규칙 및 사용자 지정 끝점을 사용하여 Azure IoT Hub 장치-클라우드 메시지를 처리하는 방법을 설명합니다.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: python
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 98216328e970af5f8acf04c7e7c3ca855f7065f1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/07/2018
---
# <a name="routing-messages-with-iot-hub-python"></a>IoT Hub(Python)를 사용하여 메시지 라우팅

[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

이 자습서는 [IoT Hub 시작] 자습서를 기반으로 합니다.  이 자습서의 내용은 다음과 같습니다.

* I라우팅 규칙을 사용하여 손쉬운 구성 기반 방식으로 장치-클라우드 메시지를 발송하는 방법을 보여 줍니다.
* 추가 처리를 위해 솔루션 백 엔드의 즉각적인 작업을 요구하는 대화형 메시지를 격리하는 방법을 보여 줍니다.  예를 들어 장치는 CRM 시스템으로의 티켓 삽입을 트리거하는 경보 메시지를 보낼 수 있습니다.  반면 온도 원격 분석과 같은 데이터 요소 메시지는 분석 엔진으로 전달됩니다.

이 자습서의 끝 부분에서 다음의 세 개의 Python 콘솔 앱을 실행합니다.

* [IoT Hub 시작] 자습서에서 만든 수정된 버전의 앱인 **SimulatedDevice.py**는 매초 데이터 요소 장치-클라우드 메시지를 보내고 임의 간격마다 대화형 장치-클라우드 메시지를 보냅니다. 이 앱에서는 IoT Hub와 통신하는 데 MQTT 프로토콜을 사용합니다.
* **ReadCriticalQueue.py**는 IoT Hub에 연결된 Service Bus 큐에서 중요한 메시지를 큐에서 제거합니다.

> [!NOTE]
> IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 JavaScript 포함)에 SDK를 지원합니다. 물리적 장치를 사용하여 이 자습서의 장치를 바꾸는 방법 및 장치를 IoT Hub에 연결하는 방법에 대한 지침은 [Azure IoT 개발자 센터]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

* [IoT Hub 시작] 자습서의 전체 작업 버전
* [Python 2.x 또는 3.x][lnk-python-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. 설치하는 동안 메시지가 나타나면 플랫폼별 환경 변수에 Python을 추가해야 합니다. Python 2.x를 사용하는 경우 [Python 패키지 관리 시스템인 *pip*을 설치 또는 업그레이드][lnk-install-pip]해야 할 수도 있습니다.
* Windows OS를 사용하는 경우 Python에서 네이티브 DLL을 사용하기 위해 필요한 [Visual C++ 재배포 가능 패키지][lnk-visual-c-redist].
* [Node.js 4.0 이상][lnk-node-download]. 설치 프로그램의 요구 사항에 따라 32비트 또는 64비트 설치를 사용해야 합니다. [IoT Hub 탐색기 도구][lnk-iot-hub-explorer]를 설치하는 데 필요합니다.
* 활성 Azure 계정. 계정이 없는 경우 몇 분 안에 [무료 계정][lnk-free-trial]을 만들 수 있습니다.

또한 [Azure Storage] 및 [Azure Service Bus]에 대해서도 읽어보는 것이 좋습니다.


## <a name="send-interactive-messages-from-a-device-app"></a>장치 앱에서 대화형 메시지 보내기
이 섹션에서는 [IoT Hub 시작] 자습서에서 만든 장치 앱을 수정하여 즉시 처리해야 하는 메시지를 가끔씩 보낼 수 있습니다.

1. 텍스트 편집기를 사용하여 **SimulatedDevice.py** 파일을 엽니다. 이 파일에는 [IoT Hub 시작] 자습서에서 만든 **SimulatedDevice** 앱이 포함되어 있습니다.

2. **iothub_client_telemetry_sample_run** 함수를 다음 코드로 바꿉니다.

    ```python
    def iothub_client_telemetry_sample_run():

    try:
        client = iothub_client_init()
        print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
        message_counter = 0

        while True:
            random_seed = random.random()
            msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random_seed * 4 + 2))
            # messages can be encoded as string or bytearray
            if (message_counter & 1) == 1:
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
            else:
                message = IoTHubMessage(msg_txt_formatted)
            # optional: assign ids
            message.message_id = "message_%d" % message_counter
            message.correlation_id = "correlation_%d" % message_counter
            # optional: assign properties
            prop_map = message.properties()
            if random_seed > .5:
                if random_seed > .8:
                    prop_map.add("level", 'critical')
                else:
                    prop_map.add("level", 'storage')

            client.send_event_async(message, send_confirmation_callback, message_counter)
            print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

            status = client.get_send_status()
            print ( "Send status: %s" % status )
            time.sleep(30)

            status = client.get_send_status()
            print ( "Send status: %s" % status )

            message_counter += 1

    except IoTHubError as iothub_error:
        print ( "Unexpected error %s from IoTHub" % iothub_error )
        return
    except KeyboardInterrupt:
        print ( "IoTHubClient sample stopped" )
    ```
   
    이 메서드는 장치에서 보낸 메시지에 `"level": "critical"` 및 `"level": "storage"` 속성을 임의로 추가합니다. 그러면 응용 프로그램 백 엔드에 의한 즉각적인 작업을 요구하거나 영구 저장해야 하는 메시지를 시뮬레이트합니다. 응용 프로그램은 메시지 본문에 따라 메시지 라우팅을 지원합니다.
   
   > [!NOTE]
   > 메시지 속성을 사용하면 여기서 보여 주는 실행 부하 과다 경로(hot path) 예제 외에도 실행 부하 과소 경로(cold path) 처리를 포함하여 다양한 시나리오의 메시지를 라우팅할 수 있습니다.

1. **SimulatedDevice.py** 파일을 저장하고 닫습니다.

    > [!NOTE]
    > 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.


## <a name="add-queues-to-your-iot-hub-and-route-messages-to-them"></a>IoT Hub에 큐 추가 및 이에 메시지 라우팅

이 섹션에서는 Service Bus 큐 및 Storage 계정을 모두 만들고, 이를 IoT Hub에 연결하고, 메시지에 속성이 존재하는지 여부 및 Storage 계정에 대한 모든 메시지에 따라 큐에 메시지를 보내도록 IoT Hub를 구성합니다. Service Bus 큐에서 메시지를 처리하는 방법에 대한 자세한 내용은 [큐 시작][lnk-sb-queues-node] 및 저장소를 관리하는 방법 및 [Azure Storage 시작][Azure Storage]를 참조하세요.

1. [큐 시작][lnk-sb-queues-node]에서 설명한 대로 Service Bus 큐를 만듭니다. 네임스페이스 및 큐 이름을 적어둡니다.

    > [!NOTE]
    > IoT Hub으로 사용되는 Service Bus 큐 및 토픽에는 **세션** 또는 **중복 검색**이 사용하도록 설정되어 있어서는 안 됩니다. 두 옵션 중 하나가 사용하도록 설정되어 있으면 Azure Portal에서 끝점이 **연결할 수 없음**으로 표시됩니다.

1. Azure Portal에서 IoT Hub를 열고 **끝점**을 클릭합니다.

    ![IoT Hub의 끝점][30]

1. **끝점** 블레이드 위쪽에서 **추가**를 클릭하여 IoT Hub에 큐를 추가합니다. 끝점 이름을 **CriticalQueue**로 지정하고 드롭다운을 사용하여 **Service Bus 큐**, 큐가 있는 Service Bus 네임스페이스 및 큐 이름을 선택합니다. 완료되면 아래쪽의 **확인**을 클릭합니다.  

    ![끝점 추가][31]

1. 이제 IoT Hub에서 **경로**를 클릭합니다. 블레이드 위쪽에서 **추가**를 클릭하여 방금 추가한 큐로 메시지를 라우팅하는 라우팅 규칙을 만듭니다. 

    ![경로 추가][34]

    이름을 입력하고 데이터 원본으로 **장치 메시지**를 선택합니다. **CriticalQueue**를 라우팅 규칙 끝점인 사용자 지정 끝점으로 선택하고 `level="critical"`을 쿼리 문자열로 입력합니다. 아래쪽에서 **저장**을 클릭합니다.

    ![세부 정보 라우팅][32]

    대체(fallback) 경로가 **ON**으로 설정되어 있는지 확인합니다. 이 설정은 IoT Hub의 기본 구성입니다.

    ![대체(fallback) 경로][33]

## <a name="optional-read-from-the-queue-endpoint"></a>(선택 사항) 큐 끝점에서 읽기

이 섹션에서는 IoT Service Bus에서 중요한 메시지를 읽는 Python 콘솔 앱을 만듭니다. [큐 시작][lnk-sb-queues-node]에서 자세한 내용을 참조하세요. 

1. 다음과 같이 새 명령 프롬프트를 열고 Python용 Azure IoT Hub 장치 SDK를 설치합니다. 설치한 후 명령 프롬프트를 닫습니다.

    ```cmd/sh
    pip install azure-servicebus
    ```

    > [!NOTE]
    > **azure-servicebus** 패키지를 설치하는 문제 및 자세한 설치 옵션은 [Python azure-servicebus 패키지][lnk-python-service-bus]를 참조하세요.

1. **ReadCriticalQueue.py**라는 파일을 만듭니다. 원하는 Python 편집기/IDE(예: IDLE)에서 이 파일을 엽니다.

1. 장치 SDK에서 필요한 모듈을 가져오는 다음 코드를 추가합니다.

    ```python
    from azure.servicebus import ServiceBusService, Message, Queue
    ```

1. 다음 코드를 추가하고 자리 표시자를 Service Bus에 대한 연결 데이터로 바꿉니다.

    ```python
    SERVICE_BUS_NAME = {serviceBusName}
    SHARED_ACCESS_POLICY_NAME = {sharedAccessPolicyName}
    SHARED_ACCESS_POLICY_KEY_VALUE = {sharedAccessPolicyKeyValue}
    QUEUE_NAME = {queueName}    
    ```

5. 다음 코드를 추가하여 Service Bus에 연결하고 읽습니다. 

    ```python
    def setup_client():
        bus_service = ServiceBusService(
        service_namespace=SERVICE_BUS_NAME,
        shared_access_key_name=SHARED_ACCESS_POLICY_NAME,
        shared_access_key_value=SHARED_ACCESS_POLICY_KEY_VALUE)

        while True:
            msg = bus_service.receive_queue_message(QUEUE_NAME, peek_lock=False)
            print(msg.body)
    ```

1. 마지막으로 주 함수를 추가합니다. 

    ```python
    if __name__ == '__main__':
        setup_client()
    ```

1. **ReadCriticalQueue.py** 파일을 저장하고 닫습니다. 이제 응용 프로그램을 실행할 준비가 되었습니다.


## <a name="run-the-applications"></a>응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1. 명령 프롬프트를 열고 IoT Hub 탐색기를 설치합니다. 

    ```cmd/sh
    npm install -g iothub-explorer
    ```

1. 명령 프롬프트에서 다음 명령을 실행하여 장치에서 오는 장치-클라우드 메시지의 모니터링을 시작합니다. `--login` 뒤의 자리 표시자에 IoT Hub의 연결 문자열을 사용합니다.

    ```cmd/sh
    iothub-explorer monitor-events [deviceId] --login "[IoTHub connection string]"
    ```

1. 새 명령 프롬프트를 열고 **SimulatedDevice.py** 파일이 들어 있는 디렉터리로 이동합니다.

1. 주기적으로 IoT Hub에 원격 분석 데이터를 보내는 **SimulatedDevice.py** 파일을 실행합니다. 
   
    ```cmd/sh
    python SimulatedDevice.py
    ```

1. **ReadCriticalQueue** 응용 프로그램을 실행하려면 명령 프롬프트 또는 셸에서 **ReadCriticalQueue.py** 파일로 이동하고 다음 명령을 실행합니다.

   ```cmd/sh
   python ReadCriticalQueue.py
   ```

1. 이전 섹션의 IoT Hub 탐색기를 실행하는 명령 프롬프트에서 장치 메시지를 관찰합니다. **ReadCriticalQueue** 응용 프로그램에서 `critical` 메시지를 관찰합니다.

    ![Python 장치-클라우드 메시지][2]


## <a name="optional-add-storage-container-to-your-iot-hub-and-route-messages-to-it"></a>(선택 사항) 저장소 컨테이너를 IoT Hub에 추가 및 메시지 라우팅

이 섹션에서는 저장소 계정을 만들고, IoT Hub에 연결하고, 메시지에 속성이 존재하는지 여부에 따라 계정에 메시지를 보내도록 IoT Hub를 구성합니다. 저장소를 관리하는 방법에 대한 자세한 내용은 [Azure Storage 시작][Azure Storage]를 참조하세요.

 > [!NOTE]
   > _F1 체험_ 계층 하에서 생성된 IoT Hub 계정은 하나의 **끝점**으로 제한됩니다. 하나의 **끝점**으로 제한되지 않는 경우 **CriticalQueue** 외에 **StorageContainer**를 설정하고 두 끝점을 동시에 실행할 수 있습니다.

1. [Azure Storage 설명서][lnk-storage]에 설명된 대로 저장소 계정을 만듭니다. 계정 이름을 기록해 둡니다.

2. Azure Portal에서 IoT Hub를 열고 **끝점**을 클릭합니다.

3. **끝점** 블레이드에서 **CriticalQueue** 끝점을 선택하고 **삭제**를 클릭합니다. **예**를 클릭한 후 **추가**를 클릭합니다. 끝점 이름을 **StorageContainer**로 지정하고 드롭다운 메뉴를 사용하여 **Azure Storage 컨테이너**를 선택한 후 **저장소 계정** 및 **저장소 컨테이너**를 만듭니다.  이름을 기록해 둡니다.  완료되면 아래쪽의 **확인**을 클릭합니다. 

 > [!NOTE]
   > _F1 체험_ 계층 하에서 생성된 IoT Hub 계정은 하나의 **끝점**으로 제한됩니다. 하나의 **끝점**으로 제한되지 않는 경우 **CriticalQueue**를 삭제할 필요가 없습니다.

4. IoT Hub에서 **경로**를 클릭합니다. 블레이드 위쪽에서 **추가**를 클릭하여 방금 추가한 큐로 메시지를 라우팅하는 라우팅 규칙을 만듭니다. 데이터 원본으로 **장치 메시지**를 선택합니다. 조건으로 `level="storage"`를 입력하고 사용자 지정 끝점과 같은 **StorageContainer**를 라우팅 규칙 끝점으로 선택합니다. 아래쪽에서 **저장**을 클릭합니다.  

    대체(fallback) 경로가 **ON**으로 설정되어 있는지 확인합니다. 이 설정은 IoT Hub의 기본 구성입니다.

1. 이전 응용 프로그램 **SimulatedDevice.py**가 여전히 실행되고 있는지 확인합니다. 

1. Azure Portal에서 저장소 계정으로 이동한 후 **Blob Service** 아래에서 **Blob 찾아보기...** 를 클릭합니다.  컨테이너를 선택하고 JSON 파일로 이동한 후 클릭하고 **다운로드**를 클릭하여 데이터를 확인합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 IoT Hub의 메시지 라우팅 기능을 사용하여 장치-클라우드 메시지를 안정적으로 전달하는 방법을 살펴보았습니다.

IoT Hub를 사용하는 완전한 종단 간 솔루션의 예를 보려면 [Azure IoT 원격 모니터링 솔루션 가속기][lnk-suite]를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드]를 참조하세요.

IoT Hub의 메시지 라우팅에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기][lnk-devguide-messaging]를 참조하세요.

<!-- Images. -->
[2]: ./media/iot-hub-python-python-process-d2c/output.png

[30]: ./media/iot-hub-python-python-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-python-python-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-python-python-process-d2c/route-creation.png
[33]: ./media/iot-hub-python-python-process-d2c/fallback-route.png
[34]: ./media/iot-hub-python-python-process-d2c/click-routes.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-sb-queues-node]: ../service-bus-messaging/service-bus-python-how-to-use-queues.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[IoT Hub 시작]: iot-hub-python-getstarted.md
[Azure IoT 개발자 센터]: https://azure.microsoft.com/develop/iot

[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-free-trial]: https://azure.microsoft.com/free/
[lnk-storage]: https://docs.microsoft.com/azure/storage/
[lnk-python-service-bus]: https://pypi.python.org/pypi/azure-servicebus
