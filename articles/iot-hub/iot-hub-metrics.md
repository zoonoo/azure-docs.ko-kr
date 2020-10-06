---
title: 메트릭을 사용하여 Azure IoT Hub 모니터링 | Microsoft Docs
description: Azure IoT Hub 메트릭을 사용하여 IoT Hub의 전반적인 상태를 평가하고 모니터링하는 방법입니다.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: c448d7e5a5e0bea29063930bed3a59a0461b8cf5
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767623"
---
<!--for build: for each metric, if you understand what it is, it's ok. otw add more info.  -->
# <a name="understand-iot-hub-metrics"></a>IoT Hub 메트릭 이해

IoT Hub 메트릭은 Azure 구독에서 Azure IoT 리소스의 상태에 대 한 정보를 제공 합니다. IoT Hub 메트릭은 IoT Hub 서비스의 전반적인 상태와 연결 된 장치를 평가 하는 데 도움이 됩니다. 이러한 사용자 지향 통계를 통해 IoT hub를 사용 하는 상황을 확인 하 고 Azure 지원에 문의 하지 않고도 문제에 대 한 근본 원인 분석을 수행할 수 있습니다.

메트릭은 기본적으로 사용하도록 설정됩니다. Azure Portal에서 IoT Hub 메트릭을 볼 수 있습니다.

> [!NOTE]
> IoT Hub 메트릭을 사용 하 여 IoT Hub에 연결 된 [IoT 플러그 앤 플레이](../iot-pnp/overview-iot-plug-and-play.md) 장치에 대 한 정보를 볼 수 있습니다.

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub 메트릭을 보는 방법

1. IoT Hub를 만듭니다. [디바이스에서 IoT Hub에 원격 분석 보내기](quickstart-send-telemetry-dotnet.md) 가이드에서 IoT 허브를 만드는 방법에 대한 지침을 찾을 수 있습니다.

2. IoT Hub의 블레이드를 엽니다. 여기서 **메트릭**을 클릭합니다.
   
    ![IoT Hub 리소스 페이지에 있는 메트릭 옵션을 보여주는 스크린샷](./media/iot-hub-metrics/enable-metrics-1.png)

