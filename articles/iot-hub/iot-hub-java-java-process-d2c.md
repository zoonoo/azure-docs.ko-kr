---
title: "IoT Hub 장치-클라우드 메시지 처리(Java) | Microsoft Docs"
description: "다른 백 엔드 서비스에 메시지를 발송하기 위해 경로 규칙 및 사용자 지정 끝점을 사용하여 IoT Hub 장치-클라우드 메시지를 처리하는 방법을 설명합니다."
services: iot-hub
documentationcenter: java
author: dominicbetts
manager: timlt
editor: 
ms.assetid: bd9af5f9-a740-4780-a2a6-8c0e2752cf48
ms.service: iot-hub
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 1915044f252984f6d68498837e13c817242542cf
ms.openlocfilehash: 616bca96eaff12fa1929605f3480098bd8b867c2
ms.contentlocale: ko-kr
ms.lasthandoff: 01/31/2017


---
# <a name="process-iot-hub-device-to-cloud-messages-java"></a>IoT Hub 장치-클라우드 메시지 처리(Java)
[!INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>소개
Azure IoT Hub는 수백만의 장치와 솔루션 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 다른 자습서([simulated-device] 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기][lnk-c2d])에서는 IoT Hub의 기본 장치-클라우드 및 클라우드-장치 메시징 기능을 사용하는 방법을 보여 줍니다.

이 자습서에서는 [simulated-device] 자습서에 나와 있는 코드를 기반으로 하며, 메시지 라우팅을 사용하여 확장성 있는 방식으로 장치-클라우드 메시지를 처리하는 방법을 보여 줍니다. 이 자습서는 솔루션 백 엔드의 즉각적인 작업을 요구하는 메시지를 처리하는 방법을 보여 줍니다. 예를 들어 장치는 CRM 시스템으로의 티켓 삽입을 트리거하는 경보 메시지를 보낼 수 있습니다. 이와 반대로 데이터 요소 메시지는 단순히 분석 엔진으로 전달됩니다. 예를 들어 나중에 분석을 위해 저장해야 하는 장치의 온도 원격 분석이 데이터 요소 메시지에 해당합니다.

이 자습서의 끝 부분에서는 다음의 세 가지 Java 콘솔 앱을 실행합니다.

* **simulated-device**, [simulated-device] 자습서에서 만든 수정된 버전의 앱이며, 매초 데이터 요소 장치-클라우드 메시지를 보내고 10초마다 대화형 장치-클라우드 메시지를 보냅니다. 이 앱에서는 IoT Hub와 통신하는 데 AMQP 프로토콜을 사용합니다.
* **read-d2c-messages**는 시뮬레이션된 장치 앱에서 보낸 원격 분석을 표시합니다.
* **read-critical-queue**는 IoT Hub에 연결된 Service Bus 큐에서 중요한 메시지를 큐에서 제거합니다.

> [!NOTE]
> IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 JavaScript 포함)에 SDK를 지원합니다. 물리적 장치를 사용하여 이 자습서의 시뮬레이션된 장치를 바꾸는 방법 및 장치를 IoT Hub에 연결하는 방법에 대한 지침은 [Azure IoT 개발자 센터]를 참조하세요.
> 
> 

이 자습서를 완료하려면 다음이 필요합니다.

