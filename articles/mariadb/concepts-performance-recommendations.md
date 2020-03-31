---
title: 성능 권장 사항 - MariaDB용 Azure 데이터베이스
description: 이 문서에서는 MariaDB에 대한 Azure 데이터베이스의 성능 권장 사항 기능에 대해 설명합니다.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 838a4123bd5007f987f27674862409445967a2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528101"
---
# <a name="performance-recommendations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 성능 권장 사항

**다음에 적용됩니다.** MariaDB 10.2에 대한 Azure 데이터베이스

성능 권장 사항 기능은 데이터베이스를 분석하여 성능 향상을 위한 사용자 지정 제안을 만듭니다. 권장 사항을 생성하기 위해 분석은 스키마를 비롯한 다양한 데이터베이스 특성을 살펴봅니다. 서버의 [쿼리 저장소를](concepts-query-store.md) 사용하여 성능 권장 사항 기능을 완전히 활용할 수 있습니다. 성능 스키마가 꺼져 있는 경우 쿼리 저장소를 켜면 performance_schema 및 기능에 필요한 성능 스키마 계측기의 하위 집합을 사용할 수 있습니다. 성능 권장 사항을 구현한 후에는 성능을 테스트하여 이러한 변경사항이 미치는 영향을 평가해야 합니다.

## <a name="permissions"></a>사용 권한

성능 권장 사항 기능을 사용하여 분석을 실행하는 데 필요한**소유자** 또는 **참가자** 권한입니다.

## <a name="performance-recommendations"></a>성능 권장 사항

[성능 권장 사항](concepts-performance-recommendations.md) 기능은 서버 전체의 워크로드를 분석하여 성능 향상 가능성이 있는 인덱스를 식별합니다.

MariaDB 서버의 Azure 포털 페이지의 메뉴 모음의 **지능형 성능** 섹션에서 성능 **권장 사항을** 엽니다.

![성능 권장 사항 방문 페이지](./media/concepts-performance-recommendations/performance-recommendations-page.png)

**분석을** 선택하고 분석을 시작할 데이터베이스를 선택합니다. 워크로드에 따라 분석을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 분석이 완료되면 포털에 알림이 표시됩니다. 분석은 데이터베이스에 대한 심층적인 검사를 수행합니다. 사용량이 많은 기간에는 분석을 수행하는 것이 좋습니다.

**권장 사항** 창에는 발견된 경우 권장 사항 목록과 이 권장 사항을 생성한 관련 쿼리 ID가 표시됩니다. 쿼리 ID를 사용하여 [mysql.query_store](concepts-query-store.md#mysqlquery_store) 보기를 사용하여 쿼리에 대해 자세히 알아볼 수 있습니다.

![성능 권장 사항 새 페이지](./media/concepts-performance-recommendations/performance-recommendations-result.png)

권장 사항은 자동으로 적용되지 않습니다. 권장 사항을 적용하려면 쿼리 텍스트를 복사하여 선택한 클라이언트에서 실행합니다. 권장 사항을 평가하기 위해 테스트하고 모니터링해야 합니다.

## <a name="recommendation-types"></a>추천 유형

현재 *인덱스 만들기* 권장 사항만 지원됩니다.

### <a name="create-index-recommendations"></a>인덱스 만들기 권장 사항

인덱스 권장 사항 *만들기는* 워크로드에서 가장 자주 실행되거나 시간이 많이 소요되는 쿼리속도를 높이기 위해 새 인덱스를 제안합니다. 이 권장 사항 유형에서는 [쿼리 저장소를](concepts-query-store.md) 사용하도록 설정해야 합니다. 쿼리 저장소는 쿼리 정보를 수집하고 분석이 권장 사항을 만드는 데 사용하는 자세한 쿼리 런타임 및 빈도 통계를 제공합니다.

## <a name="next-steps"></a>다음 단계

- MariaDB용 Azure 데이터베이스에서 [모니터링 및 튜닝에](concepts-monitoring.md) 대해 자세히 알아봅니다.