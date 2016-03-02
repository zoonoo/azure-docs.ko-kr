<properties
 pageTitle="미리 구성된 Azure IoT 솔루션 | Microsoft Azure"
 description="미리 구성된 Azure IoT 솔루션에 대한 솔루션 및 추가 리소스에 대한 링크와 해당 아키텍처에 대한 설명입니다."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="02/19/2016"
 ms.author="dobett"/>

# 미리 구성된 Azure IoT Suite 솔루션은 무엇입니까?

미리 구성된 Azure IoT Suite 솔루션은 Azure 구독을 사용하여 Microsoft Azure에 배포할 수 있는 일반적인 IoT 솔루션 패턴의 구현입니다. 미리 구성된 솔루션을 다음으로 사용할 수 있습니다.

- 사용자 고유의 IoT 솔루션에 대한 시작점.
- IoT 솔루션 설계 및 개발에서 일반적인 패턴에 대해 알아보려면

미리 구성된 각 솔루션은 일반적인 IoT 시나리오를 구현하고 원격 분석을 생성하는 시뮬레이션된 장치를 사용하는 완전한 종단간 구현입니다.

Azure에서 솔루션을 배포 및 실행하는 것 외에도 완전한 소스 코드를 다운로드한 다음 특정 IoT 요구 사항에 맞도록 솔루션을 사용자 지정하고 확장할 수 있습니다.

> [AZURE.NOTE] [미리 구성된 loT 솔루션 시작][lnk-preconf-get-started] 문서에서는 솔루션 중 하나를 배포 및 실행하는 방법을 설명합니다.

다음 표에서 솔루션을 특정 IoT 기능에 매핑하는 방법을 보여 줍니다.

| 해결 방법 | 데이터 수집 | 장치 ID | 명령 및 제어 | 규칙 및 작업 | 예측 분석 |
|------------------------|-----|-----|-----|-----|-----|
| [원격 모니터링][lnk-remote-monitoring] | 예 | 예 | 예 | 예 | - | 
| [예측 유지 관리][lnk-predictive-maintenance] | 예 | 예 | 예 | 예 | 예 |

## 미리 구성된 원격 모니터링 솔루션 개요

이 문서에서는 가장 간단한 솔루션이며 다른 솔루션에서 공유하는 일반적인 디자인 요소를 보여 주는 미리 구성된 원격 모니터링 솔루션에 대해 설명합니다.

다음 다이어그램에서는 원격 모니터링 솔루션의 핵심 요소를 보여줍니다. 다음 섹션에서는 이러한 요소에 대한 자세한 정보를 제공합니다.

![미리 구성된 원격 모니터링 솔루션 아키텍처][img-remote-monitoring-arch]

## 장치

원격 모니터링 미리 구성 된 솔루션을 배포하는 경우, 4개의 시뮬레이션된 장치가 냉각 장치를 시뮬레이션하는 솔루션에 미리 프로비전됩니다. 시뮬레이션된 이들 장치에는 원격 분석을 내보내는 기본 제공 온도 및 습도 모델이 있습니다.

장치를 원격 모니터링 미리 구성된 솔루션에 있는 IoT Hub에 처음으로 연결하는 경우, IoT Hub로 전송된 장치 정보 메시지는 장치가 응답할 수 있는 명령의 목록을 열거합니다. 원격 모니터링 미리 구성된 솔루션에는 다음과 같은 명령이 있습니다

- *Ping 장치*. 장치가 승인을 통해 이 명령에 응답합니다. 장치가 여전히 활성 상태로 수신하고 있는지 확인하는 데 유용합니다.
- *원격 분석 시작*. 장치가 원격 분석 보내기를 시작하도록 지시합니다.
- *원격 분석 중지*. 장치가 원격 분석 보내기를 중지하도록 지시합니다.
- *설정 지점 온도 변경*. 장치가 전송하는 시뮬레이션된 온도 원격 분석 값을 제어합니다. 테스트에 유용합니다.
- *원격 분석 진단*. 장치가 외부 온도를 원격 분석으로 보내야 할지 여부를 제어합니다.
- *장치 상태 변경*. 장치를 보고하는 장치 상태 메타 데이터 속성을 설정합니다. 테스트에 유용합니다.

