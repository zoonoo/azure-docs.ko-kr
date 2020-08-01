---
title: Azure Cosmos DB에 대 한 자동화 된 성능, 비용, 보안 권장 사항
description: 워크 로드 패턴에 따라 Azure Cosmos DB에 대 한 사용자 지정 된 성능, 비용, 보안 및 기타 권장 사항을 보는 방법에 대해 알아봅니다.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: 8fa2fdf23a0d71b854e043b66c0aed7e944c5f39
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450839"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 자동화 된 권장 사항

Azure Cosmos DB를 비롯 한 모든 클라우드 서비스는 새로운 기능, 기능 및 향상 된 기능으로 빈번한 업데이트를 제공 합니다. 응용 프로그램에서 최신 성능 및 보안 업데이트를 유지 하는 것이 중요 합니다. Azure Portal는 응용 프로그램의 성능을 최대화할 수 있는 사용자 지정 권장 사항을 제공 합니다. Azure Cosmos DB의 advise 엔진은 Azure Cosmos DB 리소스의 사용 기록을 지속적으로 분석 하 고 워크 로드 패턴에 따라 권장 사항을 제공 합니다. 이러한 권장 사항은 분할, 인덱싱, 네트워크, 보안 등과 같은 영역에 해당 합니다. 이러한 맞춤형 권장 사항은 응용 프로그램의 성능을 향상 시키는 데 도움이 됩니다.

## <a name="view-recommendations"></a>권장 사항 보기

다음과 같은 방법으로 Azure Cosmos DB에 대 한 권장 사항을 볼 수 있습니다.

