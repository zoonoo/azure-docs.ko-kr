---
title: Azure Cosmos DB .NET SDK를 사용하는 경우 문제 진단 및 해결
description: 클라이언트 쪽 로깅 및 기타 타사 도구와 같은 기능을 사용 하 여 .NET SDK를 사용 하는 경우 Azure Cosmos DB 문제를 식별, 진단 및 해결 합니다.
author: j82w
ms.service: cosmos-db
ms.date: 03/11/2020
ms.author: jawilley
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: e015c1ee335cbdfed7964d63b1f4600bc6a4cb77
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82208740"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>Azure Cosmos DB .NET SDK를 사용하는 경우 문제 진단 및 해결
이 문서에서는 [.NET SDK](sql-api-sdk-dotnet.md) 를 사용 하 여 SQL API 계정을 Azure Cosmos DB 하는 일반적인 문제, 해결 방법, 진단 단계 및 도구에 대해 설명 합니다.
.NET SDK는 Azure Cosmos DB SQL API에 액세스 하는 클라이언트 쪽 논리적 표현을 제공 합니다. 이 문서에서는 문제가 발생하는 경우 사용자에게 도움이 되는 도구 및 방법을 설명합니다.

## <a name="checklist-for-troubleshooting-issues"></a>문제 해결을 위한 검사 목록
응용 프로그램을 프로덕션 환경으로 이동 하기 전에 다음 검사 목록을 고려 하십시오. 검사 목록을 사용 하면 표시 되는 몇 가지 일반적인 문제를 방지할 수 있습니다. 문제가 발생 하는 경우 신속 하 게 진단할 수도 있습니다.

*    최신 [SDK](sql-api-sdk-dotnet-standard.md)를 사용 합니다. Preview Sdk는 프로덕션 환경에서 사용 하면 안 됩니다. 이렇게 하면 이미 수정 된 알려진 문제를 해결할 수 있습니다.
*    [성능 팁](performance-tips.md)을 검토하고 제안된 사례를 따릅니다. 이렇게 하면 크기 조정, 대기 시간 및 기타 성능 문제를 방지할 수 있습니다.
*    문제를 해결 하는 데 도움이 되도록 SDK 로깅을 사용 하도록 설정 합니다. 로깅을 사용 하도록 설정 하면 성능에 영향을 줄 수 있으므로 문제를 해결할 때만 사용 하도록 설정 하는 것이 좋습니다. 다음 로그를 사용 하도록 설정할 수 있습니다.
    *    Azure Portal를 사용 하 여 [메트릭을 기록](monitor-accounts.md) 합니다. 포털 메트릭은 Azure Cosmos DB 원격 분석을 표시 합니다 .이는 문제가 Azure Cosmos DB에 해당 하는지 또는 클라이언트 쪽의 것인지를 확인 하는 데 도움이 됩니다.
    *    V2 SDK 또는 V3 SDK의 [진단](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics) 에서 시점 작업 응답의 [진단 문자열](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring) 을 기록 합니다.
    *    모든 쿼리 응답에서 [SQL 쿼리 메트릭](sql-api-query-metrics.md) 기록 
    *    [SDK 로깅]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md) 설정 따르기

