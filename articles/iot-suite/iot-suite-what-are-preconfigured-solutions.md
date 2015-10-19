<properties
 pageTitle="Azure IoT 미리 구성된 솔루션 설명 | Microsoft Azure"
 description="미리 구성된 Azure IoT 솔루션에 대한 솔루션 및 추가 리소스에 대한 링크와 해당 아키텍처에 대한 설명입니다."
 services=""
 documentationCenter=".net"
 authors="aguilaaj"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="araguila"/>

# 미리 구성된 Azure IoT 솔루션은 무엇입니까?

Azure 구독을 사용하여 Microsoft Azure에 일반적인 IoT(사물 인터넷) 시나리오를 구현하는 미리 구성된 솔루션을 배포할 수 있습니다. 미리 구성된 솔루션을 다음으로 사용할 수 있습니다.

- 사용자 고유의 IoT 솔루션에 대한 시작점.
- IoT 솔루션 설계 및 개발에서 가장 일반적인 패턴에 대해 알아보려면

미리 구성된 각 솔루션은 일반적인 IoT 시나리오를 구현하고 이 솔루션은 완전한 종단간 구현입니다.

Azure에서 미리 구성된 솔루션을 배포 및 실행하는 것 외에도 완전한 소스 코드를 다운로드하여 특정 요구 사항에 맞도록 솔루션을 사용자 지정하고 확장할 수 있습니다.

사용 가능한 미리 구성된 솔루션은 다음과 같습니다.

- 원격 모니터링
- 예측 유지 관리

다음 표에서 미리 구성된 솔루션을 특정 IoT 기능에 매핑하는 방법을 보여 줍니다.

| 해결 방법 | 데이터 수집 | 장치 ID | 명령 및 제어 | 규칙 및 작업 | 예측 분석 |
|------------------------|----------------|-----------------|---------------------|-------------------|----------------------|
| 원격 모니터링 | 예 | 예 | 예 | 예 | - | | 예측 유지 관리 | 예 | 예 | 예 | 예 | 예 |

## 원격 모니터링 샘플 개요

원격 모니터링은 완전한 기능이 있는 가장 간단한 미리 구성된 솔루션입니다. 이 섹션은 미리 구성된 솔루션의 전체 집합에 대한 소개를 통해 미리 구성된 원격 모니터링 솔루션의 주요 기능 중 일부에 대해 설명합니다.

다음 다이어그램에서는 솔루션의 주요 기능 및 다음 섹션을 보여 줍니다. 다음 섹션에서는 이 다이어그램에서 표시된 요소에 대한 자세한 정보를 제공합니다.

![미리 구성된 원격 모니터링 솔루션 아키텍처][img-remote-monitoring-arch]

### 장치

미리 구성된 원격 모니터링 솔루션을 통해 미리 프로비전을 제공하는 장치는 온도 및 습도 원격 분석 데이터를 보내는 쿨러의 소프트웨어 시뮬레이션입니다. 장치는 IoT Hub를 통해 솔루션 포털에서 전송하는 명령 집합에 응답할 수도 있습니다. 시뮬레이터에서 이미 구현된 명령은 다음과 같습니다. Ping Device; Start Telemetry; Stop Telemetry; Change Set Point Temp; Diagnostic Telemetry; and Change Device State

미리 구성된 솔루션의 쿨러는 일반적인 IoT 솔루션 아키텍처의 **장치 및 데이터 원본**에 해당합니다.

### IoT Hub

IoT Hub는 단일 끝점에 있는 쿨러에서 원격 분석 데이터를 수신하고 장치에서 PingDevice 명령과 같은 명령을 검색할 수 있는 장치 특정 끝점을 유지합니다.

IoT Hub는 소비자 그룹 끝점을 통해 받는 원격 분석 데이터를 노출합니다.

미리 구성된 솔루션에서 IoT Hub 인스턴스는 일반적인 IoT 솔루션 아키텍처의 **IoT 백엔드 응용 프로그램**에 해당됩니다.

### Azure 스트림 분석

미리 구성된 솔루션은 [Azure 스트림 분석][] 작업을 사용하여 쿨러에서 이벤트 스트림을 필터링합니다. 하나의 작업은 콜드 저장소에 대해 모든 원격 분석 데이터를 Azure 저장소 Blob에 보냅니다. 두 번째 작업은 명령 응답 메시지 및 장치 상태 업데이트 메시지에 대한 이벤트 스트림을 필터링하고 이러한 특정 메시지를 Azure 이벤트 허브 끝점에 보냅니다. 세 번째 작업은 트리거된 경보를 필터링하고 솔루션 포털 대시보드 보기의 경보 기록 테이블에 해당 경보를 표시합니다.


### 이벤트 프로세서

웹 작업에서 실행되는 이벤트 프로세서 인스턴스는 명령 응답 및 장치 상태 데이터를 처리하고 해당 정보를 Azure DocumentDB 데이터베이스에 저장합니다.

### 솔루션 포털

솔루션 포털은 웹 기반 UI로 다음을 수행할 수 있습니다.

- 대시보드에서 원격 분석 및 경보 기록을 봅니다.
- 새 장치를 프로비전합니다.
- 장치를 관리 및 모니터링합니다.
- 특정 장치에 명령을 보냅니다.
- 규칙 및 작업을 관리합니다.

> [AZURE.NOTE]솔루션 포털의 장치 보기는 DocumentDB 데이터베이스의 다양한 장치 상태 정보의 저장소와 동기화된 IoT Hub 장치 ID 레지스트리를 유지합니다.

## 다음 단계

이 리소스를 탐색하여 IoT 미리 구성된 솔루션에 대해 자세히 알아봅니다.

- [Azure IoT 미리 구성된 솔루션 개요](iot-suite-overview.md)
- [미리 구성된 loT 솔루션 시작](iot-suite-getstarted-preconfigured-solutions.md)

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[Azure 스트림 분석]: https://azure.microsoft.com/services/stream-analytics/

<!---HONumber=Oct15_HO2-->