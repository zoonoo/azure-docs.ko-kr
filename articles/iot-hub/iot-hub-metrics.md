---
title: 메트릭을 사용하여 Azure IoT Hub 모니터링 | Microsoft Docs
description: Azure IoT Hub 메트릭을 사용하여 IoT Hub의 전반적인 상태를 평가하고 모니터링하는 방법입니다.
author: nberdy
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/25/2017
ms.author: nberdy
ms.openlocfilehash: 34a561c5bb523b914f4b88dc8c6edc2bd00627d0
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741932"
---
# <a name="understand-iot-hub-metrics"></a>IoT Hub 메트릭 이해

IoT Hub 메트릭은 Azure 구독의 Azure IoT 리소스 상태에 대한 더 나은 데이터를 제공합니다. IoT Hub 메트릭을 통해 IoT Hub 서비스와 연결된 장치의 전반적인 상태를 평가할 수 있습니다. 사용자 측 통계는 Azure 지원 센터에 문의할 필요 없이 IoT Hub의 상황을 파악하고 근본 원인을 해결할 수 있기 때문에 중요합니다.

메트릭은 기본적으로 사용하도록 설정됩니다. Azure Portal에서 IoT Hub 메트릭을 볼 수 있습니다.

## <a name="how-to-view-iot-hub-metrics"></a>IoT Hub 메트릭을 보는 방법

1. IoT Hub를 만듭니다. [장치에서 IoT Hub에 원격 분석 보내기](quickstart-send-telemetry-dotnet.md) 가이드에서 IoT 허브를 만드는 방법에 대한 지침을 찾을 수 있습니다.

2. IoT Hub의 블레이드를 엽니다. 여기서 **메트릭**을 클릭합니다.
   
    ![IoT Hub 리소스 페이지에 있는 메트릭 옵션을 보여주는 스크린샷](./media/iot-hub-metrics/enable-metrics-1.png)

