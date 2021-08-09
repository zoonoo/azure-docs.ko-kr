---
title: SQL 인사이트를 사용하여 경고 만들기(미리 보기)
description: Azure Monitor에서 SQL 인사이트를 사용하여 경고 만들기
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2021
ms.openlocfilehash: bb42f74f6ac8487a93479bdf980c66ef87e8e742
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726865"
---
# <a name="create-alerts-with-sql-insights-preview"></a>SQL 인사이트를 사용하여 경고 만들기(미리 보기)
SQL 인사이트에는 일반적인 SQL 문제에 대한 [Azure Monitor 경고 규칙](../alert/../alerts/alerts-overview.md)을 만드는 데 사용할 수 있는 경고 규칙 템플릿 집합이 포함되어 있습니다. SQL 인사이트의 경고 규칙은 Azure Monitor 로그의 *InsightsMetrics* 테이블에 저장된 성능 데이터를 기반으로 하는 로그 경고 규칙입니다.  

> [!NOTE]
> Resource Manager 템플릿을 사용하여 SQL 통계에 대한 경고를 만들려면 [SQL 통계에 대한 Resource Manager 템플릿 샘플](resource-manager-sql-insights.md#create-an-alert-rule-for-sql-insights)을 참조하세요.


> [!NOTE]
> 더 많은 SQL 인사이트 경고 규칙 템플릿에 대한 요청이 있는 경우 이 페이지의 맨 아래에 있는 링크를 사용하거나 Azure Portal의 SQL 인사이트 피드백 링크를 사용하여 피드백을 보내주세요.

## <a name="enable-alert-rules"></a>경고 규칙 사용 
Azure Portal에서 Azure Monitor 경고를 사용하도록 설정하려면 다음 단계를 수행합니다.생성되는 경고 규칙의 범위는 선택한 모니터링 프로필에서 모니터링되는 모든 SQL 리소스에 대하여 지정됩니다.  경고 규칙이 트리거되면 특정 SQL 인스턴스 또는 데이터베이스에서 트리거됩니다.

> [!NOTE]
> *InsightsMetrics* 테이블의 데이터 세트에 대해 쿼리를 실행한 다음 해당 쿼리를 경고 규칙으로 저장하여 사용자 지정 [로그 경고 규칙](../alerts/alerts-log.md)을 만들 수도 있습니다. 

Azure Portal의 Azure Monitor 메뉴에 있는 **Insights** 섹션에서 **SQL(미리 보기)** 를 선택합니다. **경고** 를 클릭

:::image type="content" source="media/sql-insights-alerts/alerts-button.png" alt-text="경고 단추":::

**경고** 창이 페이지의 오른쪽에 열립니다. 기본적으로 이 경고는 이미 만든 경고 규칙에 따라 선택한 모니터링 프로필의 SQL 리소스에 대해 발생한 경고를 표시합니다. 경고 규칙을 만드는 데 사용할 수 있는 템플릿 목록을 표시하는 **경고 템플릿** 을 선택합니다.

:::image type="content" source="media/sql-insights-alerts/alert-templates.png" alt-text="경고 템플릿":::

**경고 규칙 만들기** 페이지에서 규칙의 기본 설정을 검토하고 필요에 따라 편집합니다. [작업 그룹](../alerts/action-groups.md)을 선택하여 경고 규칙이 트리거될 때 알림과 작업을 만들 수도 있습니다. 모든 속성을 확인한 후 **경고 규칙 사용** 을 클릭해 경고 규칙을 만듭니다.


:::image type="content" source="media/sql-insights-alerts/alert-rule.png" alt-text="경고 규칙 페이지":::

경고 규칙을 즉시 배포하려면 **경고 규칙 배포** 를 클릭합니다. 규칙 템플릿을 실제로 배포하기 전에 보려면 **템플릿 보기** 를 클릭합니다.

:::image type="content" source="media/sql-insights-alerts/alert-rule-deploy.png" alt-text="경고 규칙 배포":::

템플릿 보기를 선택하는 경우 템플릿 페이지에서 **배포** 를 선택하여 경고 규칙을 만듭니다.

:::image type="content" source="media/sql-insights-alerts/view-template-deploy.png" alt-text="보기 템플릿에서 배포":::


## <a name="next-steps"></a>다음 단계

[Azure Monitor의 경고](../alerts/alerts-overview.md)에 대해 자세히 알아봅니다.

