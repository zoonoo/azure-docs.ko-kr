<properties
 pageTitle="미리 구성된 Azure IoT 솔루션 | Microsoft Azure"
 description="미리 구성된 Azure IoT 솔루션에 대한 솔루션 및 추가 리소스에 대한 링크와 해당 아키텍처에 대한 설명입니다."
 services=""
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/30/2015"
 ms.author="dobett"/>

# 미리 구성된 Azure IoT Suite 솔루션은 무엇입니까?

미리 구성된 Azure IoT Suite 솔루션은 Azure 구독을 사용하여 Microsoft Azure에 배포할 수 있는 일반적인 IoT 솔루션 패턴의 구현입니다. 미리 구성된 솔루션을 다음으로 사용할 수 있습니다.

- 사용자 고유의 IoT 솔루션에 대한 시작점.
- IoT 솔루션 설계 및 개발에서 일반적인 패턴에 대해 알아보려면

미리 구성된 각 솔루션은 일반적인 IoT 시나리오를 구현하고 원격 분석을 생성하는 시뮬레이션된 장치를 사용하는 완전한 종단간 구현입니다.

Azure에서 미리 구성된 솔루션을 배포 및 실행하는 것 외에도 완전한 원본 코드를 다운로드한 다음 특정 IoT 요구 사항에 맞도록 솔루션을 사용자 지정하고 확장할 수 있습니다.

사용 가능한 미리 구성된 솔루션은 다음과 같습니다.

- [원격 모니터링][lnk-remote-monitoring]
- [예측 유지 관리][lnk-predictive-maintenance]

다음 표에서 미리 구성된 솔루션을 특정 IoT 기능에 매핑하는 방법을 보여 줍니다.

| 해결 방법 | 데이터 수집 | 장치 ID | 명령 및 제어 | 규칙 및 작업 | 예측 분석 |
|------------------------|-----|-----|-----|-----|-----|
| 원격 모니터링 | 예 | 예 | 예 | 예 | - |
| 예측 유지 관리 | 예 | 예 | 예 | 예 | 예 |

## 미리 구성된 원격 모니터링 솔루션 개요

이 섹션은 원격 모니터링 미리 구성된 솔루션의 핵심 요소 중 일부를 설명합니다. 원격 모니터링은 미리 구성된 솔루션의 가장 간단한 기능이며 다른 미리 구성된 솔루션을 공유하는 일반적인 디자인 요소를 보여줍니다.

다음 다이어그램에서는 원격 모니터링 솔루션의 핵심 요소를 보여줍니다. 다음 섹션에서는 이러한 요소에 대한 자세한 정보를 제공합니다.

![미리 구성된 원격 모니터링 솔루션 아키텍처][img-remote-monitoring-arch]

## 장치

원격 모니터링 미리 구성된 솔루션을 배포할 때 배포에는 실제 냉각 장치를 시뮬레이션하는 소프트웨어 장치 시뮬레이터의 인스턴스가 포함됩니다. 시뮬레이션된 장치는 IoT hub 끝점에 온도 및 습도 원격 분석을 보냅니다. 또한 시뮬레이션된 장치는 IoT hub를 통해 솔루션 포털에서 보낸 다음 명령에 응답합니다.

- Ping 장치
- 원격 분석 시작
- 원격 분석 중지
- 설정 지점 온도 변경
- 진단 원격 분석
- 장치 상태 변경

## IoT 허브

IoT hub는 단일 끝점의 냉각 장치에서 원격 분석을 받습니다. 또한 IoT hub는 각 장치가 전송되는 **Ping 장치** 명령과 같은 명령을 검색할 수 있는 장치 특정 끝점을 유지 관리합니다.

IoT Hub는 소비자 그룹 끝점을 통해 받는 원격 분석 데이터를 사용할 수 있도록 합니다.

미리 구성된 솔루션에서 IoT Hub 인스턴스는 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 *클라우드 게이트웨이*에 해당됩니다.

## Azure 스트림 분석

미리 구성된 솔루션은 세 가지 [Azure 스트림 분석][lnk-asa](ASA) 작업을 사용하여 쿨러 장치에서 원격 분석 스트림을 필터링합니다.

- 작업#1 콜드 저장소용 Azure Blob 저장소에 모든 원격 분석 전송
- 작업 #2 장치에서 명령 응답 메시지 및 장치 상태 업데이트 메시지를 식별하는 원격 분석 스트림을 필터링하고 Azure 이벤트 허브 끝점에 이러한 특정 메시지 전송
- 작업 #3 경보를 트리거하는 값에 대한 원격 분석 스트림 필터링 값이 경보를 트리거할 때 솔루션은 알림을 솔루션 포털의 대시보드 보기에 있는 경보 기록 테이블에 표시합니다.

미리 구성된 솔루션에서 ASA 작업은 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 *IoT 솔루션 백 엔드*의 일부를 형성합니다.

## 이벤트 프로세서

[WebJob][lnk-web-job]에서 실행되는 [EventPocessorHost][lnk-event-processor] 인스턴스는 ASA 작업 #2에서 식별된 명령 응답 및 장치 상태 메시지를 처리하고 해당 정보를 [Azure DocumentDB 데이터베이스][lnk-document-db]에 저장합니다.

미리 구성된 솔루션에서 이벤트 프로세서는 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 *IoT 솔루션 백 엔드*의 일부를 형성합니다.

## 솔루션 포털

솔루션 포털은 미리 구성된 솔루션의 일부로 클라우드에 배포된 웹 기반 UI입니다. 다음을 수행할 수 있습니다.

- 대시보드에서 원격 분석 및 경보 기록을 봅니다.
- 새 장치를 프로비전합니다.
- 장치를 관리 및 모니터링합니다.
- 특정 장치에 명령을 보냅니다.
- 규칙 및 작업을 관리합니다.

> [AZURE.NOTE]솔루션 포털은 솔루션의 DocumentDB 데이터베이스에서 다양한 장치 상태 정보의 저장소와 동기화된 IoT Hub [장치 ID 레지스트리][lnk-identity-registry]를 유지합니다.

미리 구성된 솔루션에서 솔루션 포털은 *IoT 솔루션 백 엔드*의 일부를 형성하며 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]에서 *처리 및 비즈니스 연결*의 일부입니다.

## 다음 단계

이 리소스를 탐색하여 IoT 미리 구성된 솔루션에 대해 자세히 알아봅니다.

- [Azure IoT 미리 구성된 솔루션 개요][lnk-suite-overview]
- [미리 구성된 loT 솔루션 시작][lnk-preconf-get-started]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: web-sites-create-web-jobs.md
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-identity-registry]: iot-hub-devguide.md#device-identity-registry
[lnk-suite-overview]: iot-suite-overview.md
[lnk-preconf-get-started]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_1210_2015-->
