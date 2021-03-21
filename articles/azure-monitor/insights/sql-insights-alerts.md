---
title: SQL insights (미리 보기)를 사용 하 여 경고 만들기
description: Azure Monitor에서 SQL 정보를 사용 하 여 경고 만들기
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: 5fe853ee0f7a113bfb8b0511744d9087f67927c4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609905"
---
# <a name="create-alerts-with-sql-insights-preview"></a>SQL insights (미리 보기)를 사용 하 여 경고 만들기
SQL insights에는 일반적인 SQL 문제에 대 한 [Azure Monitor에서 경고 규칙](../alert/../alerts/alerts-overview.md) 을 만드는 데 사용할 수 있는 경고 규칙 템플릿 집합이 포함 되어 있습니다. SQL insights의 경고 규칙은 Azure Monitor 로그의 *InsightsMetrics* 테이블에 저장 된 성능 데이터를 기반으로 하는 로그 경고 규칙입니다.  

> [!NOTE]
> 더 많은 SQL insights 경고 규칙 템플릿에 대 한 요청이 있는 경우이 페이지의 맨 아래에 있는 링크를 사용 하거나 Azure Portal의 SQL insights 피드백 링크를 사용 하 여 피드백을 보내 주세요.

## <a name="enable-alert-rules"></a>경고 규칙 사용 
Azure Portal에서 Azure Monitor 경고를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.생성 되는 경고 규칙의 범위는 선택한 모니터링 프로필에서 모니터링 되는 모든 SQL 리소스에 적용 됩니다.  경고 규칙이 트리거되면 특정 SQL 인스턴스 또는 데이터베이스에서 트리거됩니다.

> [!NOTE]
> *InsightsMetrics* 테이블의 데이터 집합에 대해 쿼리를 실행 한 다음 해당 쿼리를 경고 규칙으로 저장 하 여 사용자 지정 [로그 경고 규칙](../alerts/alerts-log.md) 을 만들 수도 있습니다. 

Azure Portal Azure Monitor 메뉴의 **Insights** 섹션에서 **SQL (미리 보기)** 을 선택 합니다. **경고** 클릭

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="경고 단추":::

**경고** 창이 페이지의 오른쪽에 열립니다. 기본적으로이 경고는 이미 만든 경고 규칙에 따라 선택한 모니터링 프로필의 SQL 리소스에 대해 발생 한 경고를 표시 합니다. 경고 규칙을 만드는 데 사용할 수 있는 사용 가능한 템플릿 목록을 표시 하는 **경고 템플릿** 을 선택 합니다.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="경고 템플릿":::

**경고 규칙 만들기** 페이지에서 규칙에 대 한 기본 설정을 검토 하 고 필요에 따라 편집 합니다. [작업 그룹](../alerts/action-groups.md) 을 선택 하 여 경고 규칙이 트리거될 때 알림 및 작업을 만들 수도 있습니다. 모든 속성을 확인 한 후 경고 규칙 **사용** 을 클릭 하 여 경고 규칙을 만듭니다.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="경고 규칙 페이지":::

경고 규칙을 즉시 배포 하려면 **경고 규칙 배포** 를 클릭 합니다. 규칙 템플릿을 실제로 배포 하기 전에 보려면 **템플릿 보기** 를 클릭 합니다.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="경고 규칙 배포":::

템플릿을 보도록 선택 하는 경우 템플릿 페이지에서 **배포** 를 선택 하 여 경고 규칙을 만듭니다.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="보기 템플릿에서 배포":::


## <a name="next-steps"></a>다음 단계

[Azure Monitor의 경고](../alerts/alerts-overview.md)에 대해 자세히 알아보세요.