- 권장 사항을 확인 하는 한 가지 방법은 알림 탭 내에 있습니다. 새로운 권장 사항이 있는 경우 메시지 표시줄이 표시 됩니다. [Azure Portal](https://portal.azure.com) 에 로그인 하 여 Azure Cosmos 계정으로 이동 합니다. Azure Cosmos 계정 내에서 **알림** 창을 열고 **권장** 구성 탭을 선택 합니다. 메시지를 선택 하 고 권장 사항을 볼 수 있습니다.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Azure Cosmos DB 창에서 권장 사항 보기":::

- 또한 비용, 보안, 안정성, 성능, 운영 등 다양 한 버킷으로 분류 된 [Azure Advisor](../advisor/advisor-overview.md) 를 통해 권장 사항을 찾을 수 있습니다. 특정 구독을 선택 하 고 리소스 유형 ( **Azure Cosmos DB 계정**)을 기준으로 필터링 할 수 있습니다.  특정 권장 사항을 선택 하는 경우 작업을 수행 하기 위해 수행할 수 있는 작업이 표시 됩니다.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Azure Advisor 창에서 권장 사항 보기":::

Azure Cosmos DB 창에 표시 되는 모든 권장 사항은 Azure Advisor에서 사용할 수 있으며 그 반대의 경우도 마찬가지입니다. 이는 Azure Advisor 창, Azure Cosmos DB 창 또는 둘 다에 맞는 권장 구성 유형을 기반으로 하기 때문입니다.

현재 Azure Cosmos DB는 다음 영역에 대 한 권장 사항을 지원 합니다. 이러한 각 권장 사항에는 설명서의 관련 섹션에 대 한 링크가 포함 되어 있으므로 다음 단계를 쉽게 수행할 수 있습니다.

## <a name="sdk-usage-recommendations"></a>SDK 사용 권장 사항

이 범주에서 advisor는 이전 버전의 Sdk 사용을 검색 하 고 최신 버그 수정 및 성능 향상을 활용 하기 위해 최신 버전으로 업그레이드 하는 것을 권장 합니다. 현재 다음과 같은 SDK 관련 권장 사항을 사용할 수 있습니다.

|이름  |Description  |
|---------|---------|
| 이전 Spark 커넥터 | 이전 버전의 Spark 커넥터 사용을 검색 하 고 업그레이드를 권장 합니다. |
| 이전 .NET SDK | 이전 버전의 .NET SDK를 사용 하는 방법을 검색 하 고 업그레이드를 권장 합니다. |
| 이전 Java SDK | 이전 버전의 Java 커넥터 사용을 검색 하 고 업그레이드를 권장 합니다. |

## <a name="indexing-recommendations"></a>인덱싱 권장 사항

이 범주에서 advisor는 인덱싱 모드, 인덱싱 정책, 인덱싱된 경로를 검색 하 고 현재 구성이 쿼리 성능에 영향을 주는지 여부를 변경 하는 것을 권장 합니다. 현재 다음과 같은 인덱싱 관련 권장 사항을 사용할 수 있습니다.

|이름  |Description  |
|---------|---------|
| 지연 인덱싱 | 지연 인덱싱 모드의 사용을 검색 하 고 대신 일관 된 인덱싱 모드를 사용 하는 것이 좋습니다. Azure Cosmos DB의 지연 인덱싱 모드의 용도는 제한 되며, 경우에 따라 일관 된 인덱싱 모드를 적용 하는 경우 쿼리 결과의 새로 고침에 영향을 줄 수 있습니다. |
| 복합 인덱싱| 복합 인덱스를 사용 하 여 쿼리를 사용할 수 있는 계정을 검색 하 고이를 사용 하는 것이 좋습니다. 복합 인덱스는 일부 쿼리의 성능 및 처리량 소비를 크게 향상 시킬 수 있습니다.|
| 여러 인덱싱된 경로를 사용 하는 기본 인덱싱 정책 | 인덱싱된 경로가 많고 기본 인덱싱에서 실행 중인 컨테이너를 검색 하 고 인덱싱 정책의 사용자 지정을 권장 합니다.|
| 높은 o s/s 요금이 있는 쿼리 별 정렬| 최고/초 요금으로 쿼리를 실행 하는 컨테이너를 검색 하 고 복합 인덱스 탐색을 권장 합니다.|
| 인덱스 없음 및 높은 r u/초 소비를 포함 하는 MongoDB 3.6 계정| 3.6 MongoDB에 대 한 Azure Cosmos DB의 API를 검색 하 여 높은 r u/초 요금으로 쿼리를 실행 하 고 인덱스를 추가 하는 것을 권장 합니다.|

## <a name="cost-optimization-recommendations"></a>비용 최적화 권장 사항

이 범주에서 advisor는 r u/초 사용을 검색 하 고 리소스를 일부 변경 하거나 다른 가격 책정 모델을 활용 하 여 가격을 최적화할 수 있음을 결정 합니다. 현재 다음과 같은 비용 최적화 관련 권장 사항을 사용할 수 있습니다.

|이름  |Description  |
|---------|---------|
| 예약 용량 | 는 r u/s 사용률을 감지 하 고이를 활용할 수 있는 사용자에 게 예약 인스턴스를 권장 합니다. |
| 비활성 컨테이너 | 는 30 일 이상 사용 되지 않은 컨테이너를 검색 하 고 이러한 컨테이너의 처리량을 줄이거나 삭제 하는 것을 권장 합니다.|
| 높은 처리량이 있는 새 구독 | 하루에 비정상적으로 많은 계정이 포함 된 새 구독을 검색 하 고 알림을 제공 합니다. 이 알림은 특히 프로 비전 된 처리량 기반 모델에서 작동 하는 새 고객을 인식 하는 데 사용 되며,이는 소비 기반 모델이 아니라 Azure Cosmos DB 합니다. |

## <a name="migration-recommendations"></a>마이그레이션 권장 사항

이 범주에서 advisor는 기존 기능을 사용 하는 것을 감지 하 여 Azure Cosmos DB의 대규모 확장성 및 기타 이점을 활용할 수 있도록 마이그레이션을 권장 합니다. 현재 다음과 같은 마이그레이션 관련 권장 사항을 사용할 수 있습니다.

|이름  |Description  |
|---------|---------|
| 분할 되지 않은 컨테이너 | 최대 저장소 제한에 도달 하는 고정 크기 컨테이너를 검색 하 고 분할 된 컨테이너로 마이그레이션하는 것을 권장 합니다.|

## <a name="query-usage-recommendations"></a>쿼리 사용 권장 사항

이 범주에서 advisor는 쿼리 실행을 감지 하 고 일부 변경 내용으로 쿼리 성능을 튜닝할 수 있음을 식별 합니다. 현재 다음과 같은 쿼리 사용 권장 사항을 사용할 수 있습니다.

|이름  |Description  |
|---------|---------|
| 고정 페이지 크기가 있는 쿼리 | 고정 된 페이지 크기를 사용 하 여 실행 된 쿼리를 검색 하 고 특정 값을 정의 하는 대신-1을 사용 하는 것이 좋습니다 (페이지 크기에 제한이 없음). 이 옵션을 선택 하면 모든 결과를 검색 하는 데 필요한 네트워크 왕복 횟수가 줄어듭니다. |

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 쿼리 성능 조정](sql-api-query-metrics.md)
* Azure Cosmos DB를 사용 하는 경우 [쿼리 문제 해결](troubleshoot-query-performance.md)