동일한 원격 분석을 내보내고 동일한 명령에 응답하는 솔루션에 시뮬레이션된 장치를 추가할 수 있습니다.

## IoT 허브

IoT hub는 단일 끝점의 냉각 장치에서 원격 분석을 받습니다. 또한 IoT hub는 각 장치가 보내 명령을 검색할 수 있는 장치 특정 끝점을 관리합니다.

IoT Hub는 소비자 그룹 끝점을 통해 받는 원격 분석 데이터를 사용할 수 있도록 합니다.

미리 구성된 솔루션에서 IoT Hub 인스턴스는 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 *클라우드 게이트웨이*에 해당됩니다.

## Azure 스트림 분석

미리 구성된 솔루션은 세 가지 [Azure 스트림 분석][lnk-asa](ASA) 작업을 사용하여 쿨러 장치에서 원격 분석 스트림을 필터링합니다.


- *DeviceInfo 작업* - 솔루션 장치 레지스트리(DocumentDB 데이터베이스)에 장치 등록 특정 메시지를 보냅니다.
- *원격 분석 작업* - 콜드 저장소용 Azure Blob 저장소에 모든 원격 분석을 보내고 솔루션 대시보드에 표시하는 원격 분석 집계를 계산합니다.
- *규칙 작업* - 규칙 임계값을 초과하는 값에 대해 원격 분석 스트림을 필터링합니다. 규칙이 실행되면 솔루션 포털 대시보드 보기는 이 이벤트를 경보 기록 테이블에 새 행으로 표시하고 솔루션 포털의 규칙 및 작업 보기에 정의된 설정에 따라 작업을 트리거합니다.

미리 구성된 솔루션에서 ASA 작업은 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 *IoT 솔루션 백 엔드*의 일부를 형성합니다.

## 이벤트 프로세서

[WebJob][lnk-web-job]에서 실행 중인 [EventPocessorHost][lnk-event-processor] 인스턴스는 DeviceInfo 및 규칙 작업의 출력을 처리합니다.

미리 구성된 솔루션에서 이벤트 프로세서는 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 *IoT 솔루션 백 엔드*의 일부를 형성합니다.

## 솔루션 포털

![솔루션 대시보드][img-dashboard]

솔루션 포털은 미리 구성된 솔루션의 일부로 클라우드에 배포된 웹 기반 UI입니다. 다음을 수행할 수 있습니다.

- 대시보드에서 원격 분석 및 경보 기록을 봅니다.
- 새 장치를 프로비전합니다.
- 장치를 관리 및 모니터링합니다.
- 특정 장치에 명령을 보냅니다.
- 규칙 및 작업을 관리합니다.

> [AZURE.NOTE] 미리 구성된 솔루션은 보다 많은 장치 메타데이터를 저장하기 위해 IoT Hub [장치 ID 레지스트리][lnk-identity-registry]가 솔루션의 장치 레지스터리(DocumentDB 데이터베이스)와 동기화되도록 합니다.

미리 구성된 솔루션에서 솔루션 포털은 *IoT 솔루션 백 엔드*의 일부를 형성하며 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]에서 *처리 및 비즈니스 연결*의 일부입니다.

## 다음 단계

이 리소스를 탐색하여 IoT 미리 구성된 솔루션에 대해 자세히 알아봅니다.

- [미리 구성된 loT 솔루션 시작][lnk-preconf-get-started]
- [예측 정비 사전 구성 솔루션 개요][lnk-predictive-maintenance]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
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

<!-----HONumber=AcomDC_0224_2016-->