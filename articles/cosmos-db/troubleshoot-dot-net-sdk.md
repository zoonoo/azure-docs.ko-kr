---
title: 진단 및 Azure Cosmos DB.NET SDK를 사용 하는 경우 문제 해결
description: 클라이언트 쪽 로깅 및 식별, 진단 및.NET SDK를 사용 하는 경우 Azure Cosmos DB 문제를 해결 하려면 다른 타사 도구 등의 기능을 사용.
author: j82w
ms.service: cosmos-db
ms.date: 05/28/2019
ms.author: jawilley
ms.devlang: c#
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7e48809537acc21edbcf12d299a333df486c258f
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66257147"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-cosmos-db-net-sdk"></a>진단 및 Azure Cosmos DB.NET SDK를 사용 하는 경우 문제 해결
이 문서에서는 일반적인 문제, 해결, 진단 단계 및 도구 사용 하는 경우는 [.NET SDK](sql-api-sdk-dotnet.md) Azure Cosmos DB SQL API 계정으로 합니다.
.NET SDK는 Azure Cosmos DB SQL API에 액세스 하려면 클라이언트 쪽 논리적 표현을 제공 합니다. 이 문서에서는 문제가 발생하는 경우 사용자에게 도움이 되는 도구 및 방법을 설명합니다.

## <a name="checklist-for-troubleshooting-issues"></a>문제 해결을 위한 검사 목록:
프로덕션 환경에 응용 프로그램을 이동 하기 전에 다음 검사 목록을 것이 좋습니다. 검사 목록을 사용 하 여 문제를 방지할 수 몇 가지 일반적인 표시 될 수 있습니다. 또한 신속 하 게 문제가 발생 하는 경우를 진단할 수 있습니다.

