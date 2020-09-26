---
title: 다중 지역 환경에서 Azure Cosmos Sdk의 가용성 진단 및 문제 해결
description: 다중 지역 환경에서 작동 하는 경우 Azure Cosmos SDK 가용성 동작에 대 한 모든 것을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.date: 09/24/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 8dd7ced2dfcfd3c555555d6f0a197623bd8726f2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91330437"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>다중 지역 환경에서 Azure Cosmos Sdk의 가용성 진단 및 문제 해결

이 문서에서는 특정 지역에 연결 문제가 있거나 지역 장애 조치가 발생 하는 경우 Azure Cosmos Sdk의 최신 버전에 대 한 동작을 설명 합니다.

모든 Azure Cosmos Sdk는 지역별 기본 설정을 사용자 지정 하는 옵션을 제공 합니다. 다른 Sdk에서 사용 되는 속성은 다음과 같습니다.

* .NET V2 SDK의 [Connectionpolicy](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 속성입니다.
* .NET V3 SDK의 [CosmosClientOptions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 또는 [CosmosClientOptions ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) 속성
* Java V4 SDK의 preferredRegions 메서드를 [CosmosClientBuilder.](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions)
* Node SDK의 [Preferred_locations CosmosClient](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) 매개 변수입니다.
* JS SDK의 preferredLocations 매개 변수를 [CosmosClientOptions](/javascript/api/@azure/cosmos/connectionpolicy#preferredlocations) .

국가별 기본 설정을 지정 하는 경우 클라이언트는 다음 표에 설명 된 대로 지역에 연결 됩니다.

|계정 유형 |읽기 |쓰기 |
|------------------------|--|--|
| 단일 쓰기 지역 | 기본 지역 | 주 지역  |
| 여러 쓰기 지역 | 기본 지역 | 기본 지역  |

기본 영역을 설정 하지 않은 경우:

|계정 유형 |읽기 |쓰기 |
|------------------------|--|--|
| 단일 쓰기 지역 | 주 지역 | 주 지역 |
| 여러 쓰기 지역 | 주 지역  | 주 지역  |

> [!NOTE]
> 주 지역은 [Azure Cosmos 계정 지역 목록의](distribute-data-globally.md) 첫 번째 지역을 나타냅니다.

다음 시나리오 중 하나가 발생 하면 Azure Cosmos SDK를 사용 하는 클라이언트가 로그를 노출 하 고 **작업 진단 정보의**일부로 재시도 정보를 포함 합니다.

* .NET V2 SDK의 응답에 대 한 *RequestDiagnosticsString* 속성입니다.
* .NET V3 SDK의 응답 및 예외에 대 한 *진단* 속성입니다.
* Java V4 SDK의 응답 및 예외에 대 한 *Getdiagnostics ()* 메서드입니다.

이러한 이벤트 중 SLA 보증에 대 한 포괄적인 정보는 [가용성에 대 한 sla](high-availability.md#slas-for-availability)를 참조 하세요.

## <a name="removing-a-region-from-the-account"></a><a id="remove-region"></a>계정에서 하위 지역 제거

Azure Cosmos 계정에서 영역을 제거 하면 해당 계정을 사용 하는 모든 SDK 클라이언트에서 백 엔드 응답 코드를 통해 지역 제거를 검색 합니다. 그러면 클라이언트는 지역 끝점을 사용할 수 없는 것으로 표시 합니다. 클라이언트는 현재 작업을 다시 시도 하 고, 모든 후속 작업은 우선 순위에 따라 다음 지역으로 영구적으로 라우팅됩니다.

## <a name="adding-a-region-to-an-account"></a>계정에 지역 추가

Azure Cosmos SDK 클라이언트는 5 분 마다 계정 구성을 읽고 인식 되는 지역을 새로 고칩니다.

영역을 제거 하 고 나중에 다시 계정에 추가 하는 경우 추가 된 지역에서 현재 연결 된 지역 보다 SDK 구성에서 더 높은 지역 기본 설정 순서를 사용 하는 경우 SDK는이 지역을 영구적으로 사용 하도록 다시 전환 합니다. 추가 된 지역이 검색 된 후 모든 후속 요청이 전달 됩니다.

Azure Cosmos 계정에 없는 지역에 연결 하기 위해 클라이언트를 구성 하는 경우 기본 지역은 무시 됩니다. 해당 지역을 나중에 추가 하면 클라이언트에서 해당 지역을 검색 하 고 해당 지역으로 영구적으로 전환 합니다.

## <a name="failover-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>단일 쓰기 지역 계정에서 쓰기 지역 장애 조치 (Failover)

현재 쓰기 지역의 장애 조치 (failover)를 시작 하면 다음 쓰기 요청이 알려진 백 엔드 응답과 함께 실패 합니다. 이 응답이 감지 되 면 클라이언트는 계정을 쿼리하여 새 쓰기 영역을 확인 하 고 현재 작업을 다시 시도 하 고 이후의 모든 쓰기 작업을 새 지역으로 영구적으로 라우팅합니다.

## <a name="regional-outage"></a>지역 가동 중단

계정이 단일 쓰기 지역이 고 쓰기 작업 중에 지역 가동 중단이 발생 하는 경우이 동작은 [수동 장애 조치 (failover)](#manual-failover-single-region)와 유사 합니다. 읽기 요청 또는 여러 쓰기 지역 계정의 경우이 동작은 [지역을 제거](#remove-region)하는 것과 비슷합니다.

## <a name="session-consistency-guarantees"></a>세션 일관성 보장

[세션 일관성](consistency-levels.md#guarantees-associated-with-consistency-levels)을 사용 하는 경우 클라이언트는 자체 쓰기를 읽을 수 있도록 보장 해야 합니다. 읽기 지역 기본 설정이 쓰기 지역과 다른 단일 쓰기 지역 계정에서는 사용자가 쓰기를 실행 하 고 로컬 지역에서 읽기를 수행할 때 로컬 지역에서 데이터 복제를 아직 받지 않은 경우가 있을 수 있습니다 (광원 제약 조건의 속도). 이러한 경우 SDK는 읽기 작업에서 특정 오류를 검색 하 고 허브 지역에서 읽기를 다시 시도 하 여 세션 일관성을 유지 합니다.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP 프로토콜의 일시적인 연결 문제

Azure Cosmos SDK 클라이언트가 TCP 프로토콜을 사용 하도록 구성 된 경우 지정 된 요청에 대해 네트워크 상태가 일시적으로 특정 끝점과의 통신에 영향을 주는 상황이 발생할 수 있습니다. 이러한 임시 네트워크 상태는 TCP 시간 제한으로 노출 될 수 있습니다. 클라이언트는 특정 시간 (초) 동안 동일한 끝점에서 로컬로 요청을 다시 시도 합니다.

사용자가 두 개 이상의 지역이 있는 기본 지역 목록을 구성 하 고 Azure Cosmos 계정이 여러 쓰기 지역 또는 단일 쓰기 지역이 고 작업이 읽기 요청인 경우 클라이언트는 기본 설정 목록에서 다음 지역의 해당 단일 작업을 다시 시도 합니다.

## <a name="next-steps"></a>다음 단계

* [가용성 sla](high-availability.md#slas-for-availability)를 검토 합니다.
* 최신 [.NET SDK](sql-api-sdk-dotnet-standard.md) 사용
* 최신 [JAVA SDK](sql-api-sdk-java-v4.md) 사용
* 최신 [PYTHON SDK](sql-api-sdk-python.md) 사용
* 최신 [노드 SDK](sql-api-sdk-node.md) 사용
