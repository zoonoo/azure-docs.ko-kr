<properties
	pageTitle="IoT Hub 클라우드-장치 메시지를 처리 | Microsoft Azure"
	description="이 자습서를 수행하여 IoT Hub 장치-클라우드 메시지를 처리하는 데 유용한 패턴을 알아봅니다."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/29/2016"
     ms.author="dobett"/>

# 자습서: IoT Hub 장치-클라우드 메시지를 처리하는 방법

## 소개

Azure IoT Hub는 수백만의 IoT 장치와 응용 프로그램 백 엔드 간에서 안정적이고 안전한 양방향 통신이 가능하도록 완전히 관리되는 서비스입니다. 기타 자습서([IoT Hub로 시작] 및 [IoT Hub를 사용하여 클라우드-장치 메시지 보내기])는 IoT Hub의 기본 장치-클라우드 및 클라우드-장치 메시징 기능을 사용하는 방법을 보여 줍니다.

이 자습서는 [IoT Hub 시작] 자습서에 나와있는 코드에 기반하고 장치-클라우드 메시지를 처리하는 데 사용할 수 있는 확장성 있는 두 개의 패턴을 보여 줍니다.

- [Azure Blob 저장소]에서 장치-클라우드 메시지의 신뢰할 수 있는 저장소입니다. 매우 일반적인 시나리오는 분석 프로세스에 대한 입력으로 사용할 원격 분석 데이터를 blob에 저장하는 *콜드 경로* 분석입니다. 이러한 프로세스는 [Azure 데이터 팩터리] 또는 [HDInsight(Hadoop)] 스택과 같은 도구를 통해 진행됩니다.

- *대화형* 장치-클라우드 메시지의 신뢰할 수 있는 처리입니다. 장치-클라우드 메시지는 응용 프로그램 백 엔드에서 일련의 작업에 대해 즉각적인 트리거인 경우 대화형입니다. 예를 들어 장치는 CRM 시스템으로의 티켓 삽입을 트리거하는 경보 메시지를 보낼 수 있습니다. 이와 반대로 *데이터 요소* 메시지는 단순히 분석 엔진에 공급됩니다. 예를 들어 나중에 분석을 위해 저장해야 하는 장치의 온도 원격 분석이 데이터 요소 메시지에 해당합니다.

IoT Hub가 [이벤트 허브][lnk-event-hubs] 호환 끝점을 노출하여 장치-클라우드 메시지를 받기 때문에 이 자습서에서는 [EventProcessorHost] 인스턴스를 사용합니다. 이 인스턴스는 다음을 수행합니다.

* *데이터 요소* 메시지를 Azure Blob 저장소에 안정적으로 저장합니다.
* 즉시 처리를 위해 *대화형* 장치-클라우드 메시지를 [Azure 서비스 버스 큐]에 전달합니다.

서비스 버스는 메시지당 검사점 및 시간 기반 중복 제거 기능을 제공하므로 신뢰할 수 있게 대화형 메시지를 처리할 수 있도록 도와줍니다.

> [AZURE.NOTE] **EventProcessorHost** 인스턴스는 대화형 메시지를 처리하는 유일한 방법입니다. 기타 옵션으로는 [Azure 서비스 패브릭][lnk-service-fabric] 및 [Azure 스트림 분석][lnk-stream-analytics]이 있습니다.

이 자습서의 끝 부분에서 다음의 세 가지 Windows 콘솔 앱을 실행합니다.

* **SimulatedDevice**, [IoT Hub 시작] 자습서에서 만든 수정된 버전의 앱이며, 매초 데이터 요소 장치-클라우드 메시지를 보내고 10초마다 대화형 장치-클라우드 메시지를 보냅니다. 이 앱에서는 IoT Hub와 통신하는 데 AMQPS 프로토콜을 사용합니다.
* **ProcessDeviceToCloudMessages**에서는 [EventProcessorHost] 클래스를 사용하여 이벤트 허브 호환 끝점에서 메시지를 검색합니다. 그런 다음 Azure Blob 저장소에 데이터 요소 메시지를 안정적으로 저장하고 대화형 메시지를 서비스 버스 큐에 전달합니다.
* **ProcessD2CInteractiveMessages**는 서비스 버스 큐에서 대화형 메시지를 제거합니다.

> [AZURE.NOTE] IoT Hub는 많은 장치 플랫폼 및 언어(C, Java 및 JavaScript 포함)에 SDK를 지원합니다. 물리적 장치를 사용하여 이 자습서의 시뮬레이션된 장치를 바꾸는 방법 및 일반적으로 장치를 IoT Hub에 연결하는 방법에 대한 단계별 지침은 [Azure IoT 개발자 센터]를 참조하세요.

