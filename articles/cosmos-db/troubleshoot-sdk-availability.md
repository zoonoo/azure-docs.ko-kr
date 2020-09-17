---
title: 다중 지역 환경에서 Azure Cosmos Sdk의 가용성 진단 및 문제 해결
description: 다중 지역 환경에서 작동 하는 경우 Azure Cosmos SDK 가용성 동작에 대 한 모든 것을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.date: 09/16/2020
ms.author: maquaran
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0c717aca88095df05fc7927f3c3d6e2d481925d2
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708915"
---
# <a name="diagnose-and-troubleshoot-the-availability-of-azure-cosmos-sdks-in-multiregional-environments"></a>다중 지역 환경에서 Azure Cosmos Sdk의 가용성 진단 및 문제 해결

이 문서에서는 특정 지역에 연결 문제가 있거나 지역 장애 조치가 발생 하는 경우 Azure Cosmos Sdk의 최신 버전에 대 한 동작을 설명 합니다.

모든 Azure Cosmos Sdk는 지역별 기본 설정을 사용자 지정 하는 옵션을 제공 합니다. 다른 Sdk에서 사용 되는 속성은 다음과 같습니다.

* .NET V2 SDK의 [Connectionpolicy](/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations) 속성입니다.
* .NET V3 SDK의 [CosmosClientOptions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationregion) 또는 [CosmosClientOptions ApplicationPreferredRegions](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.applicationpreferredregions) 속성
* Java V4 SDK의 preferredRegions 메서드를 [CosmosClientBuilder.](/java/api/com.azure.cosmos.cosmosclientbuilder.preferredregions)
* Python SDK의 [Preferred_locations CosmosClient](/python/api/azure-cosmos/azure.cosmos.cosmos_client.cosmosclient) 매개 변수입니다.

단일 쓰기 지역 계정의 경우 모든 쓰기 작업은 항상 쓰기 지역으로 이동 하므로 기본 지역 목록은 읽기 작업에만 적용 됩니다. 여러 개의 쓰기 지역 계정에 대 한 기본 설정 목록은 읽기 및 쓰기 작업에 영향을 줍니다.

기본 지역을 설정 하지 않으면 지역 기본 설정 순서는 [Azure Cosmos DB 지역 목록 순서로](distribute-data-globally.md)정의 됩니다.

다음 시나리오 중 하나가 발생 하면 Azure Cosmos SDK를 사용 하는 클라이언트가 로그를 노출 하 고 **작업 진단 정보의**일부로 재시도 정보를 포함 합니다.

## <a name="removing-a-region-from-the-account"></a><a id="remove region"></a>계정에서 하위 지역 제거

Azure Cosmos 계정에서 영역을 제거 하면 해당 계정을 사용 하는 모든 SDK 클라이언트에서 백 엔드 응답 코드를 통해 지역 제거를 검색 합니다. 그러면 클라이언트는 지역 끝점을 사용할 수 없는 것으로 표시 합니다. 클라이언트는 현재 작업을 다시 시도 하 고, 모든 후속 작업은 우선 순위에 따라 다음 지역으로 영구적으로 라우팅됩니다.

## <a name="adding-a-region-to-an-account"></a>계정에 지역 추가

Azure Cosmos SDK 클라이언트는 5 분 마다 계정 구성을 읽고 인식 되는 지역을 새로 고칩니다.

영역을 제거 하 고 나중에 다시 계정에 추가 하는 경우 추가 된 지역에 우선 순위가 더 높은 경우 SDK는이 지역을 영구적으로 사용 하도록 다시 전환 합니다. 추가 된 지역이 검색 된 후 모든 후속 요청이 전달 됩니다.

Azure Cosmos 계정에 없는 지역에 연결 하기 위해 클라이언트를 구성 하는 경우 기본 지역은 무시 됩니다. 해당 지역을 나중에 추가 하면 클라이언트에서 해당 지역을 검색 하 고 해당 지역으로 영구적으로 전환 합니다.

## <a name="failover-the-write-region-in-a-single-write-region-account"></a><a id="manual-failover-single-region"></a>단일 쓰기 지역 계정에서 쓰기 지역 장애 조치 (Failover)

현재 쓰기 지역의 장애 조치 (failover)를 시작 하면 다음 쓰기 요청이 알려진 백 엔드 응답과 함께 실패 합니다. 이 응답이 감지 되 면 클라이언트는 계정을 쿼리하여 새 쓰기 영역을 확인 하 고 현재 작업을 다시 시도 하 고 이후의 모든 쓰기 작업을 새 지역으로 영구적으로 라우팅합니다.

## <a name="regional-outage"></a>지역 가동 중단

계정이 단일 쓰기 지역이 고 쓰기 작업 중에 지역 가동 중단이 발생 하는 경우이 동작은 [수동 장애 조치 (failover)](#manual-failover-single-region)와 유사 합니다. 읽기 요청 또는 여러 쓰기 지역 계정의 경우이 동작은 [지역을 제거](#remove region)하는 것과 비슷합니다.

## <a name="session-consistency-guarantees"></a>세션 일관성 보장

[세션 일관성](consistency-levels.md#guarantees-associated-with-consistency-levels)을 사용 하는 경우 클라이언트는 자체 쓰기를 읽을 수 있도록 보장 해야 합니다. 읽기 지역 기본 설정이 쓰기 지역과 다른 단일 쓰기 지역 계정에서는 사용자가 쓰기를 실행 하 고 로컬 지역에서 읽기를 수행할 때 로컬 지역에서 데이터 복제를 아직 받지 않은 경우가 있을 수 있습니다 (광원 제약 조건의 속도). 이러한 경우 SDK는 읽기 작업에서 특정 오류를 검색 하 고 허브 지역에서 읽기를 다시 시도 하 여 세션 일관성을 유지 합니다.

## <a name="transient-connectivity-issues-on-tcp-protocol"></a>TCP 프로토콜의 일시적인 연결 문제

Azure Cosmos SDK 클라이언트가 TCP 프로토콜을 사용 하도록 구성 된 경우 지정 된 요청에 대해 네트워크 상태가 일시적으로 특정 끝점과의 통신에 영향을 주는 상황이 발생할 수 있습니다. 이러한 임시 네트워크 상태는 TCP 시간 제한으로 노출 될 수 있습니다. 클라이언트는 특정 시간 (초) 동안 동일한 끝점에서 로컬로 요청을 다시 시도 합니다.

사용자가 두 개 이상의 지역이 있는 기본 지역 목록을 구성 하 고 Azure Cosmos 계정이 여러 쓰기 지역 또는 단일 쓰기 지역이 고 작업이 읽기 요청인 경우 클라이언트는 기본 설정 목록에서 다음 지역의 해당 단일 작업을 다시 시도 합니다.

## <a name="next-steps"></a>다음 단계

* 최신 [.NET SDK](sql-api-sdk-dotnet-standard.md) 사용
* 최신 [JAVA SDK](sql-api-sdk-java-v4.md) 사용
* 최신 [PYTHON SDK](sql-api-sdk-python.md) 사용
* 최신 [노드 SDK](sql-api-sdk-node.md) 사용
