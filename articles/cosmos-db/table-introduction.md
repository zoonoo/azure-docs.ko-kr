---
title: "Azure Cosmos DB의 테이블 API 소개 | Microsoft Docs"
description: "인기 있는 OSS MongoDB API를 사용하여 짧은 대기 시간으로 엄청난 양의 키-값 데이터를 저장하고 쿼리하기 위해 Azure Cosmos DB를 사용하는 방법에 대해 알아봅니다."
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/29/2017
ms.author: arramac
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3ccc3b176df2f0a5d864554a74508292d272bd5a
ms.contentlocale: ko-kr
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB: 테이블 API 소개

[Azure Cosmos DB](introduction.md)에서는 Azure 테이블 저장소 서비스를 위해 작성된 응용 프로그램에 테이블 API(미리 보기)를 제공하며, [턴키 전역 배포](distribute-data-globally.md), [전용 처리량](partition-data.md), 99번째 백분위수에서의 1자리 밀리초 대기 시간, 보장된 고가용성 및 [자동 보조 인덱싱](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)과 같은 프리미엄 기능이 필요합니다. 이러한 응용 프로그램은 코드를 변경하지 않고 테이블 API를 사용하여 Azure Cosmos DB로 마이그레이션할 수 있으며 프리미엄 기능을 활용할 수 있습니다.

Aravind Ramachandran이 Azure Cosmos DB용 테이블 API를 시작하는 방법에 대해 설명하는 다음 비디오를 보면서 시작하는 것이 좋습니다.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>프리미엄 및 표준 테이블 API
현재 Azure Table Storage를 사용하는 경우 Azure Cosmos DB의 "프리미엄 테이블" 미리 보기로 이동하면 다음과 같은 이점이 있습니다.

|  | Azure Table Storage | Azure Cosmos DB: Table Storage(미리 보기) |
| --- | --- | --- |
| 대기 시간 | 빠르지만 대기 시간에 상한 없음 | 읽기/쓰기에 대한 1자리 밀리초 대기 시간(전 세계의 모든 규모에 99번째 백분위수에서 10ms 미만의 읽기 대기 시간 및 15ms 미만의 쓰기 대기 시간 지원) |
| 처리량 | 가변 처리량 모델, 20,000작업/초의 테이블 확장 한도 | [테이블당 전용 예약된 처리량](request-units.md)으로 탁월한 확장성(SLA로 지원). 계정은 처리량에 상한이 없으며 테이블당 1000만 개 이상의 초당 작업 지원 |
| 글로벌 배포 | HA에 대해 선택적으로 읽을 수 있는 보조 읽기 지역이 하나 있는 단일 지역. 장애 조치를 시작할 수 없음 | 1 ~ 30개 이상 지역까지 [턴키 글로벌 배포](distribute-data-globally.md), 전 세계 어디에서 언제든 [자동 및 수동 장애 조치](regional-failover.md) 지원 |
| 인덱싱 | PartitionKey 및 RowKey에 대한 기본 인덱스만 제공. 보조 인덱스 없음 | 모든 속성에 대한 자동 및 전체 인덱싱, 인덱스 관리 없음 |
| 쿼리 | 쿼리 실행 시 기본 키에 대한 인덱스를 사용하고 그렇지 않은 경우 검색합니다. | 쿼리는 빠른 쿼리 시간을 위해 속성에 대해 자동 인덱싱을 활용할 수 있습니다. Azure Cosmos DB의 데이터베이스 엔진은 집계, 지리 공간 및 정렬을 지원할 수 있습니다. |
| 일관성 | 주 지역 내에서 강력, 보조 지역에서 최종적 | 응용 프로그램 요구 사항에 따라 가용성, 대기 시간, 처리량 및 일관성을 절충할 수 있는 [잘 정의된 5가지 일관성 수준](consistency-levels.md) |
| 가격 | 저장소 최적화  | 처리량 최적화 |
| SLA | 99.9% 가용성 | 단일 지역 내에서 99.99% 가용성 및 더 높은 가용성이 필요한 경우 더 많은 지역을 추가하는 기능. 일반 공급에서 [업계 최고의 포괄적인 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) |

## <a name="how-to-get-started"></a>시작하는 방법

[Azure Portal](https://portal.azure.com)에서 Azure Cosmos DB 계정을 만들고 [.NET을 사용한 Table API 빠른 시작](create-table-dotnet.md)을 통해 시작합니다. 

## <a name="next-steps"></a>다음 단계

다음은 시작하기 위한 몇 가지 지침입니다.
* [테이블 API를 사용하여 .NET 응용 프로그램 빌드](create-table-dotnet.md)
* [.NET의 테이블 API(미리 보기)를 사용하여 개발](tutorial-develop-table-dotnet.md)
* [테이블 API를 사용하여 테이블 데이터 쿼리](tutorial-query-table.md)
* [테이블 API를 사용하여 Azure Cosmos DB 전역 배포를 설정하는 방법](tutorial-global-distribution-table.md)
* [.NET용 Azure Cosmos DB 테이블 API SDK](table-sdk-dotnet.md)

