---
title: "연결된 공장 Azure IoT Suite 솔루션 연습 | Microsoft Docs"
description: "공장 및 해당 아키텍처에 연결된 Azure IoT 미리 구성된 솔루션에 대한 설명입니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 3011fd608ba83561c319e57c8a7b5a4f3c4c2284
ms.contentlocale: ko-kr
ms.lasthandoff: 05/26/2017


---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>연결된 공장 미리 구성된 솔루션 연습

IoT Suite 연결된 공장 [미리 구성된 솔루션][lnk-preconfigured-solutions]은 종단 간 산업 솔루션의 구현입니다.

* 시뮬레이션된 공장 생산 라인에서 OPC UA 서버를 실행하는 시뮬레이션된 산업 장치 및 실제 OPC UA 서버 장치에 연결합니다. OPC UA에 대한 자세한 내용은 [FAQ][lnk-faq]를 참조하세요.
* 이러한 장치 및 생산 라인의 운영 KPI 및 OEE를 보여 줍니다.
* OPC UA 서버 시스템과 상호 작용하는 데 클라우드 기반 응용 프로그램을 사용하는 방법을 보여 줍니다.
* 고유한 OPC UA 서버 장치를 연결할 수 있습니다.
* OPC UA 서버 데이터를 검색하고 수정할 수 있습니다.
* Azure TSI(Time Series Insights) 서비스와 통합하여 OPC UA 서버에서 데이터의 사용자 지정된 보기를 제공합니다.

솔루션을 고유한 구현을 위한 출발점으로 사용하고 사용자의 특정 비즈니스 요구 사항을 충족하도록 [사용자 지정][lnk-customize]할 수 있습니다.

이 문서는 작동 방식을 이해할 수 있도록 연결된 공장 솔루션의 핵심 요소 중 일부를 안내합니다. 이 정보는 다음 항목을 도울 수 있습니다.

* 솔루션의 문제를 해결합니다.
* 솔루션을 사용자 지정하여 고유한 특정 요구 사항을 충족하는 방법을 계획합니다.
* Azure 서비스를 사용하는 고유한 IoT 솔루션을 디자인합니다.

## <a name="logical-architecture"></a>논리 아키텍처

다음 다이어그램에서는 미리 구성된 솔루션의 논리적 구성 요소를 간략히 보여줍니다.

![연결된 공장 논리 아키텍처][connected-factory-logical]

## <a name="simulation"></a>시뮬레이션

시뮬레이션된 스테이션과 시뮬레이션된 MES(제조 실행 시스템)는 공장 생산 라인을 구성합니다. 시뮬레이션된 장치 및 OPC 게시자 모듈은 OPC Foundation에서 게시한 [OPC UA .NET 표준][lnk-OPC-UA-NET-Standard]을 기반으로 합니다.

OPC 프록시 및 OPC 게시자는 [Azure IoT Edge][lnk-Azure-IoT-Gateway]를 기반으로 하는 모듈로 구현됩니다. 각 시뮬레이션된 생산 라인에는 지정된 게이트웨이가 연결되었습니다.

모든 시뮬레이션 구성 요소는 Azure Linux VM에서 호스팅되는 Docker 컨테이너에서 실행됩니다. 시뮬레이션은 기본적으로 8개의 시뮬레이션된 생산 라인을 실행하도록 구성됩니다.

## <a name="simulated-production-line"></a>시뮬레이션된 생산 라인

생산 라인 제조 파트. 어셈블리 스테이션, 테스트 스테이션 및 패키징 스테이션과 같은 다른 스테이션으로 구성됩니다.

시뮬레이션은 OPC UA 노드를 통해 노출되는 데이터를 실행 및 업데이트합니다. 모든 시뮬레이션된 생산 라인 스테이션은 OPC UA를 통해 MES에 의해 오케스트레이션됩니다.

## <a name="simulated-manufacturing-execution-system"></a>시뮬레이션된 제조 실행 시스템

MES는 스테이션 상태 변경 내용을 검색하는 OPC UA를 통해 생산 라인의 각 스테이션을 모니터링합니다. 스테이션을 제어하는 OPC UA 메서드를 호출하고 완료될 때까지 한 스테이션에서 다음으로 제품을 전달합니다.

## <a name="gateway-opc-publisher-module"></a>게이트웨이 OPC 게시자 모듈