* [simulated-device] 자습서의 전체 작업 버전
* Java SE 8. <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Java를 설치하는 방법을 설명합니다.
* Maven 3.  <br/> [개발 환경 준비][lnk-dev-setup]는 Windows 또는 Linux에서 이 자습서에 대한 Maven을 설치하는 방법을 설명합니다.
* 활성 Azure 계정. <br/>계정이 없는 경우 몇 분 안에 [무료 계정](https://azure.microsoft.com/free/) 을 만들 수 있습니다.

[Azure Storage] 및 [Azure Service Bus]에 대한 기본 지식이 있어야 합니다.

## <a name="send-interactive-messages-from-a-simulated-device-app"></a>시뮬레이션된 장치 앱에서 대화형 메시지 보내기
이 섹션에서는 [simulated-device] 자습서에서 만든 시뮬레이션된 장치 앱을 수정하여 즉시 처리해야 하는 메시지를 가끔씩 보낼 수 있습니다.

1. 텍스트 편집기를 사용하여 simulated-device\src\main\java\com\mycompany\app\App.java 파일을 엽니다. 이 파일에는 **IoT Hub 시작** 자습서에서 만든 [simulated-device] 앱이 포함되어 있습니다.
2. **MessageSender** 클래스를 다음 코드로 바꿉니다.
   
    ```
    private static class MessageSender implements Runnable {

        public void run()  {
            try {
                double minTemperature = 20;
                double minHumidity = 60;
                Random rand = new Random();

                while (true) {
                    String msgStr;
                    Message msg;
                    if (new Random().nextDouble() > 0.7) {
                        msgStr = "This is a critical message.";
                        msg = new Message(msgStr);
                        msg.setProperty("level", "critical");
                    } else {
                        double currentTemperature = minTemperature + rand.nextDouble() * 15;
                        double currentHumidity = minHumidity + rand.nextDouble() * 20; 
                        TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
                        telemetryDataPoint.deviceId = deviceId;
                        telemetryDataPoint.temperature = currentTemperature;
                        telemetryDataPoint.humidity = currentHumidity;

                        msgStr = telemetryDataPoint.serialize();
                        msg = new Message(msgStr);
                    }
                    
                    System.out.println("Sending: " + msgStr);

                    Object lockobj = new Object();
                    EventCallback callback = new EventCallback();
                    client.sendEventAsync(msg, callback, lockobj);

                    synchronized (lockobj) {
                        lockobj.wait();
                    }
                    Thread.sleep(1000);
                }
            } catch (InterruptedException e) {
                System.out.println("Finished.");
            }
        }
    }
    ```
   
    이 메서드는 시뮬레이션된 장치에서 보낸 메시지에 `"level": "critical"` 속성을 임의로 추가합니다. 그러면 응용 프로그램 백 엔드에 의한 즉각적인 작업을 요구하는 메시지를 시뮬레이션합니다. 응용 프로그램에서 메시지 본문 대신 메시지 속성에 이 정보를 전달하므로 IoT Hub에서 메시지를 적절한 메시지 대상으로 라우팅할 수 있습니다.
   
   > [!NOTE]
   > 메시지 속성을 사용하면 여기서 보여 주는 실행 부하 과다 경로(hot path) 예제 외에도 실행 부하 과소 경로(cold path) 처리를 포함하여 다양한 시나리오의 메시지를 라우팅할 수 있습니다.
   > 
   > 

2. simulated-device\src\main\java\com\mycompany\app\App.java 파일을 저장한 후 닫습니다.
   
   > [!NOTE]
   > 간단히 하기 위해 이 자습서에서는 재시도 정책을 구현하지 않습니다. 프로덕션 코드에서는 MSDN 문서 [일시적인 오류 처리]에서 제시한 대로 재시도 정책(예: 지수 백오프)을 구현해야 합니다.
   > 
   > 

3. Maven을 사용하여 **simulated-device** 앱을 빌드하려면 simulated-device 폴더의 명령 프롬프트에서 다음 명령을 실행합니다.
   
    ```
    mvn clean package -DskipTests
    ```

## <a name="add-a-queue-to-your-iot-hub-and-route-messages-to-it"></a>IoT Hub에 큐 추가 및 메시지 라우팅
이 섹션에서는 Service Bus 큐를 만들고, IoT Hub에 연결하고, 메시지에 속성이 존재하는지 여부에 따라 큐에 메시지를 보내도록 IoT Hub를 구성합니다. Service Bus 큐에서 메시지를 처리하는 방법에 대한 자세한 내용은 [큐 시작][Service Bus queue]을 참조하세요.

1. [큐 시작][Service Bus queue]에서 설명한 대로 Service Bus 큐를 만듭니다. 네임스페이스 및 큐 이름을 적어둡니다.

2. Azure Portal에서 IoT Hub를 열고 **끝점**을 클릭합니다.
    
    ![IoT Hub의 끝점][30]

3. **끝점** 블레이드 위쪽에서 **추가**를 클릭하여 IoT Hub에 큐를 추가합니다. 끝점 이름을 **CriticalQueue**로 지정하고 드롭다운을 사용하여 **Service Bus 큐**, 큐가 있는 Service Bus 네임스페이스 및 큐 이름을 선택합니다. 완료되면 아래쪽의 **저장** 을 클릭합니다.
    
    ![끝점 추가][31]
    
4. 이제 IoT Hub에서 **경로**를 클릭합니다. 블레이드 위쪽에서 **추가**를 클릭하여 방금 추가한 큐로 메시지를 라우팅하는 라우팅 규칙을 만듭니다. 데이터 원본으로 **DeviceTelemetry**를 선택합니다. 조건으로 `level="critical"`을 입력하고 방금 사용자 지정 끝점으로 추가한 큐를 경로 규칙 끝점으로 선택합니다. 완료되면 아래쪽의 **저장** 을 클릭합니다.
    
    ![경로 추가][32]
    
    대체(fallback) 경로가 **ON**으로 설정되어 있는지 확인합니다. 이 설정은 IoT Hub의 기본 구성입니다.
    
    ![대체(fallback) 경로][33]


## <a name="optional-read-from-the-queue-endpoint"></a>(선택 사항) 큐 끝점에서 읽기
[큐 시작][lnk-sb-queues-java]의 지침에 따라 큐 끝점에서 메시지를 선택적으로 읽을 수 있습니다. 앱 이름을 **read-critical-queue**로 지정합니다.

## <a name="run-the-applications"></a>응용 프로그램 실행
이제 세 개의 응용 프로그램을 실행할 준비가 되었습니다.

1. **read-d2c-messages** 응용 프로그램을 실행하려면 명령 프롬프트 또는 셸에서 read-d2c 폴더로 이동한 후 다음 명령을 실행합니다.
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![read-d2c-messages 실행][readd2c]
2. **read-critical-queue** 응용 프로그램을 실행하려면 명령 프롬프트 또는 셸에서 read-critical-queue 폴더로 이동한 후 다음 명령을 실행합니다.
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![read-critical-messages 실행][readqueue]

3. **simulated-device** 앱을 실행하려면 명령 프롬프트 또는 셸에서 simulated-device 폴더로 이동한 후 다음 명령을 실행합니다.
   
   ```
   mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
   ```
   
   ![simulated-device 실행][simulateddevice]


## <a name="next-steps"></a>다음 단계
이 자습서에서는 IoT Hub의 메시지 라우팅 기능을 사용하여 장치-클라우드 메시지를 안정적으로 전달하는 방법을 살펴보았습니다.


[IoT Hub를 사용하여 클라우드-장치 메시지를 보내는 방법][lnk-c2d]에서는 솔루션 백 엔드에서 장치로 메시지를 보내는 방법을 보여줍니다.

IoT Hub를 사용하는 전체 종단 간 솔루션의 예를 보려면 [Azure IoT Suite][lnk-suite]를 참조하세요.

IoT Hub를 사용하여 솔루션을 개발하는 방법에 대한 자세한 내용은 [IoT Hub 개발자 가이드]를 참조하세요.

IoT Hub의 메시지 라우팅에 대한 자세한 내용은 [IoT Hub를 통해 메시지 보내고 받기][lnk-devguide-messaging]를 참조하세요.

<!-- Images. -->
<!-- TODO: UPDATE PICTURES -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[readd2c]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[readqueue]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/click-endpoints.png
[31]: ./media/iot-hub-java-java-process-d2c/endpoint-creation.png
[32]: ./media/iot-hub-java-java-process-d2c/route-creation.png
[33]: ./media/iot-hub-java-java-process-d2c/fallback-route.png

<!-- Links -->

[Azure blob storage]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure Data Factory]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus queue]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md
[lnk-sb-queues-java]: ../service-bus-messaging/service-bus-java-how-to-use-queues.md

[IoT Hub developer guide - Device to cloud]: iot-hub-devguide-messaging.md

[Azure Storage]: https://azure.microsoft.com/documentation/services/storage/
[Azure Service Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[simulated-device]: iot-hub-java-java-getstarted.md
[Azure IoT 개발자 센터]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Get Started with Event Hubs]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[일시적인 오류 처리]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-java
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub

