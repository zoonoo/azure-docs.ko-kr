---
title: 포함 파일
description: 포함 파일
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 04/06/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 93bb0a853199b8b1fc38b8cc903af0a434cab034
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50254567"
---
현재 Azure Table Storage를 사용하는 경우 Azure Cosmos DB 테이블 API로 이동하면 다음과 같은 이점이 있습니다.

| | Azure 테이블 저장소 | Azure Cosmos DB 테이블 API |
| --- | --- | --- |
| 대기 시간 | 빠르지만 대기 시간에 대한 상한이 없습니다. | 읽기/쓰기에 대한 1자리 밀리초 대기 시간이 지원됩니다(전 세계의 모든 규모에 대해 99번째 백분위수에서 10ms 미만의 읽기 및 15ms 미만의 쓰기 대기 시간 지원). |
| 처리량 | 가변 처리량 모델입니다. 테이블의 확장 제한은 20,000개 작업/초입니다. | SLA를 통해 지원하는 [테이블당 예약된 전용 처리량](../articles/cosmos-db/request-units.md)으로 확장성이 뛰어납니다. 계정에는 처리량에 대한 상한이 없으며, 테이블당 1,000만 개 이상 작업/초를 지원합니다. |
| 글로벌 분포 | 고가용성을 위해 선택적인 읽기 가능한 보조 읽기 하위 지역 하나가 있는 단일 지역입니다. 장애 조치를 시작할 수 없습니다. | 하나에서 30개가 넘는 지역까지 [턴키 전역 배포](../articles/cosmos-db/distribute-data-globally.md)를 수행할 수 있습니다. 전 세계 어디에서나 [자동 및 수동 장애 조치](../articles/cosmos-db/high-availability.md)를 지원합니다. |
| 인덱싱 | PartitionKey 및 RowKey에 대한 기본 인덱스만 제공. 보조 인덱스가 없습니다. | 모든 속성에 대해 자동 및 전체 인덱싱을 수행할 수 있으며, 인덱스 관리가 없습니다. |
| 쿼리 | 쿼리 실행 시 기본 키에 대한 인덱스를 사용하고 그렇지 않은 경우 검색합니다. | 쿼리는 빠른 쿼리 시간을 위해 속성에 대해 자동 인덱싱을 활용할 수 있습니다. |
| 일관성 | 주 지역 내에서 강력하게 유지되며, 최종적으로는 보조 지역 내에서 유지됩니다. | 응용 프로그램 요구 사항에 따라 가용성, 대기 시간, 처리량 및 일관성을 절충할 수 있는 [잘 정의된 5가지 일관성 수준](../articles/cosmos-db/consistency-levels.md)이 적용됩니다. |
| 가격 | 저장소 최적화 | 처리량 최적화 |
| SLA | 99.99% 가용성입니다. | 모든 단일 지역 계정 및 모든 다중 지역 계정에는 99.99% 가용성 SLA와 완화된 일관성이 제공되고, 일반 공급 시에는 모든 다중 지역 데이터베이스 계정에 [업계 최고 수준의 포괄적인 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)와 99.999% 읽기 가용성이 제공됩니다. |