3. 메트릭 블레이드에서 IoT Hub에 대한 메트릭을 보고 메트릭의 사용자 지정 뷰를 만들 수 있습니다. 
   
    ![IoT Hub에 대한 메트릭 페이지를 보여주는 스크린샷](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. **진단 설정**을 클릭 한 다음 **진단 설정 추가**를 클릭 하 여 Event Hubs 끝점 또는 Azure Storage 계정에 메트릭 데이터를 보내도록 선택할 수 있습니다.

   ![진단 설정 단추의 위치를 보여주는 스크린샷](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub 메트릭 및 사용 방법

IoT Hub는 허브의 상태와 연결된 디바이스의 총 수에 대한 개요를 제공하는 몇 가지 메트릭을 제공합니다. IoT Hub 상태의 큰 그림을 그리기 위해 여러 가지 메트릭의 정보를 결합할 수 있습니다. 다음 테이블에는 각 IoT Hub가 추적하는 메트릭과 각 메트릭이 IoT hub의 전반적인 상태와 어떤 관련성이 있는지 설명합니다.

|메트릭|메트릭 표시 이름|단위|집계 형식|설명:|차원|
|---|---|---|---|---|---|
|RoutingDeliveries | 라우팅 배달 시도 (미리 보기) | 개수 | 합계 |라우팅 배달 메트릭입니다. 차원을 사용 하 여 특정 끝점 또는 특정 라우팅 원본의 배달 상태를 식별할 수 있습니다.| ResourceID<br>결과,<br>RoutingSource,<br>EndpointType<br>FailureReasonCategory,<br>EndpointName<br>*차원에 대 한 자세한 내용은 [**여기**](#dimensions)* 를 참조 하세요. |
|RoutingDeliveryLatency| 라우팅 대기 시간 (미리 보기) | 밀리초 | 평균 |라우팅 전달 대기 시간 메트릭입니다. 차원을 사용 하 여 특정 끝점 또는 특정 라우팅 원본의 대기 시간을 확인 합니다.| ResourceID<br>RoutingSource,<br>EndpointType<br>EndpointName<br>*차원에 대 한 자세한 내용은 [**여기**](#dimensions)* 를 참조 하세요.|
|RoutingDataSizeInBytesDelivered| 라우팅 배달 데이터 크기 (바이트) (미리 보기)| 바이트 | 합계 |사용자 지정 끝점과 기본 제공 끝점에 IoT Hub 의해 라우팅되는 총 바이트 수입니다. 차원을 사용 하 여 특정 끝점 또는 특정 라우팅 원본에 대해 라우팅되는 데이터 크기를 식별할 수 있습니다.| ResourceID<br>RoutingSource,<br>EndpointType<br>EndpointName<br>*차원에 대 한 자세한 내용은 [**여기**](#dimensions)* 를 참조 하세요.|
|d2c.<br>allProtocol|원격 분석 메시지 보내기 시도|개수|합계|IoT Hub로 보내려 한 디바이스-클라우드 원격 분석 메시지 수|없음|
|d2c.<br>성공|보낸 원격 분석 메시지|개수|합계|IoT Hub로 보내기 성공한 디바이스-클라우드 원격 분석 메시지 수|없음|
|c2d.<br>완료. 성공|C2D 메시지 배달 완료|개수|합계|장치에서 성공적으로 완료 한 클라우드-장치 메시지 배달 수|없음|
|c2d.<br>중단. 성공|중단된 C2D 메시지|개수|합계|장치에서 중단 한 클라우드-장치 메시지 수|없음|
|c2d.<br>거부. 성공|거부된 C2D 메시지|개수|합계|장치에서 거부 한 클라우드-장치 메시지 수|없음|
|C2DMessagesExpired|만료된 C2D 메시지(미리 보기)|개수|합계|만료 된 클라우드-장치 메시지 수|없음|
|devices.totalDevices|총 디바이스(사용되지 않음)|개수|합계|IoT 허브에 등록된 디바이스 수|없음|
|connectedDevices.<br>allProtocol|연결된 디바이스(사용되지 않음) |개수|합계|IoT 허브에 연결된 디바이스 수|없음|
|d2c.<br>성공|라우팅: 배달된 원격 분석 메시지|개수|합계|IoT Hub 라우팅을 사용하여 모든 엔드포인트에 메시지가 성공적으로 배달된 횟수입니다. 메시지가 여러 엔드포인트로 라우팅되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다. 메시지가 동일한 엔드포인트로 여러 번 배달되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다.|없음|
|d2c.<br>놓도록|라우팅: 삭제된 원격 분석 메시지 |개수|합계|데드 엔드포인트로 인해 IoT Hub 라우팅에서 메시지가 삭제된 횟수입니다. 삭제된 메시지는 배달되지 않으므로 이 값은 대체 경로에 배달된 메시지를 계산에 넣지 않습니다.|없음|
|d2c.<br>분리|라우팅: 분리된 원격 분석 메시지 |개수|합계|대체 경로를 사용할 수 없는 경우 라우팅 쿼리와 일치 하지 않기 때문에 IoT Hub 라우팅이 메시지를 분리 한 횟수입니다.|없음|
|d2c.<br>잘못됨|라우팅: 원격 분석 메시지 호환되지 않음|개수|합계|IoT Hub 라우팅에서 엔드포인트와의 비호환성으로 인해 메시지를 배달하지 못한 횟수입니다. Iot Hub가 끝점에 메시지를 배달 하려고 시도 하 고 일시적 오류가 아닌 오류로 인해 실패 하는 경우 메시지는 끝점과 호환 되지 않습니다. 잘못 된 메시지는 다시 시도 되지 않습니다. 이 값은 재시도를 포함하지 않습니다.|없음|
|d2c.<br>fallback|라우팅: 대체에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 대체 경로와 연결된 엔드포인트에 메시지를 배달한 횟수입니다.|없음|
|d2c.<br>eventHubs|라우팅: 이벤트 허브에 배달된 메시지|개수|합계|이벤트 허브 형식의 사용자 지정 끝점에 성공적으로 메시지를 전달 IoT Hub 횟수입니다. 여기에는 기본 제공 끝점 (이벤트)에 대 한 경로 메시지가 포함 되지 않습니다.|없음|
|d2c.<br>eventHubs|라우팅: 이벤트 허브에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대 한 메시지 수신 간의 평균 대기 시간 (밀리초) 및 이벤트 허브 유형의 사용자 지정 끝점에 대 한 메시지 수신입니다. 여기에는 기본 제공 끝점 (이벤트)에 대 한 경로 메시지가 포함 되지 않습니다.|없음|
|d2c.<br>serviceBusQueues|라우팅: Service Bus 큐에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 큐 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|d2c.<br>serviceBusQueues|라우팅: Service Bus 큐에 대한 메시지 대기 시간|밀리초|평균|메시지 수신 간의 평균 대기 시간 (밀리초)을 IoT Hub 하 고 Service Bus 큐 끝점으로 메시지를 수신 합니다.|없음|
|d2c.<br>serviceBusTopics|라우팅: Service Bus 토픽에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 토픽 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|d2c.<br>serviceBusTopics|라우팅: Service Bus 토픽에 대한 메시지 대기 시간|밀리초|평균|메시지 수신 간의 평균 대기 시간 (밀리초)을 IoT Hub 하 고 Service Bus 토픽 끝점으로 메시지를 수신 합니다.|없음|
|d2c.<br>기본 제공 이벤트|라우팅: 메시지/이벤트에 배달된 메시지|개수|합계|기본 제공 끝점 (메시지/이벤트) 및 대체 (fallback) 경로에 성공적으로 메시지를 전달 IoT Hub 횟수입니다.|없음|
|d2c.<br>기본 제공 이벤트|라우팅: 메시지/이벤트에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대 한 메시지 수신 간의 평균 대기 시간 (밀리초) 및 기본 제공 끝점 (메시지/이벤트) 및 대체 경로에 대 한 메시지 수신 간의 평균 대기 시간 (밀리초)입니다.|없음|
|d2c.<br>스토리지|라우팅: 스토리지에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|없음|
|d2c.<br>스토리지|라우팅: 스토리지에 대한 메시지 대기 시간|밀리초|평균|메시지 수신 간의 평균 대기 시간 (밀리초)을 IoT Hub 하 고 저장소 끝점으로 메시지를 수신 합니다.|없음|
|d2c.<br>저장소 바이트|라우팅: 스토리지에 배달된 데이터|바이트|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 배달된 데이터 양입니다(바이트).|없음|
|d2c.<br>저장소. blob|라우팅: 스토리지에 배달된 Blob|개수|합계|IoT Hub 라우팅에서 스토리지 엔드포인트에 Blob을 배달한 횟수입니다.|없음|
|EventGridDeliveries|Event Grid 배달(미리 보기)|개수|합계|Event Grid에 게시 된 IoT Hub 이벤트 수입니다. 성공 및 실패 한 요청의 수에 대해 결과 차원을 사용 합니다. EventType dimension 이벤트 유형 ()을 표시 합니다 https://aka.ms/ioteventgrid) .|ResourceId<br/>결과,<br/>EventType|
|EventGridLatency|Event Grid 대기 시간(미리 보기)|밀리초|평균|Event Grid에 이벤트가 게시 될 때 Iot Hub 이벤트가 생성 된 시간에 대 한 평균 대기 시간 (밀리초)입니다. 이 숫자는 모든 이벤트 유형 사이의 평균입니다. 특정 유형의 이벤트에 대 한 대기 시간을 확인 하려면 EventType 차원을 사용 합니다.|ResourceId<br/>EventType|
|d2c.twin.read.success|디바이스에서의 성공한 쌍 읽기|개수|합계|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|없음|
|d2c.twin.read.failure|디바이스에서의 실패한 쌍 읽기|개수|합계|실패한 모든 디바이스 시작 쌍 읽기 수입니다.|없음|
|d2c.twin.read.size|디바이스에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 장치 시작 쌍 읽기 수입니다.|없음|
|d2c.twin.update.success|디바이스에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 디바이스 시작 쌍 업데이트 수입니다.|없음|
|d2c.twin.update.failure|디바이스에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 디바이스 시작 쌍 업데이트 수입니다.|없음|
|d2c.twin.update.size|디바이스에서의 쌍 업데이트 크기|바이트|평균|모든 성공한 장치 시작 쌍 업데이트의 총 크기입니다.|없음|
|c2d.methods.success|성공한 직접 메서드 호출|개수|합계|성공한 모든 직접 메서드 호출의 수입니다.|없음|
|c2d.methods.failure|실패한 직접 메서드 호출|개수|합계|실패한 모든 직접 메서드 호출의 수입니다.|없음|
|c2d.methods.requestSize|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청 수입니다.|없음|
|c2d.methods.responseSize|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 수입니다.|없음|
|c2d.twin.read.success|백 엔드에서의 성공한 쌍 읽기|개수|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|없음|
|c2d.twin.read.failure|백 엔드에서의 실패한 쌍 읽기|개수|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|없음|
|c2d.twin.read.size|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|없음|
|c2d.twin.update.success|백 엔드에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|없음|
|c2d.twin.update.failure|백 엔드에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|없음|
|c2d.twin.update.size|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트의 총 크기입니다.|없음|
|twinQueries.success|성공한 쌍 쿼리|개수|합계|성공한 모든 쌍 쿼리의 수입니다.|없음|
|twinQueries.failure|실패한 쌍 쿼리|개수|합계|실패한 모든 쌍 쿼리의 수입니다.|없음|
|twinQueries.resultSize|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리의 결과 크기의 합계입니다.|없음|
|createTwinUpdateJob.<br>성공|쌍 업데이트 작업에 대한 성공한 만들기|개수|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|없음|
|createTwinUpdateJob.<br>실패|쌍 업데이트 작업에 대한 실패한 만들기|개수|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|없음|
|jobs. createDirectMethodJob.<br>성공|메서드 호출 작업에 대한 성공한 만들기|개수|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|없음|
|jobs. createDirectMethodJob.<br>실패|실패한 메서드 호출 작업 만들기|개수|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|없음|
|jobs.listJobs.success|목록 작업에 대한 성공한 호출|개수|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|없음|
|jobs.listJobs.failure|목록 작업에 대한 실패한 호출|개수|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|없음|
|jobs.cancelJob.success|성공한 작업 취소|개수|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|없음|
|jobs.cancelJob.failure|실패한 작업 취소|개수|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|없음|
|jobs.queryJobs.success|성공한 작업 쿼리|개수|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|없음|
|jobs.queryJobs.failure|실패한 작업 쿼리|개수|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|없음|
|jobs.completed|완료된 작업|개수|합계|완료된 모든 작업의 수입니다.|없음|
|jobs.failed|실패한 작업|개수|합계|실패한 모든 작업의 수입니다.|없음|
|d2c.<br>sendThrottle|제한 오류 수|개수|합계|디바이스 처리량 제한으로 인한 제한 오류 수|없음|
|dailyMessageQuotaUsed|사용된 전체 메시지 수|개수|평균|오늘 사용된 전체 메시지 수입니다. 매일 00:00 UTC에 0으로 다시 설정되는 누적 값입니다.|없음|
|deviceDataUsage|총 디바이스 데이터 사용량|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|없음|
|deviceDataUsageV2|총 디바이스 데이터 사용량(미리 보기)|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|없음|
|totalDeviceCount|총 디바이스(미리 보기)|개수|평균|IoT 허브에 등록된 디바이스 수|없음|
|connectedDeviceCount|연결된 디바이스(미리 보기)|개수|평균|IoT 허브에 연결된 디바이스 수|없음|
|구성|구성 메트릭|개수|합계|대상 장치 집합에서 장치 구성 및 IoT Edge 배포에 대해 수행 된 총 CRUD 작업 수입니다. 이러한 구성으로 인해 장치 쌍 또는 모듈 쌍을 수정 하는 작업의 수도 포함 됩니다.|없음|

### <a name="dimensions"></a>차원
차원은 메트릭에 대 한 자세한 정보를 식별 하는 데 도움이 됩니다. 일부 라우팅 메트릭은 끝점 당 정보를 제공 합니다. 다음 표에서는 이러한 차원에 사용할 수 있는 값을 나열 합니다.

|차원|값|
|---|---|
|ResourceID|IoT Hub 리소스|
|결과|성공<br>실패|
|RoutingSource|장치 메시지<br>쌍 변경 이벤트<br>장치 수명 주기 이벤트|
|EndpointType|eventHubs<br>serviceBusQueues<br>cosmosDB<br>serviceBusTopics<br>제공<br>blobStorage|
|FailureReasonCategory|잘못됨<br>놓도록<br>분리<br>null|
|EndpointName|엔드포인트 이름|

## <a name="next-steps"></a>다음 단계

지금까지 IoT Hub 메트릭의 개요를 살펴보았습니다. Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 보려면 다음 링크를 따라가십시오.

* [진단 로그 설정](iot-hub-monitor-resource-health.md)

* [메시지 라우팅 문제를 해결 하는 방법 알아보기](troubleshoot-message-routing.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)

* [Azure IoT Edge를 사용하여 에지 디바이스에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)
