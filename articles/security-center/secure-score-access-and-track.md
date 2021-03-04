---
title: Azure Security Center에서 보안 점수 추적
description: Azure Security Center에서 보안 점수를 액세스 하 고 추적 하는 여러 가지 방법에 대해 알아봅니다.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: 5efc48d348e9cfceab590bcfba8c621e7721376f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107697"
---
# <a name="access-and-track-your-secure-score"></a>보안 점수 액세스 및 추적

다음 섹션에 설명 된 대로 Azure Portal를 통해 또는 프로그래밍 방식으로 전체 보안 점수 뿐만 아니라 구독 당 점수를 확인할 수 있습니다.

> [!TIP]
> 점수가 계산 되는 방법에 대 한 자세한 설명은 [계산-점수 이해](secure-score-security-controls.md#calculations---understanding-your-score)를 참조 하세요.

## <a name="get-your-secure-score-from-the-portal"></a>포털에서 보안 점수 얻기

Security Center는 포털에서 점수를 두드러지게 표시 합니다. Security Center 개요 페이지의 첫 번째 주 타일입니다. 이 타일을 선택 하면 전용 보안 점수 페이지로 이동 하 여 구독 별로 세분화 된 점수를 볼 수 있습니다. 단일 구독을 선택 하 여 우선 순위가 지정 된 권장 사항의 세부 목록과 구독의 점수를 수정 하 게 될 잠재적 영향을 확인 합니다. 

Security Center의 포털 페이지에서 다음 위치에 보안 점수가 표시 됩니다.

- Security Center **개요** 의 타일 (주 대시보드):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Security Center 대시보드의 보안 점수":::

- 전용 **보안 점수** 페이지에서 구독 및 관리 그룹에 대 한 보안 점수를 볼 수 있습니다.

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Security Center의 보안 점수 페이지에서 구독에 대 한 보안 점수":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Security Center의 보안 점수 페이지에서 관리 그룹의 보안 점수":::

    > [!NOTE]
    > 충분 한 권한이 없는 모든 관리 그룹은 해당 점수를 "제한 됨"으로 표시 합니다. 

- **권장 사항** 페이지의 맨 위에:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="Security Center 권장 사항 페이지의 보안 점수":::

## <a name="get-your-secure-score-from-the-rest-api"></a>REST API에서 보안 점수 얻기

보안 점수 API를 통해 점수에 액세스할 수 있습니다. API 메서드는 데이터를 쿼리할 수 있는 유연성을 제공하고, 시간 경과에 따른 보안 점수에 대한 사용자 고유의 보고 메커니즘을 빌드합니다. 예를 들어 [보안 점수 API](/rest/api/securitycenter/securescores) 를 사용 하 여 특정 구독에 대 한 점수를 가져올 수 있습니다. 또한 보안 [점수 컨트롤 API](/rest/api/securitycenter/securescorecontrols) 를 사용 하 여 보안 제어 및 구독의 현재 점수를 나열할 수 있습니다.

![API를 통해 단일 보안 점수 검색](media/secure-score-security-controls/single-secure-score-via-api.png)

보안 점수 API를 기반으로 하는 도구에 대 한 예제는 [GitHub 커뮤니티의 보안 점수 영역](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)을 참조 하세요. 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Azure 리소스 그래프에서 보안 점수 얻기

Azure 리소스 그래프를 사용 하면 강력한 필터링, 그룹화 및 정렬 기능을 통해 클라우드 환경에서 리소스 정보에 즉시 액세스할 수 있습니다. Azure 구독 간에 프로그래밍 방식으로 또는 Azure Portal 내에서 정보를 쿼리하는 빠르고 효율적인 방법입니다. [Azure Resource Graph에 대한 자세한 정보](../governance/resource-graph/index.yml).

Azure 리소스 그래프를 사용 하 여 여러 구독의 보안 점수에 액세스 하려면 다음을 수행 합니다.

1. Azure Portal에서 **Azure 리소스 그래프 탐색기** 를 엽니다.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure 리소스 그래프 탐색기 시작 * * 권장 사항 페이지" :::

1. (지침을 보려면 아래 예제를 사용 하 여) Kusto 쿼리를 입력 합니다.

    - 이 쿼리는 구독 ID, 점의 현재 점수 및 백분율을 반환 하 고 구독의 최대 점수를 반환 합니다. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - 이 쿼리는 모든 보안 컨트롤의 상태를 반환 합니다. 각 컨트롤에 대해 비정상 리소스의 수, 현재 점수 및 최대 점수를 얻을 수 있습니다. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. **쿼리 실행** 을 선택 합니다.


## <a name="tracking-your-secure-score-over-time"></a>시간에 따른 보안 점수 추적

### <a name="secure-score-over-time-report-in-workbooks-page"></a>통합 문서 페이지에서 시간에 따른 보안 점수 보고서

Security Center의 통합 문서 페이지에는 구독, 보안 제어 등의 점수를 시각적으로 추적 하기 위한 미리 만들어진 보고서가 포함 되어 있습니다. [Security Center 데이터에 대 한 풍부한 대화형 보고서 만들기](custom-dashboards-azure-workbooks.md)에서 자세히 알아보세요.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Azure Security Center의 통합 문서 갤러리에서 시간에 따른 보안 점수 보고서의 섹션":::

### <a name="power-bi-pro-dashboards"></a>대시보드 Power BI Pro

Pro 계정이 있는 사용자 Power BI 경우 **시간** 에 따른 보안 점수 Power BI 대시보드를 사용 하 여 시간에 따른 보안 점수를 추적 하 고 변경 내용을 조사할 수 있습니다.

> [!TIP]
> GitHub의 Azure Security Center 커뮤니티 전용 영역에서 보안 점수를 사용 하 여 프로그래밍 방식으로 작업 하는 데 사용할 수 있는 다른 도구 뿐만 아니라이 대시보드를 찾을 수 있습니다. https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

대시보드에는 보안 상태를 분석 하는 데 도움이 되는 다음과 같은 두 가지 보고서가 포함 되어 있습니다.

- **리소스 요약** -리소스 상태와 관련 하 여 요약 된 데이터를 제공 합니다.
- **보안 점수 요약** -점수 진행률과 관련 하 여 요약 된 데이터를 제공 합니다. "구독 당 시간에 따른 보안 점수" 차트를 사용 하 여 점수의 변화를 확인 합니다. 점수가 크게 변경 되는 경우 변경 될 수 있는 가능한 변경 내용에 대해서는 "보안 점수에 영향을 줄 수 있는 검색 된 변경 내용" 표를 확인 합니다. 이 테이블에는 권장 사항 중 하나에 대해 보안 상태가 변경 된 리소스, 새로 배포 된 리소스 또는 리소스가 삭제 됩니다.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="시간에 따른 보안 점수를 추적 하 고 변경 내용을 조사 하는 시간에 따른 선택적 보안 점수 Power BI 대시보드":::


## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 점수를 액세스 하 고 추적 하는 방법을 설명 했습니다. 관련 자료는 다음 문서를 참조하세요.

- [권장 사항의 다양한 요소에 대해 알아보기](security-center-recommendations.md)
- [권장 사항을 수정하는 방법 알아보기](security-center-remediate-recommendations.md)
- [안전한 점수를 사용 하 여 프로그래밍 방식으로 작업 하기 위한 GitHub 기반 도구 보기](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)