---
title: 성능 권장 사항 - PostgreSQL용 Azure 데이터베이스 - 단일 서버
description: 이 문서에서는 PostgreSQL - 단일 서버에 대한 Azure 데이터베이스의 성능 권장 사항 기능에 대해 설명합니다.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: a30af0c8bef47a37fe3439e885d3895a2c826225
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768472"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>PostgreSQL - 단일 서버에 대한 Azure 데이터베이스의 성능 권장 사항

**다음에 적용됩니다.** PostgreSQL용 Azure 데이터베이스 - 단일 서버 버전 9.6, 10, 11

성능 권장 사항 기능은 데이터베이스를 분석하여 성능 향상을 위한 사용자 지정 제안을 만듭니다. 권장 사항을 생성하기 위해 분석은 스키마를 비롯한 다양한 데이터베이스 특성을 살펴봅니다. 서버의 [쿼리 저장소를](concepts-query-store.md) 사용하여 성능 권장 사항 기능을 완전히 활용할 수 있습니다. 성능 권장 사항을 구현한 후에는 성능을 테스트하여 이러한 변경사항이 미치는 영향을 평가해야 합니다. 

## <a name="permissions"></a>사용 권한
성능 권장 사항 기능을 사용하여 분석을 실행하는 데 필요한**소유자** 또는 **참가자** 권한입니다.

## <a name="performance-recommendations"></a>성능 권장 사항
[성능 권장 사항](concepts-performance-recommendations.md) 기능은 서버 전체의 워크로드를 분석하여 성능 향상 가능성이 있는 인덱스를 식별합니다.

PostgreSQL 서버의 Azure 포털 페이지의 메뉴 모음의 **지능형 성능** 섹션에서 성능 **권장 사항을** 엽니다.

![성능 권장 사항 방문 페이지](./media/concepts-performance-recommendations/performance-recommendations-page.png)

**분석을** 선택하고 분석을 시작할 데이터베이스를 선택합니다. 워크로드에 따라 분석을 완료하는 데 몇 분 정도 걸릴 수 있습니다. 분석이 완료되면 포털에 알림이 표시됩니다. 분석은 데이터베이스에 대한 심층적인 검사를 수행합니다. 사용량이 많은 기간에는 분석을 수행하는 것이 좋습니다. 

**권장 사항** 창에 발견된 경우 권장 사항 목록이 표시됩니다.

![성능 권장 사항 새 페이지](./media/concepts-performance-recommendations/performance-recommendations-result.png)

권장 사항은 자동으로 적용되지 않습니다. 권장 사항을 적용하려면 쿼리 텍스트를 복사하여 선택한 클라이언트에서 실행합니다. 권장 사항을 평가하기 위해 테스트하고 모니터링해야 합니다. 

## <a name="recommendation-types"></a>추천 유형

현재 두 가지 유형의 권장 사항이 지원됩니다: *인덱스 만들기* 및 *삭제 인덱스*.

### <a name="create-index-recommendations"></a>인덱스 만들기 권장 사항
인덱스 권장 사항 *만들기는* 워크로드에서 가장 자주 실행되거나 시간이 많이 소요되는 쿼리속도를 높이기 위해 새 인덱스를 제안합니다. 이 권장 사항 유형에서는 [쿼리 저장소를](concepts-query-store.md) 사용하도록 설정해야 합니다. 쿼리 저장소는 쿼리 정보를 수집하고 분석이 권장 사항을 만드는 데 사용하는 자세한 쿼리 런타임 및 빈도 통계를 제공합니다.

### <a name="drop-index-recommendations"></a>인덱스 삭제 권장 사항
누락된 인덱스를 검색하는 것 외에도 PostgreSQL용 Azure 데이터베이스는 기존 인덱스의 성능을 분석합니다. 인덱스가 거의 사용되지 않거나 중복되는 경우 분석기는 인덱스를 삭제하는 것이 좋습니다.

## <a name="considerations"></a>고려 사항
* 성능 권장 사항은 [읽기 복제본에](concepts-read-replicas.md)사용할 수 없습니다.
## <a name="next-steps"></a>다음 단계
- Azure Database for PostgreSQL의 [모니터링 및 튜닝](concepts-monitoring.md)에 대해 자세히 알아보세요.