이 문서의 [일반적인 문제 및 해결 방법](#common-issues-workarounds) 섹션을 살펴봅니다.

적극적으로 모니터링 되는 [GitHub 문제 섹션](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) 을 확인 합니다. 해결 방법이 있는 유사한 문제가 이미 제출되었는지 확인합니다. 솔루션을 찾지 못한 경우 GitHub 문제를 파일에 표시 합니다. 긴급 한 문제에 대 한 지원 틱을 열 수 있습니다.


## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>일반적인 이슈 및 해결 방법

### <a name="general-suggestions"></a>일반 제안
* 가능 하면 Azure Cosmos DB 계정과 동일한 Azure 지역에서 앱을 실행 합니다. 
* 클라이언트 컴퓨터의 리소스가 부족 하 여 연결/가용성 문제가 발생할 수 있습니다. Azure Cosmos DB 클라이언트를 실행 하는 노드에서 CPU 사용률을 모니터링 하 고 높은 부하 상태에서 실행 중인 경우 확장/축소 하는 것이 좋습니다.

### <a name="check-the-portal-metrics"></a>포털 메트릭 확인
[포털 메트릭을](monitor-accounts.md) 확인 하면 클라이언트 쪽 문제 인지 또는 서비스에 문제가 있는지 확인 하는 데 도움이 됩니다. 예를 들어, 메트릭에 처리율이 제한 된 요청 (HTTP 상태 코드 429)이 포함 된 경우 요청을 제한 하는 것을 의미 하는 요청 [빈도 너무 큼] 섹션을 확인 합니다. 

### <a name="requests-timeouts"></a><a name="request-timeouts"></a>요청 시간 제한
RequestTimeout은 일반적으로 Direct/TCP를 사용할 때 발생 하지만 게이트웨이 모드에서 발생할 수 있습니다. 이러한 오류는 일반적으로 알려진 원인 및 문제 해결 방법에 대 한 제안입니다.

* CPU 사용률이 높으면 대기 시간 및/또는 요청 시간 제한이 발생 합니다. 고객은 호스트 컴퓨터를 확장 하 여 더 많은 리소스를 제공 하거나 부하가 더 많은 컴퓨터에 분산 될 수 있습니다.
* 소켓/포트 가용성이 낮을 수 있습니다. Azure에서 실행 하는 경우 .NET SDK를 사용 하는 클라이언트는 Azure SNAT (PAT) 포트 고갈에 도달할 수 있습니다. 이 문제가 발생할 가능성을 줄이려면 .NET SDK의 최신 버전인 2.x 또는 3.x를 사용 합니다. 이 예는 항상 최신 SDK 버전을 실행 하는 것이 좋습니다.
* 여러 DocumentClient 인스턴스를 만들면 연결 경합 및 시간 제한 문제가 발생할 수 있습니다. [성능 팁](performance-tips.md)을 따르고 전체 프로세스에서 단일 documentclient 인스턴스를 사용 합니다.
* 사용자에 게는 컬렉션이 프로 비전 되 고 백 엔드 제한 요청이 발생 하 고 클라이언트는 내부적으로 재시도 하기 때문에 불충분 대기 시간 또는 요청 시간 제한이 표시 될 수도 있습니다. [포털 메트릭을](monitor-accounts.md)확인 합니다.
* Azure Cosmos DB는 전체 프로 비전 된 처리량을 실제 파티션에 균등 하 게 분산 합니다. 포털 메트릭을 확인 하 여 워크 로드에서 핫 [파티션 키](partition-data.md)가 발생 하는지 확인 합니다. 이로 인해 집계 된 처리량 (r u/초)이 프로 비전 된 RUs 아래에 있는 것 처럼 보이지만 처리량을 사용 하는 단일 파티션 (r u/초)이 프로 비전 된 처리량을 초과 하 게 됩니다. 
* 또한 2.0 SDK는 직접/TCP 연결에 채널 의미 체계를 추가 합니다. 한 번에 하나의 TCP 연결이 여러 요청에 사용 됩니다. 이 경우 다음과 같은 두 가지 문제가 발생할 수 있습니다.
    * 동시성 수준이 높으면 채널 경합이 발생할 수 있습니다.
    * 대량 요청이 나 응답은 비교적 낮은 동시성을 사용 하는 경우에도 채널 및 악화 경합의 줄이 차단 될 수 있습니다.
    * 사례가 이러한 두 범주 중 하나에 해당 하는 경우 (또는 높은 CPU 사용률이 의심 되는 경우) 다음과 같은 완화 방법을 사용할 수 있습니다.
        * 응용 프로그램의 크기를 조정 합니다.
        * 또한 자세한 정보를 얻기 위해 [추적 수신기](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) 를 통해 SDK 로그를 캡처할 수 있습니다.

### <a name="high-network-latency"></a><a name="high-network-latency"></a>높은 네트워크 대기 시간
높은 네트워크 대기 시간은 V2 SDK의 [진단 문자열](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) 또는 V3 sdk의 [진단](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.responsemessage.diagnostics?view=azure-dotnet#Microsoft_Azure_Cosmos_ResponseMessage_Diagnostics) 을 사용 하 여 식별할 수 있습니다.

[시간 초과가](#request-timeouts) 없고 진단에 단일 요청이 표시 되는 경우 (예:이 예에서는 >300 밀리초) `ResponseTime` 와 `RequestStartTime`같이 대기 시간이 긴 단일 요청을 표시 합니다.

```bash
RequestStartTime: 2020-03-09T22:44:49.5373624Z, RequestEndTime: 2020-03-09T22:44:49.9279906Z,  Number of regions attempted:1
ResponseTime: 2020-03-09T22:44:49.9279906Z, StoreResult: StorePhysicalAddress: rntbd://..., ...
```

이 대기 시간에는 여러 원인이 있을 수 있습니다.

* 응용 프로그램이 Azure Cosmos DB 계정과 동일한 지역에서 실행 되 고 있지 않습니다.
* [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 또는 [applicationregion](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 구성이 잘못 되어 응용 프로그램이 현재 실행 중인 다른 지역에 연결 하려고 합니다.
* 트래픽이 많아 네트워크 인터페이스에서 병목 상태가 발생할 수 있습니다. 응용 프로그램이 Azure Virtual Machines에서 실행 되는 경우 가능한 해결 방법이 있습니다.
    * [가속화 된 네트워킹을](../virtual-network/create-vm-accelerated-networking-powershell.md)사용 하는 가상 컴퓨터를 사용 하는 것이 좋습니다.
    * [기존 가상 머신에서 가속화 된 네트워킹을](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms)사용 하도록 설정 합니다.
    * [더 높은 최종 가상 컴퓨터](../virtual-machines/windows/sizes.md)를 사용 하는 것이 좋습니다.

### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT(PAT) 포트 고갈

앱이 [공용 IP 주소 없이 azure Virtual Machines](../load-balancer/load-balancer-outbound-connections.md#defaultsnat)에 배포 되는 경우 기본적으로 [azure SNAT 포트](../load-balancer/load-balancer-outbound-connections.md#preallocatedports) 는 VM 외부의 끝점에 대 한 연결을 설정 합니다. VM에서 Azure Cosmos DB 엔드포인트로 허용되는 연결 수는 [Azure SNAT 구성](../load-balancer/load-balancer-outbound-connections.md#preallocatedports)으로 제한됩니다. 이 경우 연결 제한, 연결 종료 또는 위에 언급 된 [요청 시간 초과가](#request-timeouts)발생할 수 있습니다.

 Azure SNAT 포트는 VM에서 개인 IP 주소를 공용 IP 주소에 연결 하는 경우에만 사용 됩니다. Azure SNAT 제한을 방지 하기 위한 두 가지 해결 방법이 있습니다 (전체 응용 프로그램에서 단일 클라이언트 인스턴스를 이미 사용 하 고 있는 경우).

* Azure Virtual Machines 가상 네트워크의 서브넷에 Azure Cosmos DB 서비스 엔드포인트를 추가합니다. 자세한 내용은 [Azure Virtual Network 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요. 

    서비스 엔드포인트를 사용하도록 설정한 경우 요청이 더 이상 공용 IP에서 Azure Cosmos DB로 전송되지 않습니다. 대신 가상 네트워크 및 서브넷 ID가 전송됩니다. 공용 IP만 허용되는 경우 이 변경 내용으로 인해 방화벽이 삭제될 수 있습니다. 방화벽을 사용하는 경우 서비스 엔드포인트를 사용하도록 설정하면 [Virtual Network ACL](../virtual-network/virtual-networks-acl.md)을 사용하여 방화벽에 서브넷을 추가합니다.
* [AZURE VM에 공용 IP](../load-balancer/load-balancer-outbound-connections.md#assignilpip)를 할당 합니다.

### <a name="http-proxy"></a>HTTP 프록시
HTTP 프록시를 사용하는 경우 SDK `ConnectionPolicy`에서 구성된 연결 수를 지원할 수 있는지 확인합니다.
그렇지 않으면 연결 문제가 발생할 수 있습니다.

### <a name="request-rate-too-large"></a><a name="request-rate-too-large"></a>너무 많은 요청 빈도
' 요청 속도 너무 큼 ' 또는 오류 코드 429은 사용 된 처리량 (r u/초)이 [프로 비전 된 처리량](set-throughput.md)을 초과 하 여 요청을 제한 하 고 있음을 나타냅니다. SDK는 지정 된 [재시도 정책](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)에 따라 요청을 자동으로 다시 시도 합니다. 이 오류가 자주 발생 하는 경우 컬렉션에 대 한 처리량을 높이는 것이 좋습니다. 429 오류를 받고 있는지 확인 하려면 [포털의 메트릭을](use-metrics.md) 확인 하세요. [파티션 키](partitioning-overview.md#choose-partitionkey) 를 검토 하 여 저장소 및 요청 볼륨의 균등 한 배포가 발생 하는지 확인 합니다. 

### <a name="slow-query-performance"></a>쿼리 성능 저하
쿼리 [메트릭은](sql-api-query-metrics.md) 쿼리가 가장 많은 시간을 소비 하는 위치를 확인 하는 데 도움이 됩니다. 쿼리 메트릭에 따라 백 엔드 및 클라이언트에서 얼마나 많은 시간이 소요 되는지 확인할 수 있습니다.
* 백 엔드 쿼리가 신속 하 게 반환 하 고 클라이언트에서 많은 시간을 소비 하는 경우 컴퓨터의 부하를 확인 합니다. 리소스가 부족 하 여 SDK에서 응답을 처리 하는 데 리소스를 사용할 수 있을 때까지 대기 하 고 있을 수 있습니다.
* 백 엔드 쿼리가 느려지는 경우 [쿼리를 최적화](optimize-cost-queries.md) 하 고 현재 [인덱싱 정책을](index-overview.md) 확인 하십시오. 

### <a name="http-401-the-mac-signature-found-in-the-http-request-is-not-the-same-as-the-computed-signature"></a>HTTP 401: HTTP 요청에서 찾은 MAC 서명이 계산 된 시그니처와 동일 하지 않습니다.
다음 401 오류 메시지가 표시 되 면 "HTTP 요청에서 찾은 MAC 서명이 계산 된 시그니처와 동일 하지 않습니다" 라는 오류 메시지가 표시 됩니다. 이 문제는 다음과 같은 경우에 발생할 수 있습니다.

1. 키가 회전 되었으며 [모범 사례](secure-access-to-data.md#key-rotation)를 따르지 않았습니다. 일반적으로이 경우에 해당 합니다. 계정 키 회전 Cosmos DB Cosmos DB 계정 크기에 따라 몇 초에서 며칠까지 걸릴 수 있습니다.
   1. 401 MAC 서명은 키 회전 직후에 표시 되며 결국에는 변경 하지 않고 중지 됩니다. 
2. 키가 계정과 일치 하지 않도록 응용 프로그램에서 키가 잘못 구성 되었습니다.
   1. 401 MAC 서명 문제가 일관적이 고 모든 호출에 대해 발생 합니다.
3. 컨테이너를 만드는 경합 조건이 있습니다. 컨테이너 만들기가 완료 되기 전에 응용 프로그램 인스턴스가 컨테이너에 액세스 하려고 합니다. 응용 프로그램을 실행 하는 경우이에 대 한 가장 일반적인 시나리오 이며, 응용 프로그램이 실행 되는 동안 컨테이너를 삭제 하 고 같은 이름으로 다시 만듭니다. SDK는 새 컨테이너를 사용 하려고 하지만 컨테이너 만들기는 아직 진행 중 이므로 키를 포함 하지 않습니다.
   1. 401 MAC 서명 문제는 컨테이너를 만든 직후에 표시 되며 컨테이너 만들기가 완료 될 때 까지만 발생 합니다.
 
 ### <a name="http-error-400-the-size-of-the-request-headers-is-too-long"></a>HTTP 오류 400. 요청 헤더의 크기가 너무 깁니다.
 헤더 크기가 크게 커지고 최대 허용 크기를 초과 합니다. 항상 최신 SDK를 사용 하는 것이 좋습니다. 예외 메시지에 헤더 크기 추적을 추가 하는 버전 [3(sp3)](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/changelog.md) 또는 2.x를 사용 해야 [합니다.](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)

컨트롤이
 1. 세션 토큰이 너무 크게 증가 했습니다. 세션 토큰은 컨테이너의 파티션 수가 증가 함에 따라 증가 합니다.
 2. 연속 토큰이 크게 증가 했습니다. 쿼리가 서로 다른 연속 토큰 크기를 갖습니다.
 3. 세션 토큰과 연속 토큰의 조합으로 인해 발생 합니다.

해결 방법:
   1. [성능 팁](performance-tips.md) 을 따르고 응용 프로그램을 직접 + TCP 연결 모드로 변환 합니다. Direct + TCP에는이 문제를 방지 하는 HTTP와 같은 헤더 크기 제한이 없습니다.
   2. 세션 토큰이 원인인 경우에는 응용 프로그램을 다시 시작 하는 것이 일시적입니다. 응용 프로그램 인스턴스를 다시 시작 하면 세션 토큰이 다시 설정 됩니다. 다시 시작한 후에도 예외가 중지 되 면 세션 토큰이 원인 임을 확인 합니다. 결국에는 예외를 발생 시키는 크기로 다시 증가 하 게 됩니다.
   3. 응용 프로그램을 Direct + TCP로 변환할 수 없고 세션 토큰이 원인인 경우 클라이언트 [일관성 수준을](consistency-levels.md)변경 하 여 완화 작업을 수행할 수 있습니다. 세션 토큰은 Cosmos DB의 기본값인 세션 일관성에만 사용 됩니다. 다른 모든 일관성 수준은 세션 토큰을 사용 하지 않습니다. 
   4. 응용 프로그램을 Direct + TCP로 변환할 수 없고 연속 토큰이 원인인 경우에는 ResponseContinuationTokenLimitInKb 옵션을 설정 해 보십시오. 옵션은 v 2의 경우 FeedOptions 또는 v3의 QueryRequestOptions에서 찾을 수 있습니다.

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[너무 많은 요청 빈도]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT (PAT) port exhaustion]: #snat
[Production check list]: #production-check-list
