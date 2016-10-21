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
 ms.date="08/09/2016"
 ms.author="dobett"/>

# 미리 구성된 Azure IoT Suite 솔루션은 무엇입니까?

미리 구성된 Azure IoT Suite 솔루션은 구독을 사용하여 Azure에 배포할 수 있는 일반적인 IoT 솔루션 패턴의 구현입니다. 미리 구성된 솔루션을 다음으로 사용할 수 있습니다.

- 사용자 고유의 IoT 솔루션에 대한 시작점.
- IoT 솔루션 설계 및 개발에서 일반적인 패턴에 대해 알아보려면

미리 구성된 각 솔루션은 시뮬레이션된 장치를 사용하여 원격 분석을 생성하는 완전한 종단 간 구현입니다.

Azure에서 솔루션을 배포 및 실행하는 것 외에도 완전한 소스 코드를 다운로드한 다음 특정 IoT 요구 사항에 맞도록 솔루션을 사용자 지정하고 확장할 수 있습니다.

> [AZURE.NOTE] 미리 구성된 솔루션 중 하나를 배포하려면 [Microsoft Azure IoT Suite][lnk-azureiotsuite]를 방문합니다. [미리 구성된 IoT 솔루션 시작][lnk-getstarted-preconfigured] 문서에서는 솔루션 중 하나를 배포 및 실행하는 방법에 대한 정보를 제공합니다.

다음 표에서 솔루션을 특정 IoT 기능에 매핑하는 방법을 보여 줍니다.

| 해결 방법 | 데이터 수집 | 장치 ID | 명령 및 제어 | 규칙 및 작업 | 예측 분석 |
|------------------------|-----|-----|-----|-----|-----|
| [원격 모니터링][lnk-getstarted-preconfigured] | 예 | 예 | 예 | 예 | - |
| [예측 유지 관리][lnk-predictive-maintenance] | 예 | 예 | 예 | 예 | 예 |

- *데이터 수집*: 클라우드에 대한 대규모 데이터의 수신입니다.
- *장치 ID*: 연결된 모든 장치의 고유한 ID를 관리합니다.
- *명령 및 컨트롤*: 장치가 일부 작업을 수행하도록 클라우드에서 장치에 메시지를 보냅니다.
- *규칙 및 동작*: 솔루션 백 엔드는 규칙을 사용하여 특정 장치-클라우드 데이터에서 작동합니다.
- *예측 분석*: 솔루션 백 엔드 적용은 장치-클라우드 데이터를 분석하여 특정 작업이 수행될 때를 예측합니다. 예를 들어 항공기 엔진 원격 분석을 분석하여 엔진 유지 관리가 만료될 때를 확인합니다.

## 미리 구성된 원격 모니터링 솔루션 개요

이 문서에서는 다른 솔루션에서 공유하는 여러 가지 일반적인 디자인 요소를 보여주는 미리 구성된 원격 모니터링 솔루션에 대해 설명합니다.

다음 다이어그램에서는 원격 모니터링 솔루션의 핵심 요소를 보여줍니다. 다음 섹션에서는 이러한 요소에 대한 자세한 정보를 제공합니다.

![미리 구성된 원격 모니터링 솔루션 아키텍처][img-remote-monitoring-arch]

## 장치

원격 모니터링 미리 구성 된 솔루션을 배포하는 경우, 4개의 시뮬레이션된 장치가 냉각 장치를 시뮬레이션하는 솔루션에 미리 프로비전됩니다. 이러한 시뮬레이션된 장치에는 원격 분석을 내보내는 기본 제공 온도 및 습도 모델이 있습니다. 사용자가 사용자 지정 구현을 위한 시작 지점으로 솔루션을 사용하는 백 엔드 개발자인 경우 시뮬레이션된 장치는 솔루션을 통해 데이터의 종단 간 흐름을 설명하고 명령에 대한 원격 분석 및 대상이라는 편리한 소스를 제공합니다.

장치를 원격 모니터링 미리 구성된 솔루션에 있는 IoT Hub에 처음으로 연결하는 경우, IoT Hub로 전송된 장치 정보 메시지는 장치가 응답할 수 있는 명령의 목록을 열거합니다. 원격 모니터링 미리 구성된 솔루션에는 다음과 같은 명령이 있습니다

- *Ping 장치*: 장치가 승인을 사용하여 이 명령에 응답합니다. 장치가 여전히 활성 상태로 수신하고 있는지 확인하는 데 유용합니다.
- *원격 분석 시작*: 장치가 원격 분석 보내기를 시작하도록 지시합니다.
- *원격 분석 중지*: 장치가 원격 분석 보내기를 중지하도록 지시합니다.
- *설정 지점 온도 변경*: 장치가 전송하는 시뮬레이션된 온도 원격 분석 값을 제어합니다. 백 엔드 논리를 테스트하는 데 유용합니다.
- *진단 원격 분석*: 장치가 외부 온도를 원격 분석으로 보내야 할지 여부를 제어합니다.
- *장치 상태 변경*.: 장치가 보고하는 장치 상태 메타데이터 속성을 설정합니다. 백 엔드 논리를 테스트하는 데 유용합니다.

