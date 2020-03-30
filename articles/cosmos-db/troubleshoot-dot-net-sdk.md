---
title: Azure Cosmos DB .NET SDK를 사용하는 경우 문제 진단 및 해결
description: .NET SDK를 사용할 때 클라이언트 측 로깅 및 기타 타사 도구와 같은 기능을 사용하여 Azure Cosmos DB 문제를 식별, 진단 및 해결합니다.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5f92d98630c6fb875babeb907f92732b0c24bb52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137957"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK를 사용하는 경우 문제 진단 및 해결
이 문서에서는 Azure Cosmos DB SQL API 계정과 함께 [.NET SDK를](sql-api-sdk-dotnet.md) 사용할 때의 일반적인 문제, 해결 방법, 진단 단계 및 도구를 다룹니다.
.NET SDK는 Azure Cosmos DB SQL API에 액세스하기 위해 클라이언트 측 논리 표현을 제공합니다. 이 문서에서는 문제가 발생하는 경우 사용자에게 도움이 되는 도구 및 방법을 설명합니다.

## <a name="checklist-for-troubleshooting-issues"></a>문제 해결에 대한 체크리스트:
응용 프로그램을 프로덕션으로 이동하기 전에 다음 검사 목록을 고려하십시오. 검사 목록을 사용하면 다음과 같은 몇 가지 일반적인 문제가 발생할 수 있습니다. 문제가 발생할 때 신속하게 진단할 수도 있습니다.

*    최신 [SDK를](sql-api-sdk-dotnet-standard.md)사용합니다. 미리 보기 SDK를 프로덕션에 사용해서는 안 됩니다. 이렇게 하면 이미 수정된 알려진 문제가 발생하지 않습니다.
*    [성능 팁](performance-tips.md)을 검토하고 제안된 사례를 따릅니다. 이렇게 하면 크기 조정, 대기 시간 및 기타 성능 문제를 방지할 수 있습니다.
*    문제를 해결하는 데 도움이 되는 SDK 로깅을 사용하도록 설정합니다. 로깅을 사용하도록 설정하면 성능에 영향을 줄 수 있으므로 문제 해결 시에만 로깅을 사용하도록 설정하는 것이 가장 좋습니다. 다음 로그를 활성화할 수 있습니다.
    *    Azure 포털을 사용하여 [메트릭을 기록합니다.](monitor-accounts.md) 포털 메트릭에는 Azure Cosmos DB 원격 분석이 표시되며, 이 원격 측정은 문제가 Azure Cosmos DB에 해당하는지 또는 클라이언트 측에서 제공하는 것인지 확인하는 데 유용합니다.
    *    V2 SDK에 [진단 문자열을](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) 기록하거나 지점 작업 응답에서 V3 SDK의 [진단을](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) 기록합니다.
    *    모든 [쿼리](sql-api-query-metrics.md) 응답에서 SQL 쿼리 메트릭 로그 
    *    [SDK 로깅]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) 설정 수행