*   최신 [SDK](https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/changelog.md)합니다. 미리 보기 Sdk 프로덕션용 쓰일 수 없습니다. 그러면 이미 해결 된 알려진된 문제가 발생 하지 것입니다.
*   [성능 팁](performance-tips.md)을 검토하고 제안된 사례를 따릅니다. 크기 조정, 대기 시간 및 다른 성능 문제를 방지 하는 데 도움이 됩니다.
*   문제를 해결 하는 데 SDK 로깅을 사용 합니다. 로깅을 사용 하도록 설정 하면 문제를 해결 하는 경우에 사용 하도록 설정 하는 것이 가장 좋습니다 성능이 저하 될 수 있습니다. 다음 로그를 사용할 수 있습니다.
    *   [로그 메트릭](monitor-accounts.md) Azure portal을 사용 하 여 합니다. 포털 메트릭을 Azure Cosmos DB에 해당 하는 문제 또는 클라이언트 쪽에서 온 경우를 결정 하는 데 도움이 되는 Azure Cosmos DB 원격 분석을 보여 줍니다.
    *   로그는 [진단 문자열](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.resourceresponsebase.requestdiagnosticsstring?view=azure-dotnet) 지점 작업 응답에서.
    *   로그는 [SQL 쿼리 메트릭](sql-api-query-metrics.md) 모든 쿼리 응답에서 
    *   에 대 한 설정에 따라 [SDK 로깅]( https://github.com/Azure/azure-cosmos-dotnet-v2/blob/master/docs/documentdb-sdk_capture_etl.md)

이 문서의 [일반적인 문제 및 해결 방법](#common-issues-workarounds) 섹션을 살펴봅니다.

확인 합니다 [GitHub 문제 섹션](https://github.com/Azure/azure-cosmos-dotnet-v2/issues) 적극적으로 모니터링 됩니다. 해결 방법이 있는 유사한 문제가 이미 제출되었는지 확인합니다. 솔루션을 찾지 못한 경우 GitHub 문제를 제출 합니다. 긴급 한 문제에 대 한 지원 틱을 열 수 있습니다.


## <a name="common-issues-workarounds"></a>일반적인 문제 및 해결 방법

### <a name="general-suggestions"></a>일반 제안
* 가능 하면 Azure Cosmos DB 계정으로 동일한 Azure 지역에서 앱을 실행 합니다. 
* 클라이언트 컴퓨터에 리소스가 부족 하 여 연결/가용성 문제로 인해 발생할 수 있습니다. Azure Cosmos DB 클라이언트를 실행 하는 노드의 CPU 사용률을 모니터링 하는 것이 좋습니다 및 크기 조정 업/아웃 높은 부하 상태에서 실행 중인 경우.

### <a name="check-the-portal-metrics"></a>포털 메트릭을 확인 합니다.
확인 합니다 [포털 메트릭](monitor-accounts.md) 클라이언트 쪽 문제 인지 아니면 서비스를 사용 하 여 문제가 발생 하는 경우를 결정 하는 데 도움이 됩니다. 예를 들어 메트릭을 높은 비율의 속도가 제한 요청 (HTTP 상태 코드 429)를 포함 하는 경우 요청 제한적입니다. 즉, 한 다음 확인 합니다 [너무 커서 요청 속도] 섹션입니다. 

### <a name="request-timeouts"></a>요청 시간 제한
RequestTimeout 직접/t c P를 사용 하는 경우 일반적으로 발생 하지만 게이트웨이 모드에서 발생할 수 있습니다. 이들은 일반적인 알려진된 원인 및 문제를 해결 하는 방법에 대 한 제안 사항입니다.

* CPU 사용률이 높습니다는 대기 시간이 발생할 및/또는 시간 제한 요청 합니다. 고객이 더 많은 리소스를 제공 하려면 호스트 컴퓨터를 확장할 수 또는 더 많은 컴퓨터 간에 부하를 배포할 수 있습니다.
* 소켓 / 포트 가용성 낮을 수 있습니다. 2.0 버전 이전에 릴리스된.NET Sdk를 사용 하면 Azure에서 실행 하는 클라이언트 적중 수는 [Azure SNAT(PAT) 포트 고갈]합니다. 이 항상 최신 SDK 버전을 실행 하려면 권장 되는 이유의 예입니다.
* 여러 DocumentClient 인스턴스를 만드는 연결 경합 및 시간 제한 문제가 발생할 수 있습니다. 수행 합니다 [성능 팁](performance-tips.md), 단일 DocumentClient 인스턴스를 사용 하 여 전체 프로세스를 통해.
* 사용자가 해당 컬렉션은 프로 비전 되지 않습니다 충분히 백 엔드 요청을 제한 하 고 클라이언트가이 호출자에 게 표시 하지 않고 내부적으로 다시 시도 하기 때문에 높은 대기 시간 또는 요청 시간 제한을 때로는 참조 하십시오. 확인 합니다 [포털 메트릭](monitor-accounts.md)합니다.
* Azure Cosmos DB는 전체 프로 비전 된 처리량은 실제 파티션 간에 균등 하 게 배포합니다. 포털 메트릭을 워크 로드는 핫 발생 하는 경우 참조 확인 [파티션 키](partition-data.md)합니다. 이렇게 하면 집계 사용 된 처리량 (RU/s)를 프로 비전 된 Ru를 아래에 표시 되어야 하지만 사용 하는 단일 파티션의 처리량 (RU/s)을 프로 비전된 된 처리량을 초과 합니다. 
* 또한 2.0 SDK는 직접/TCP 연결에 채널 의미 체계를 추가합니다. 동시에 여러 요청에 대 한 하나의 TCP 연결이 됩니다. 이 특정 사례에서 두 가지 문제가 발생할 수 있습니다.
    * 채널에서 동시성 수준이 높은 경합이 발생할 수 있습니다.
    * 큰 요청 또는 응답 채널에서 head 아웃오브 라인 차단 될 하 고 상대적으로 낮은 수준의 동시성을 사용 하더라도 경합을 떨어뜨릴 수 있습니다.
    * 이러한 두 범주에 속하는 경우 하는 경우 (또는 CPU 사용률이 의심 되는 경우), 다음은 가능한 완화 방법:
        * 업/아웃 응용 프로그램 크기 조정 하려고 합니다.
        * 또한를 통해 SDK 로그를 캡처할 수 있습니다 [추적 수신기](https://github.com/Azure/azure-cosmosdb-dotnet/blob/master/docs/documentdb-sdk_capture_etl.md) 자세한 세부 정보를 가져옵니다.

### <a name="connection-throttling"></a>연결 제한
연결 제한 호스트 컴퓨터에서 연결 제한으로 인해 발생할 수 있습니다. 2.0 이전의 Azure에서 실행 하는 클라이언트 적중 수는 [Azure SNAT(PAT) 포트 고갈]합니다.

### <a name="snat"></a>Azure SNAT(PAT) 포트 고갈

공용 IP 주소 없이 앱이 Azure Virtual Machines에 배포되는 경우 기본적으로 [Azure SNAT 포트](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)는 VM 외부의 모든 엔드포인트에 대한 연결을 설정하는 데 사용됩니다. VM에서 Azure Cosmos DB 엔드포인트로 허용되는 연결 수는 [Azure SNAT 구성](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)으로 제한됩니다.

 Azure SNAT 포트는 VM에 개인 IP 주소 및 VM에서 공용 IP 주소에 연결하려고 하는 프로세스가 있는 경우에만 사용됩니다. Azure SNAT 제한을 피하는 두 가지 해결 방법이 있습니다.

* Azure Virtual Machines 가상 네트워크의 서브넷에 Azure Cosmos DB 서비스 엔드포인트를 추가합니다. 자세한 내용은 [Azure Virtual Network 서비스 엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)를 참조하세요. 

    서비스 엔드포인트를 사용하도록 설정한 경우 요청이 더 이상 공용 IP에서 Azure Cosmos DB로 전송되지 않습니다. 대신 가상 네트워크 및 서브넷 ID가 전송됩니다. 공용 IP만 허용되는 경우 이 변경 내용으로 인해 방화벽이 삭제될 수 있습니다. 방화벽을 사용하는 경우 서비스 엔드포인트를 사용하도록 설정하면 [Virtual Network ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl)을 사용하여 방화벽에 서브넷을 추가합니다.
* Azure VM에 공용 IP를 할당합니다.

### <a name="http-proxy"></a>HTTP 프록시
HTTP 프록시를 사용하는 경우 SDK `ConnectionPolicy`에서 구성된 연결 수를 지원할 수 있는지 확인합니다.
그렇지 않으면 연결 문제가 발생할 수 있습니다.

### 너무 많은 요청 속도<a name="request-rate-too-large"></a>
'요청 속도 너무 큽니다.' 또는 오류 코드 429 사용 된 처리량 (RU/s) 프로 비전된 된 처리량을 초과 했으므로 요청 제한 되는 나타냅니다. SDK를 기반으로 지정 하는 요청 자동으로 다시 시도 [다시 시도 정책](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions?view=azure-dotnet)합니다. 종종이 오류를 받게 되 면 컬렉션의 처리량을 늘리는 것이 좋습니다. 확인 합니다 [포털의 메트릭](use-metrics.md) 429 오류가 발생 하는 경우를 확인 합니다. 검토 하 [파티션 키](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) 균등 요청 및 저장소 볼륨에서에서 발생 하도록 합니다. 

### <a name="slow-query-performance"></a>느린 쿼리 성능
합니다 [메트릭을 쿼리](sql-api-query-metrics.md) 쿼리는 대부분의 시간을 소모 하는 상황을 확인 하는 데 도움이 됩니다. 쿼리 메트릭을 볼 수 있습니다 되는 한 정도에 소요 되는 백 엔드 vs 클라이언트입니다.
* 백 엔드 쿼리가 신속 하 게 반환 하 고 클라이언트에서 많은 시간을 소비 하는 경우 컴퓨터의 부하를 확인 합니다. 응답을 처리할 수 있는 리소스에 대 한 SDK 기다리고 하에 충분 한 리소스가 없을 가능성이 있습니다.
* 백 엔드 쿼리 속도가 느린 경우 시도 [쿼리를 최적화할](optimize-cost-queries.md) 현재 살펴보고 [인덱싱 정책](index-overview.md) 

 <!--Anchors-->
[Common issues and workarounds]: #common-issues-workarounds
[Enable client SDK logging]: #logging
[너무 커서 요청 속도]: #request-rate-too-large
[Request Timeouts]: #request-timeouts
[Azure SNAT(PAT) 포트 고갈]: #snat
[Production check list]: #production-check-list