동일한 원격 분석을 내보내고 동일한 명령에 응답하는 솔루션에 시뮬레이션된 장치를 더 추가할 수 있습니다.

## IoT 허브

미리 구성된 솔루션에서 IoT Hub 인스턴스는 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 *클라우드 게이트웨이*에 해당됩니다.

IoT hub는 단일 끝점의 장치에서 원격 분석을 수신합니다. 또한 IoT hub는 각 장치가 보내 명령을 검색할 수 있는 장치 특정 끝점을 관리합니다.

IoT hub를 사용하면 서비스 쪽 원격 분석 읽기 끝점을 통해 수신한 원격 분석을 사용할 수 있습니다.

## Azure 스트림 분석

미리 구성된 솔루션은 세 가지 [Azure 스트림 분석][lnk-asa](ASA) 작업을 사용하여 장치에서 원격 분석 스트림을 필터링합니다.


- *DeviceInfo 작업* - 장치 등록 특정 메시지를 라우팅하는 이벤트 허브에 데이터를 출력하며 이는 장치를 처음 연결할 때 또는 **장치 상태 변경** 명령이나 솔루션 장치 레지스트리(DocumentDB 데이터베이스)에 대한 응답으로 전송됩니다.
- *원격 분석 작업* - 콜드 저장소용 Azure Blob 저장소에 모든 원시 원격 분석을 보내고 솔루션 대시보드에 표시하는 원격 분석 집계를 계산합니다.
- *규칙 작업* - 규칙 임계값을 초과하는 값에 대해 원격 분석 스트림을 필터링하고 이벤트 허브에 대한 데이터를 출력합니다. 규칙이 실행되면 솔루션 포털 대시보드 보기는 이 이벤트를 경보 기록 테이블에 새 행으로 표시하고 솔루션 포털의 규칙 및 작업 보기에 정의된 설정에 따라 작업을 트리거합니다.

미리 구성된 솔루션에서 ASA 작업은 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 **IoT 솔루션 백 엔드**의 일부를 형성합니다.

## 이벤트 프로세서

미리 구성된 솔루션에서 이벤트 프로세서는 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]의 **IoT 솔루션 백 엔드**의 일부를 형성합니다.

**DeviceInfo** 및 **규칙** ASA 작업은 다른 백 엔드 서비스에 배달하기 위해 이벤트 허브에 해당 출력을 보냅니다. 솔루션은 [WebJob][lnk-web-job]에서 실행 중인 [EventPocessorHost][lnk-event-processor] 인스턴스를 사용하여 이러한 이벤트 허브에서 메시지를 읽습니다. **EventProcessorHost**는 **DeviceInfo** 데이터를 사용하여 DocumentDB 데이터베이스의 장치 데이터를 업데이트하며 **규칙** 데이터를 사용하여 논리 앱을 호출하고 솔루션 포털에 표시할 경고를 업데이트합니다.

## 장치 ID 레지스트리 및 DocumentDB

모든 IoT Hub는 장치 키를 저장하는 [장치 ID 레지스트리][lnk-identity-registry]를 포함합니다. IoT Hub는 이 정보를 사용하여 장치를 인증하며 허브에 연결하려면 장치를 등록해야 하고 유효한 키가 있어야 합니다.

이 솔루션은 해당 상태, 지원하는 명령 및 기타 메타데이터와 같은 장치에 대한 추가 정보를 저장합니다. 솔루션은 DocumentDB 데이터베이스를 사용하여 이 솔루션별 장치 데이터를 저장하고 솔루션 포털은 이 DocumentDB 데이터베이스의 데이터를 표시 및 편집하기 위해 검색합니다.

또한 솔루션은 DocumentDB 데이터베이스의 컨텐츠로 동기화된 장치 ID 레지스트리에 정보를 보관해야 합니다. **EventProcessorHost**는 **DeviceInfo** 스트림 분석 작업의 데이터를 사용하여 동기화를 관리합니다.

## 솔루션 포털

![솔루션 대시보드][img-dashboard]

솔루션 포털은 미리 구성된 솔루션의 일부로 클라우드에 배포된 웹 기반 UI입니다. 다음을 수행할 수 있습니다.

- 대시보드에서 원격 분석 및 경보 기록을 봅니다.
- 새 장치를 프로비전합니다.
- 장치를 관리 및 모니터링합니다.
- 특정 장치에 명령을 보냅니다.
- 규칙 및 작업을 관리합니다.

미리 구성된 솔루션에서 솔루션 포털은 **IoT 솔루션 백 엔드**의 일부와 일반적인 [IoT 솔루션 아키텍처][lnk-what-is-azure-iot]에서 **프로세스 및 비즈니스 연결**의 일부를 형성합니다.

## 다음 단계

IoT 솔루션 아키텍처에 대한 자세한 내용은 [Microsoft Azure IoT 서비스: 참조 아키텍처][lnk-refarch]를 참조하세요.

이제 미리 구성된 솔루션을 알 수 있으므로 미리 구성된 *원격 모니터링* 솔루션: [미리 구성된 솔루션 시작][lnk-getstarted-preconfigured]을 배포하여 시작할 수 있습니다.

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md

<!---HONumber=AcomDC_1005_2016-->