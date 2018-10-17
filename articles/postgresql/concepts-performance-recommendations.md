---
title: Azure Database for PostgreSQL의 성능 권장 사항
description: 이 문서에서는 Azure Database for PostgreSQL에서 확인할 수 있는 성능 권장 사항에 대해 설명합니다.
services: postgresql
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: 6de302dbcfa9d6d1d2b311f41b03d8e54aeb63f6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395443"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL의 성능 권장 사항

**적용 대상:** Azure Database for PostgreSQL 9.6 및 10

> [!IMPORTANT]
> 성능 권장 사항은 일부 지역에서 공개 미리 보기로 제공됩니다.

성능 권장 사항 기능은 성능 향상을 위해 Azure Database for PostgreSQL 서버에서 만들 수 있는 상위 인덱스를 확인합니다. 이 기능은 인덱스 권장 사항을 생성하기 위해 쿼리 저장소에서 보고한 스키마와 워크로드를 포함하여 다양한 데이터베이스 특성을 고려하고 있습니다. 성능 권장 사항이 구현되면 고객이 성능을 테스트하여 변경에 따른 영향을 평가해야 합니다. 

## <a name="permissions"></a>권한
성능 권장 사항 기능을 사용하여 분석을 실행하는 데 필요한**소유자** 또는 **참가자** 권한입니다.

## <a name="performance-recommendations"></a>성능 권장 사항
[성능 권장 사항](concepts-performance-recommendations.md) 기능은 서버 전체의 워크로드를 분석하여 성능 향상 가능성이 있는 인덱스를 식별합니다.

PostgreSQL 서버에 대한 Azure Portal 페이지의 메뉴 표시줄에 있는 **지원 + 문제 해결** 섹션에서 **성능 권장 사항**을 엽니다.

![성능 권장 사항 방문 페이지](./media/concepts-performance-recommendations/performance-recommendations-landing-page.png)

**분석**을 선택하고 데이터베이스를 선택합니다. 그러면 분석이 시작됩니다. 워크로드에 따라 분석을 완료하는 데 몇 분이 걸릴 수 있습니다. 분석이 완료되면 포털에 알림이 표시됩니다.

**성능 권장 사항** 창에는 권장 사항이 있는 경우 권장 사항 목록이 표시됩니다. 권장 사항에는 관련 **데이터베이스**, **테이블**, **열** 및 **인덱스 크기**에 대한 정보가 표시됩니다.

![성능 권장 사항 새 페이지](./media/concepts-performance-recommendations/performance-recommendations-result.png)

권장 사항을 구현하려면 쿼리 텍스트를 복사하고 선택한 클라이언트에서 실행합니다.

## <a name="next-steps"></a>다음 단계
- Azure Database for PostgreSQL의 [모니터링 및 튜닝](concepts-monitoring.md)에 대해 자세히 알아보세요.