이 문서의 [일반적인 문제 및 해결 방법](#common-issues-workarounds) 섹션을 살펴봅니다.

적극적으로 모니터링되는 [GitHub 문제 섹션을](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) 확인합니다. 해결 방법이 있는 유사한 문제가 이미 제출되었는지 확인합니다. 해결책을 찾지 못한 경우 GitHub 문제를 제기하십시오. 긴급 한 문제에 대 한 지원 틱을 열 수 있습니다.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>일반적인 이슈 및 해결 방법

### <a name="general-suggestions"></a>일반 제안
* 가능하면 Azure Cosmos DB 계정과 동일한 Azure 리전에서 앱을 실행합니다. 
* 클라이언트 컴퓨터에 리소스가 부족하여 연결/가용성 문제가 발생할 수 있습니다. Azure Cosmos DB 클라이언트를 실행하는 노드에서 CPU 사용률을 모니터링하고 높은 부하에서 실행되는 경우 확장/축소하는 것이 좋습니다.

### <a name="check-the-portal-metrics"></a>포털 메트릭 확인
포털 [메트릭을](monitor-accounts.md) 확인하면 클라이언트 측 문제인지 또는 서비스에 문제가 있는지 확인하는 데 도움이 됩니다. 예를 들어 메트릭에 요금이 제한되는 요청(HTTP 상태 코드 429)이 높은 비율을 포함하는 경우 요청이 제한되고 있다는 것을 의미한 다음 [요청 비율이 너무 크다는] 것을 확인합니다. 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>시간 시간 시간 요청
요청 시간 삭제는 일반적으로 Direct/TCP를 사용할 때 발생하지만 게이트웨이 모드에서 발생할 수 있습니다. 이러한 오류는 일반적으로 알려진 원인이며 문제를 해결하는 방법에 대한 제안입니다.

* CPU 사용률이 높으면 대기 시간 및/또는 요청 시간 시간이 발생합니다. 고객은 호스트 컴퓨터를 확장하여 더 많은 리소스를 제공하거나 더 많은 컴퓨터에 부하를 분산시킬 수 있습니다.
* 소켓 / 포트 가용성이 낮을 수 있습니다. Azure에서 실행할 때 .NET SDK를 사용하는 클라이언트는 PAT(Azure SNAT) 포트 소모를 기록할 수 있습니다. 이 문제를 발생시킬 가능성을 줄이려면 .NET SDK의 최신 버전 2.x 또는 3.x를 사용합니다. 이는 항상 최신 SDK 버전을 실행하는 것이 권장되는 이유의 예입니다.
* 여러 DocumentClient 인스턴스를 만들면 연결 경합 및 시간 시간 시간 문제가 발생할 수 있습니다. 성능 [팁을](performance-tips.md)따르고 전체 프로세스에서 단일 DocumentClient 인스턴스를 사용합니다.
* 컬렉션이 불충분하게 프로비저닝되고 백 엔드 스로틀 요청이 요청되고 클라이언트가 내부적으로 다시 시도하기 때문에 사용자는 대기 시간이 길어지거나 시간 제한을 요청하는 경우가 있습니다. 포털 [메트릭을 확인합니다.](monitor-accounts.md)
* Azure Cosmos DB는 전체 프로비저닝된 처리량을 실제 파티션에 균등하게 분배합니다. 포털 메트릭을 확인하여 워크로드에 핫 [파티션 키가](partition-data.md)발생하는지 확인합니다. 이렇게 하면 사용된 집계 처리량(RU/s)이 프로비저닝된 RU 아래에 있는 것처럼 보이지만, 단일 파티션 사용 처리량(RU/s)이 프로비저닝된 처리량을 초과합니다. 
* 또한 2.0 SDK는 직접/TCP 연결에 채널 의미 체계를 추가합니다. 하나의 TCP 연결이 동시에 여러 요청에 사용됩니다. 이로 인해 특정 사례에서 두 가지 문제가 발생할 수 있습니다.
    * 높은 수준의 동시성은 채널에서 경합으로 이어질 수 있습니다.
    * 요청이나 응답이 크면 채널의 헤드 라인 차단으로 이어질 수 있으며 상대적으로 낮은 수준의 동시성으로도 경합이 악화될 수 있습니다.
    * 이러한 두 가지 범주 중 어느 한 가지 범주에 속하는 경우(또는 높은 CPU 사용률이 의심되는 경우) 다음과 같은 완화가 가능합니다.
        * 응용 프로그램을 확장/축소해 보십시오.
        * 또한 [추적 수신기를](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) 통해 SDK 로그를 캡처하여 자세한 내용을 얻을 수 있습니다.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>높은 네트워크 대기 시간
높은 네트워크 대기 시간은 V2 SDK의 [진단 문자열](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) 또는 V3 SDK의 진단을 사용하여 식별할 수 [있습니다.](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics)

시간 [시간이](#request-timeouts) 없고 진단에 높은 대기 시간이 다음과 같은 차이에서 `ResponseTime` `RequestStartTime`분명한 단일 요청이 표시되면 (이 예제에서는 >300밀리초).

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

이 대기 시간에는 다음과 같은 여러 원인이 있을 수 있습니다.

* 응용 프로그램이 Azure Cosmos DB 계정과 동일한 지역에서 실행되고 있지 않습니다.
* [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 또는 [ApplicationRegion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 구성이 올바르지 않으며 응용 프로그램이 현재 실행 중인 다른 지역에 연결하려고 합니다.
* 트래픽이 많기 때문에 네트워크 인터페이스에 병목 현상이 있을 수 있습니다. 응용 프로그램이 Azure 가상 컴퓨터에서 실행 중인 경우 다음과 같은 해결 방법이 있습니다.
    * [가속 네트워킹이 활성화된 가상 컴퓨터를](../virtual-network/create-vm-accelerated-networking-powershell.md)사용하는 것이 좋습니다.
    * [기존 가상 컴퓨터에서 가속 네트워킹을](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)활성화합니다.
    * [고급 가상 컴퓨터를](../virtual-machines/windows/sizes.md)사용하는 것이 좋습니다.

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT(PAT) 포트 고갈

[공용 IP 주소가 없는 Azure 가상 머신에](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)앱이 배포되는 경우 기본적으로 [Azure SNAT 포트는](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) VM 외부의 모든 끝점에 대한 연결을 설정합니다. VM에서 Azure Cosmos DB 엔드포인트로 허용되는 연결 수는 [Azure SNAT 구성](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)으로 제한됩니다. 이 상황으로 인해 연결 제한, 연결 종료 또는 위에서 언급한 [요청 시간 제한으로](#request-timeouts)이어질 수 있습니다.

 Azure SNAT 포트는 VM에 개인 IP 주소가 공용 IP 주소에 연결되어 있는 경우에만 사용됩니다. Azure SNAT 제한을 피하기 위한 두 가지 해결 방법이 있습니다(이미 전체 응용 프로그램에서 단일 클라이언트 인스턴스를 사용하고 있는 경우).

* Azure Virtual Machines 가상 네트워크의 서브넷에 Azure Cosmos DB 서비스 엔드포인트를 추가합니다. 자세한 내용은 [Azure Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요. 

    서비스 엔드포인트를 사용하도록 설정한 경우 요청이 더 이상 공용 IP에서 Azure Cosmos DB로 전송되지 않습니다. 대신 가상 네트워크 및 서브넷 ID가 전송됩니다. 공용 IP만 허용되는 경우 이 변경 내용으로 인해 방화벽이 삭제될 수 있습니다. 방화벽을 사용하는 경우 서비스 엔드포인트를 사용하도록 설정하면 [Virtual Network ACL](../virtual-network/virtual-networks-acl.md)을 사용하여 방화벽에 서브넷을 추가합니다.
* Azure VM 에 [공용 IP를 할당합니다.](../load-balancer/load-balancer-outbound-connections.md#assignilpip)

### <a name="http-proxy"></a>HTTP 프록시
HTTP 프록시를 사용하는 경우 SDK `ConnectionPolicy`에서 구성된 연결 수를 지원할 수 있는지 확인합니다.
그렇지 않으면 연결 문제가 발생할 수 있습니다.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>너무 많은 요청 빈도
'요청 속도가 너무 큽니다' 또는 오류 코드 429는 사용된 처리량(RU/s)이 [프로비저닝된 처리량을](set-throughput.md)초과했기 때문에 요청이 제한되고 있음을 나타냅니다. SDK는 지정된 [다시 시도 정책에](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)따라 요청을 자동으로 다시 시도합니다. 이 오류가 자주 발생하면 컬렉션의 처리량을 늘리는 것이 좋습니다. [포털의 메트릭을](use-metrics.md) 확인하여 429개의 오류가 있는지 확인합니다. [파티션 키를](partitioning-overview.md#choose-partitionkey) 검토하여 저장소 및 요청 볼륨을 균등하게 분배할 수 있도록 합니다. 

### <a name="slow-query-performance"></a>쿼리 성능 저하
[쿼리 메트릭은 쿼리가](sql-api-query-metrics.md) 대부분의 시간을 소비하는 위치를 결정하는 데 도움이 됩니다. 쿼리 메트릭에서 백 엔드와 클라이언트에 얼마나 많이 소비되는지 확인할 수 있습니다.
* 백 엔드 쿼리가 빠르게 반환되고 클라이언트에서 많은 시간을 소비하는 경우 컴퓨터의 부하를 확인합니다. 리소스가 충분하지 않고 SDK에서 응답을 처리할 수 있는 리소스를 기다리고 있을 수 있습니다.
* 백 엔드 쿼리가 느린 경우 [쿼리를 최적화하고](optimize-cost-queries.md) 현재 [인덱싱 정책을](index-overview.md) 살펴보십시오. 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[너무 많은 요청 빈도]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list