3. 메트릭 블레이드에서 IoT Hub에 대한 메트릭을 보고 메트릭의 사용자 지정 뷰를 만들 수 있습니다. 
   
    ![IoT Hub에 대한 메트릭 페이지를 보여주는 스크린샷](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. **진단 설정** 및 **진단 설정 추가**를 차례로 클릭하여 Event Hubs 엔드포인트 또는 Azure Storage 계정으로 메트릭 데이터를 보낼 수 있습니다.

   ![진단 설정 단추의 위치를 보여주는 스크린샷](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>IoT Hub 메트릭 및 사용 방법

IoT Hub는 허브의 상태와 연결된 장치의 총 수에 대한 개요를 제공하는 몇 가지 메트릭을 제공합니다. IoT Hub 상태의 큰 그림을 그리기 위해 여러 가지 메트릭의 정보를 결합할 수 있습니다. 다음 테이블에는 각 IoT Hub가 추적하는 메트릭과 각 메트릭이 IoT hub의 전반적인 상태와 어떤 관련성이 있는지 설명합니다.

|메트릭|메트릭 표시 이름|단위|집계 형식|설명|차원|
|---|---|---|---|---|---|
|d2c<br>.telemetry<br>.ingress.<br>allProtocol|원격 분석 메시지 보내기 시도|개수|합계|IoT Hub로 보내려 한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|d2c<br>.telemetry<br>.ingress<br>.success|보낸 원격 분석 메시지|개수|합계|IoT Hub로 보내기 성공한 디바이스-클라우드 원격 분석 메시지 수|차원 없음|
|c2d<br>.commands<br>.egress<br>.complete<br>.success|완료된 명령|개수|합계|디바이스에서 성공적으로 완료한 클라우드-디바이스 명령 수|차원 없음|
|c2d<br>.commands<br>.egress<br>.abandon<br>.success|중단된 명령|개수|합계|디바이스에서 중단한 클라우드-디바이스 명령 수|차원 없음|
|c2d<br>.commands<br>.egress<br>.reject<br>.success|거부된 명령|개수|합계|디바이스에서 거부한 클라우드-디바이스 명령 수|차원 없음|
|devices<br>.totalDevices|총 디바이스(사용되지 않음)|개수|합계|IoT 허브에 등록된 디바이스 수|차원 없음|
|devices<br>.connectedDevices<br>.allProtocol|연결된 디바이스(사용되지 않음) |개수|합계|IoT 허브에 연결된 장치 수|차원 없음|
|d2c<br>.telemetry<br>.egress<br>.success|라우팅: 배달된 원격 분석 메시지|개수|합계|IoT Hub 라우팅을 사용하여 모든 엔드포인트에 메시지가 성공적으로 배달된 횟수입니다. 메시지가 여러 엔드포인트로 라우팅되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다. 메시지가 동일한 엔드포인트로 여러 번 배달되는 경우 이 값은 각 성공적인 배달에 대해 하나씩 증가합니다.|차원 없음|
|d2c<br>.telemetry<br>.egress<br>.dropped|라우팅: 삭제된 원격 분석 메시지 |개수|합계|데드 엔드포인트로 인해 IoT Hub 라우팅에서 메시지가 삭제된 횟수입니다. 삭제된 메시지는 배달되지 않으므로 이 값은 대체 경로에 배달된 메시지를 계산에 넣지 않습니다.|차원 없음|
|d2c<br>.telemetry<br>.egress<br>.orphaned|라우팅: 분리된 원격 분석 메시지 |개수|합계|메시지가 라우팅 규칙(대체 규칙 포함)과 일치하지 않았으므로 IoT Hub 라우팅에 의해 분리된 횟수입니다. |차원 없음|
|d2c<br>.telemetry<br>.egress<br>.invalid|라우팅: 원격 분석 메시지 호환되지 않음|개수|합계|IoT Hub 라우팅에서 엔드포인트와의 비호환성으로 인해 메시지를 배달하지 못한 횟수입니다. 이 값은 재시도를 포함하지 않습니다.|차원 없음|
|d2c<br>.telemetry<br>.egress<br>.fallback|라우팅: 대체에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 대체 경로와 연결된 엔드포인트에 메시지를 배달한 횟수입니다.|차원 없음|
|d2c<br>.endpoints<br>.egress<br>.eventHubs|라우팅: 이벤트 허브에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 이벤트 허브 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c<br>.endpoints<br>.latency<br>.eventHubs|라우팅: 이벤트 허브에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 이벤트 허브 엔드포인트에 대한 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c<br>.endpoints<br>.egress<br>.serviceBusQueues|라우팅: Service Bus 큐에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 큐 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c<br>.endpoints<br>.latency<br>.serviceBusQueues|라우팅: Service Bus 큐에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 큐 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c<br>.endpoints<br>.egress<br>.serviceBusTopics|라우팅: Service Bus 토픽에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 Service Bus 토픽 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c<br>.endpoints<br>.latency<br>.serviceBusTopics|라우팅: Service Bus 토픽에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 Service Bus 토픽 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c<br>.endpoints<br>.egress<br>.builtIn<br>.events|라우팅: 메시지/이벤트에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 기본 제공 엔드포인트(메시지/이벤트)에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c<br>.endpoints<br>.latency<br>. builtIn.events|라우팅: 메시지/이벤트에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 기본 제공 엔드포인트(메시지.이벤트)에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c<br>.endpoints<br>.egress<br>.storage|라우팅: 저장소에 배달된 메시지|개수|합계|IoT Hub 라우팅에서 저장소 엔드포인트에 메시지를 성공적으로 배달한 횟수입니다.|차원 없음|
|d2c<br>.endpoints<br>.latency<br>.storage|라우팅: 저장소에 대한 메시지 대기 시간|밀리초|평균|IoT Hub에 대한 메시지 수신과 저장소 엔드포인트에 대한 원격 분석 메시지 수신 간의 평균 대기 시간(밀리초)입니다.|차원 없음|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.bytes|라우팅: 저장소에 배달된 데이터|바이트|합계|IoT Hub 라우팅에서 저장소 엔드포인트에 배달된 데이터 양입니다(바이트).|차원 없음|
|d2c<br>.endpoints<br>.egress<br>.storage<br>.blobs|라우팅: 저장소에 배달된 Blob|개수|합계|IoT Hub 라우팅에서 저장소 엔드포인트에 Blob을 배달한 횟수입니다.|차원 없음|
|d2c<br>.twin<br>.read<br>.success|디바이스에서의 성공한 쌍 읽기|개수|합계|성공한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c<br>.twin<br>.read<br>.failure|디바이스에서의 실패한 쌍 읽기|개수|합계|실패한 모든 디바이스 시작 쌍 읽기 수입니다.|차원 없음|
|d2c<br>.twin<br>.read<br>.size|디바이스에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|d2c<br>.twin<br>.update<br>.success|디바이스에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|d2c<br>.twin<br>.update<br>.failure|디바이스에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 디바이스 시작 쌍 업데이트 수입니다.|차원 없음|
|d2c<br>.twin<br>.update<br>.size|디바이스에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 디바이스 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|c2d<br>.methods<br>.success|성공한 직접 메서드 호출|개수|합계|성공한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d<br>.methods<br>.failure|실패한 직접 메서드 호출|개수|합계|실패한 모든 직접 메서드 호출의 수입니다.|차원 없음|
|c2d<br>.methods<br>.requestSize|직접 메서드 호출의 요청 크기|바이트|평균|성공한 모든 직접 메서드 요청의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d<br>.methods<br>.responseSize|직접 메서드 호출의 응답 크기|바이트|평균|성공한 모든 직접 메서드 응답의 평균, 최소값, 최대값입니다.|차원 없음|
|c2d<br>.twin<br>.read<br>.success|백 엔드에서의 성공한 쌍 읽기|개수|합계|성공한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d<br>.twin<br>.read<br>.failure|백 엔드에서의 실패한 쌍 읽기|개수|합계|실패한 모든 백 엔드 시작 쌍 읽기 수입니다.|차원 없음|
|c2d<br>.twin<br>.read<br>.size|백 엔드에서의 쌍 읽기 응답 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 읽기 수의 평균, 최소값 및 최대값입니다.|차원 없음|
|c2d<br>.twin<br>.update<br>.success|백 엔드에서의 성공한 쌍 업데이트|개수|합계|성공한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|c2d<br>.twin<br>.update<br>.failure|백 엔드에서의 실패한 쌍 업데이트|개수|합계|실패한 모든 백 엔드 시작 쌍 업데이트 수입니다.|차원 없음|
|c2d<br>.twin<br>.update<br>.size|백 엔드에서의 쌍 업데이트 크기|바이트|평균|성공한 모든 백 엔드 시작 쌍 업데이트 수의 평균, 최소 및 최대 크기입니다.|차원 없음|
|twinQueries<br>.success|성공한 쌍 쿼리|개수|합계|성공한 모든 쌍 쿼리의 수입니다.|차원 없음|
|twinQueries<br>.failure|실패한 쌍 쿼리|개수|합계|실패한 모든 쌍 쿼리의 수입니다.|차원 없음|
|twinQueries<br>.resultSize|쌍 쿼리 결과 크기|바이트|평균|성공한 모든 쌍 쿼리 결과 크기의 평균, 최소값 및 최대값입니다.|차원 없음|
|jobs<br>.createTwinUpdateJob<br>.success|쌍 업데이트 작업에 대한 성공한 만들기|개수|합계|쌍 업데이트 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs<br>.createTwinUpdateJob<br>.failure|쌍 업데이트 작업에 대한 실패한 만들기|개수|합계|쌍 업데이트 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs<br>.createDirectMethodJob<br>.success|메서드 호출 작업에 대한 성공한 만들기|개수|합계|직접 메서드 호출 작업에 대한 성공한 모든 만들기의 수입니다.|차원 없음|
|jobs<br>.createDirectMethodJob<br>.failure|실패한 메서드 호출 작업 만들기|개수|합계|직접 메서드 호출 작업에 대한 실패한 모든 만들기의 수입니다.|차원 없음|
|jobs<br>.listJobs<br>.success|목록 작업에 대한 성공한 호출|개수|합계|목록 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs<br>.listJobs<br>.failure|목록 작업에 대한 실패한 호출|개수|합계|목록 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs<br>.cancelJob<br>.success|성공한 작업 취소|개수|합계|작업 취소에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs<br>.cancelJob<br>.failure|실패한 작업 취소|개수|합계|작업 취소에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs<br>.queryJobs<br>.success|성공한 작업 쿼리|개수|합계|쿼리 작업에 대한 성공한 모든 호출 수입니다.|차원 없음|
|jobs<br>.queryJobs<br>.failure|실패한 작업 쿼리|개수|합계|쿼리 작업에 대한 실패한 모든 호출 수입니다.|차원 없음|
|jobs<br>.completed|완료된 작업|개수|합계|완료된 모든 작업의 수입니다.|차원 없음|
|jobs<br>.failed|실패한 작업|개수|합계|실패한 모든 작업의 수입니다.|차원 없음|
|d2c<br>.telemetry<br>.ingress<br>.sendThrottle|제한 오류 수|개수|합계|디바이스 처리량 제한으로 인한 제한 오류 수|차원 없음|
|dailyMessage<br>QuotaUsed|사용된 전체 메시지 수|개수|평균|오늘 사용된 전체 메시지 수입니다. 매일 00:00 UTC에 0으로 다시 설정되는 누적 값입니다.|차원 없음|
|deviceDataUsage|총 디바이스 데이터 사용량(사용되지 않음)|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|차원 없음|
|deviceDataUsageV2|총 디바이스 데이터 사용(미리 보기)|바이트|합계|IotHub에 연결된 모든 디바이스에서 전송된 바이트|차원 없음|
|totalDeviceCount|총 디바이스(미리 보기)|개수|평균|IoT 허브에 등록된 디바이스 수|차원 없음|
|연결됨<br>DeviceCount|연결된 디바이스(미리 보기)|개수|평균|IoT 허브에 연결된 장치 수|차원 없음|
|구성|구성 메트릭|개수|합계|구성 작업에 대한 메트릭|차원 없음|

## <a name="next-steps"></a>다음 단계

지금까지 IoT Hub 메트릭의 개요를 살펴보았습니다. Azure IoT Hub를 관리하는 방법에 대한 자세한 내용을 보려면 다음 링크를 따라가십시오.

* [작업 모니터링](iot-hub-operations-monitoring.md)

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [IoT Hub 개발자 가이드](iot-hub-devguide.md)

* [Azure IoT Edge를 사용하여 에지 장치에 AI 배포](../iot-edge/tutorial-simulate-device-linux.md)