이 자습서는 [HDInsight(Hadoop)] 프로젝트와 같이 이벤트 허브와 호환되는 메시지를 사용하는 다른 방법에 직접 적용할 수 있습니다. 자세한 내용은 [Azure IoT Hub 개발자 가이드 - 장치-클라우드]를 참조하세요.

이 자습서를 완료하려면 다음이 필요합니다.

+ Microsoft Visual Studio 2015.

+ 활성 Azure 계정. <br/>Azure 구독이 없는 경우 몇 분 만에 [무료 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.

[Azure 저장소] 및 [Azure 서비스 버스]의 기본 지식이 있어야 합니다.


[AZURE.INCLUDE [iot-hub-process-d2c-device-csharp](../../includes/iot-hub-process-d2c-device-csharp.md)]


[AZURE.INCLUDE [iot-hub-process-d2c-cloud-csharp](../../includes/iot-hub-process-d2c-cloud-csharp.md)]

## 응용 프로그램 실행

이제 응용 프로그램을 실행할 준비가 되었습니다.

1.	솔루션 탐색기의 Visual Studio에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트 설정**을 선택합니다. **여러 개의 시작 프로젝트**를 선택한 다음 **ProcessDeviceToCloudMessages**, **SimulatedDevice** 및 **ProcessD2CInteractiveMessages** 프로젝트에 **시작**을 작업으로 선택합니다.

2.	**F5** 키를 눌러 세 가지 콘솔 응용 프로그램을 시작합니다. **ProcessD2CInteractiveMessages** 응용 프로그램은 **SimulatedDevice** 응용 프로그램에서 보낸 모든 대화형 메시지를 처리해야 합니다.

  ![3개의 콘솔 응용 프로그램][50]

> [AZURE.NOTE] Blob 파일의 업데이트를 보려면 **StoreEventProcessor** 클래스의 **MAX\_BLOCK\_SIZE** 상수를 **1024**와 같은 더 작은 값으로 줄여야 합니다. 즉, 시뮬레이션된 장치에서 보낸 데이터로 블록 크기 제한에 도달하는데 시간이 걸리기 때문입니다. 블록 크기가 작을수록 Blob가 만들어지고 업데이트되는 과정을 오래 기다리지 않습니다. 그러나 더 큰 블록 크기를 사용하면 응용 프로그램을 더 확장할 수 있습니다.

## 다음 단계

이 자습서에서 [EventProcessorHost]를 사용하여 안정적으로 데이터 요소 및 대화형 장치-클라우드 메시지를 처리하는 방법을 알아보았습니다.

[장치에서 파일 업로드] 자습서는 이 자습서를 토대로 유사한 메시지 처리 논리를 사용하여 작성되었습니다. 이 자습서는 장치에서 파일을 쉽게 업로드하기 위해 클라우드-장치 메시지를 사용하는 패턴도 설명합니다.

IoT Hub에 대한 추가 정보:

* [IoT Hub 개요]
* [IoT Hub 개발자 가이드]
* [IoT Hub 지침]
* [지원하는 장치 플랫폼 및 언어][Supported devices]
* [Azure IoT 개발자 센터]

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png


<!-- Links -->

[Azure Blob 저장소]: ../storage/storage-dotnet-how-to-use-blobs.md
[Azure 데이터 팩터리]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight(Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues/
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx


[일시적인 오류 처리]: https://msdn.microsoft.com/ko-KR/library/hh680901(v=pandp.50).aspx

[Azure IoT Hub 개발자 가이드 - 장치-클라우드]: iot-hub-devguide.md#d2c

[Azure 저장소]: https://azure.microsoft.com/documentation/services/storage/
[Azure 서비스 버스]: https://azure.microsoft.com/documentation/services/service-bus/



[IoT Hub를 사용하여 클라우드-장치 메시지 보내기]: iot-hub-csharp-csharp-c2d.md
[장치-클라우드 메시지 처리]: iot-hub-csharp-csharp-process-d2c.md
[장치에서 파일 업로드]: iot-hub-csharp-csharp-file-upload.md

[IoT Hub 개요]: iot-hub-what-is-iot-hub.md
[IoT Hub 지침]: iot-hub-guidance.md
[IoT Hub 개발자 가이드]: iot-hub-devguide.md
[IoT Hub 시작]: iot-hub-csharp-csharp-getstarted.md
[IoT Hub로 시작]: iot-hub-csharp-csharp-getstarted.md
[Supported devices]: iot-hub-tested-configurations.md
[Azure IoT 개발자 센터]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/

<!---HONumber=AcomDC_0601_2016-->