OPC 게시자 모듈은 스테이션 OPC UA 서버에 연결하고 게시될 OPC 노드를 구독합니다. 모듈은 노드 데이터를 JSON 형식으로 변환 및 암호화하고 OPC UA Pub/Sub 메시지로 IoT Hub에 보냅니다.

OPC 게시자 모듈에는 아웃바운드 https 포트(443)만 필요하며 기존 엔터프라이즈 인프라로 작업할 수 있습니다.

## <a name="gateway-opc-proxy-module"></a>게이트웨이 OPC 프록시 모듈

게이트웨이 OPC UA 프록시 모듈은 이진 OPC UA 명령 및 제어 메시지를 터널링하고 아웃바운드 https 포트(443)만을 필요로 합니다. 웹 프록시를 포함하여 기존 엔터프라이즈 인프라로 작업할 수 있습니다.

IoT Hub 장치 메서드를 사용하여 응용 프로그램 계층에서 패킷으로 나누어진 TCP/IP 데이터를 전송하므로 끝점 신뢰, 데이터 암호화 및 SSL/TLS를 사용하여 무결성을 보장합니다.

프록시 자체를 통해 릴레이된 OPC UA 이진 프로토콜은 UA 인증 및 암호화를 사용합니다.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

게이트웨이 OPC 게시자 모듈은 OPC UA 서버 노드를 구독하여 데이터 값에서 변경을 감지합니다. 노드 중 하나에서 데이터 변경이 감지되면 이 모듈은 Azure IoT Hub로 메시지를 보냅니다.

IoT Hub는 Azure TSI에 이벤트 원본을 제공합니다. TSI는 메시지에 연결된 타임스탬프에 따라 30일 동안 데이터를 저장합니다. 이 데이터에는 다음이 포함됩니다.

* OPC UA ApplicationUri
* OPC UA NodeId
* 노드 값
* 원본 타임스탬프
* OPC UA DisplayName

현재 TSI는 데이터를 유지하려는 기간에 대한 고객의 사용자 지정을 허용하지 않습니다.

TSI는 SearchSpan(Time.From, Time.To)을 사용하여 노드 데이터에 대해 쿼리하고 OPC UA ApplicationUri 또는 OPC UA NodeId 또는 OPC UA DisplayName으로 집계합니다.

OEE 및 KPI 계기 및 시간열 차트에 대한 데이터를 검색하기 위해 데이터는 이벤트, Sum, Avg, Min 및 Max의 수로 집계됩니다.

시계열은 다른 프로세스를 사용하여 빌드됩니다. OEE 및 KPI는 스테이션 기본 데이터에서 계산되며 응용 프로그램에서 토폴로지(생산 라인, 공장, 엔터프라이즈)에 대해 버블링됩니다.

또한 OEE 및 KPI 토폴로지에 대한 시계열은 표시된 timespan이 준비될 때마다 앱에서 계산됩니다. 예를 들어 일 보기는 1시간마다 업데이트됩니다.

노드 데이터의 시계열 보기는 timespan에 대한 집계를 사용하여 TSI에서 직접 제공됩니다.

## <a name="iot-hub"></a>IoT 허브
[IoT Hub][lnk-IoT Hub]는 OPC 게시자 모듈에서 클라우드로 전송된 데이터를 수신하고 Azure TSI 서비스에 사용할 수 있도록 합니다. 

또한 솔루션에서 IoT Hub는:
- 모든 OPC 게시자 모듈 및 모든 OPC 프록시 모듈에 대한 ID를 저장하는 ID 레지스트리를 유지 관리합니다.
- OPC 프록시 모듈의 양방향 통신에 대한 전송 채널로 사용됩니다.

## <a name="azure-storage"></a>Azure 저장소
솔루션은 VM에 대 한 디스크 저장소로 Azure Blob 저장소를 사용하여 배포 데이터를 저장합니다.

## <a name="web-app"></a>웹앱
미리 구성된 솔루션의 일부로 배포된 웹앱은 통합된 OPC UA 클라이언트, 경고 처리 및 원격 분석 시각화로 이루어집니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하여 IoT Suite 시작 작업을 계속할 수 있습니다.

* [azureiotsuite.com 사이트에 대한 사용 권한][lnk-permissions]

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
