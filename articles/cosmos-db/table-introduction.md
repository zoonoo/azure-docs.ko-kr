---
title: "Azure Cosmos DB의 테이블 API 소개 | Microsoft Docs"
description: "인기 있는 OSS MongoDB API를 사용하여 짧은 대기 시간으로 엄청난 양의 키-값 데이터를 저장하고 쿼리하기 위해 Azure Cosmos DB를 사용하는 방법에 대해 알아봅니다."
services: cosmosdb
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 8226ecfb5fa8c70b162ae776679302db6cfdcdbc
ms.contentlocale: ko-kr
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Azure Cosmos DB: 테이블 API 소개

[Azure Cosmos DB](introduction.md)는 업무에 중요한 응용 프로그램에 대한 Microsoft의 전역 분산 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB는 [업계 최고의 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/)로 지원되는 [턴키 전역 분산](../documentdb/documentdb-distribute-data-globally.md), 전 세계적으로 [처리량 및 저장소의 탄력적인 확장](partition-data.md), 99번째 백분위수의 1자리 수 밀리초 크기 대기 시간, [잘 정의된 5개 일관성 수준](../documentdb/documentdb-consistency-levels.md), 보장된 고가용성을 제공합니다. Azure Cosmos DB는 사용자가 스키마 및 인덱스 관리를 처리하지 않아도 되도록 [데이터를 자동으로 인덱싱](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)합니다. 또한 다중 모델 방식이며, 문서, 키-값, 그래프 및 열 형식 데이터 모델을 지원합니다. 

![Azure Table Storage API 및 Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Azure Cosmos DB는 유연한 스키마가 있는 키-값 저장소를 필요로 하는 응용 프로그램에 대한 테이블 API를 제공합니다. [Azure Table Storage](../storage/storage-introduction.md) SDK 및 REST API를 Azure Cosmos DB와 함께 사용하여 작업할 수 있습니다. Azure Cosmos DB를 사용하면 높은 처리량 요구 사항의 테이블을 만들 수 있습니다. Azure Cosmos DB는 현재 공개 미리 보기에서 처리량 최적화 테이블(비공식적으로 "프리미엄 테이블"이라고 함)을 지원합니다. 

높은 저장소 및 낮은 처리량 요구 사항의 테이블에 대해서도 Azure Table Storage를 계속 사용할 수 있습니다. Azure Cosmos DB는 향후 업데이트에서 저장소 최적화 테이블을 지원하고 기존 및 새로운 Azure Table Storage 계정을 Azure Cosmos DB로 업그레이드할 것입니다.

## <a name="premium-and-standard-table-apis"></a>프리미엄 및 표준 테이블 API
현재 Azure Table Storage를 사용하는 경우 Azure Cosmos DB의 "프리미엄 테이블" 미리 보기로 이동하면 다음과 같은 이점이 있습니다.

|  | Azure Table Storage | Azure Cosmos DB: Table Storage(미리 보기) |
| --- | --- | --- |
| 대기 시간 | 빠르지만 대기 시간에 상한 없음 | 1자리 밀리초 읽기/쓰기 대기 시간, 규모에 관계 없이 전 세계 어디에서든 99번째 백분위수에서 10ms 미만의 읽기 대기 시간 및 15ms 미만의 쓰기 대기 시간 지원 |
| 처리량 | 확장성이 뛰어나지만 전용 처리량 모델 없음. 20,000작업/초의 테이블 확장 한도 | [테이블당 전용 예약된 처리량](../documentdb/documentdb-request-units.md)으로 탁월한 확장성(SLA로 지원). 계정은 처리량에 상한이 없으며 테이블당 1000만 개 이상의 초당 작업 지원 |
| 글로벌 배포 | HA에 대해 선택적으로 읽을 수 있는 보조 읽기 지역이 하나 있는 단일 지역. 장애 조치를 시작할 수 없음 | 1 ~ 30개 이상 지역까지 [턴키 글로벌 배포](../documentdb/documentdb-distribute-data-globally.md), 전 세계 어디에서 언제든 [자동 및 수동 장애 조치](../documentdb/documentdb-regional-failovers.md) 지원 |
| 인덱싱 | PartitionKey 및 RowKey에 대한 기본 인덱스만 제공. 보조 인덱스 없음 | 모든 속성에 대한 자동 및 전체 인덱싱, 인덱스 관리 없음 |
| 쿼리 | 쿼리 실행 시 기본 키에 대한 인덱스를 사용하고 그렇지 않은 경우 검색합니다. | 쿼리는 빠른 쿼리 시간을 위해 속성에 대해 자동 인덱싱을 활용할 수 있습니다. Azure Cosmos DB의 데이터베이스 엔진은 집계, 지리 공간 및 정렬을 지원할 수 있습니다. |
| 일관성 | 주 지역 내에서 강력, 보조 지역에서 최종적 | 응용 프로그램 요구 사항에 따라 가용성, 대기 시간, 처리량 및 일관성을 조정할 수 있는 [5가지 잘 정의된 일관성 수준](../documentdb/documentdb-consistency-levels.md) |
| 가격 | 저장소 최적화  | 처리량 최적화 |
| SLA | 99.9% 가용성 | 단일 지역 내에서 99.99% 가용성 및 더 높은 가용성이 필요한 경우 더 많은 지역을 추가하는 기능. 일반 공급에서 [업계 최고의 포괄적인 SLA](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) |

## <a name="how-to-get-started"></a>시작하는 방법

[Azure Portal](https://portal.azure.com)에서 Azure Cosmos DB 계정을 만들고 [.NET을 사용한 Table API 빠른 시작](create-table-dotnet.md)을 통해 시작합니다. 

## <a name="next-steps"></a>다음 단계

다음은 시작하기 위한 몇 가지 지침입니다.
* 기존 NET 테이블 SDK를 사용하여 [Azure Cosmos DB 테이블 API](create-table-dotnet.md)를 시작합니다.
* [Azure Cosmos DB를 사용한 글로벌 배포](../documentdb/documentdb-distribute-data-globally.md)에 대해 자세히 알아봅니다.
* [Azure Cosmos DB의 프로비전된 처리량](../documentdb/documentdb-request-units.md)에 대해 자세히 알아봅니다.
