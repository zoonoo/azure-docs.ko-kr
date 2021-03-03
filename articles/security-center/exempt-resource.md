---
title: 리소스, 구독, 관리 그룹 및 보안 점수에서 Azure Security Center 권장 사항을 제외 합니다.
description: 구독 또는 관리 그룹에서 보안 권장 사항을 제외 하 고 보안 점수에 영향을 주지 않도록 규칙을 만드는 방법에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 01/22/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 374ddaa088fba9ae7035f170562e06b7f07eae47
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709379"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>보안 점수에서 리소스 및 권장 사항 제외 

모든 보안 팀의 핵심 우선 순위는 분석가가 조직에 중요 한 작업 및 인시던트에 초점을 맞출 수 있도록 하는 것입니다. Security Center는 환경을 사용자 지정 하 고 보안 점수가 조직의 보안 우선 순위를 반영 하도록 하는 많은 기능을 제공 합니다. **제외** 옵션은 이러한 기능 중 하나입니다.

Azure Security Center에서 보안 권장 사항을 조사할 때 가장 먼저 검토 하는 정보 중 하나는 영향을 받는 리소스 목록입니다.

경우에 따라 포함 하지 않아야 하는 리소스가 나열 됩니다. 또는 해당 영역이 아닌 것으로 생각되는 범위에 권장 사항이 표시됩니다. Security Center에서 추적 하지 않는 프로세스에서 리소스를 재구성 했을 수 있습니다. 권장 사항은 특정 구독에 적합하지 않을 수도 있습니다. 또는 조직이 특정 리소스 또는 권장 사항과 관련된 위험을 감수하기로 결정했을 수도 있습니다.

이러한 경우 권장 사항에 대 한 예외를 만들 수 있습니다.

- **리소스를 제외** 하여 향후 비정상적인 리소스에 나열되지 않고 보안 점수에 영향을 주지 않도록 합니다. 리소스가 해당되지 않는 것으로 나열되고 선택한 특정 근거와 함께 이유가 "예외"로 표시됩니다.

- 권장 사항이 보안 점수에 영향을 주지 않고 향후 구독 또는 관리 그룹에 표시되지 않도록 하려면 **구독 또는 관리 그룹을 제외** 합니다. 이는 기존 리소스와 나중에 만드는 리소스와 관련이 있습니다. 권장 사항은 선택한 범위에 대해 선택한 특정 근거로 표시됩니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|미리 보기<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|가격 책정:|Azure Defender 고객에 게 추가 비용 없이 제공 되는 프리미엄 Azure 정책 기능입니다. 다른 사용자에 게는 나중에 요금이 부과 될 수 있습니다.|
|필요한 역할 및 권한:|예외를 만들기 위한 **구독 소유자** 또는 **정책 기여자**<br>규칙을 만들려면 Azure Policy에서 정책을 편집할 수 있는 권한이 필요 합니다.<br>[Azure Policy에서 AZURE RBAC 사용 권한](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)에 대해 자세히 알아보세요.|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![아니요](./media/icons/no-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||

## <a name="define-an-exemption"></a>예외 정의

구독, 관리 그룹 또는 리소스에 대해 Security Center 하는 보안 권장 사항을 미세 조정 하려면 다음에 대 한 예외 규칙을 만들 수 있습니다.

- 특정 **권장 사항을** 표시 하거나 "완화" 또는 "위험 수락"으로 표시 합니다. 구독, 여러 구독 또는 전체 관리 그룹에 대 한 권장 사항 예외를 만들 수 있습니다.
- 특정 권장 사항에 대해 **하나 이상의 리소스** 를 "완화" 또는 "위험 수락"으로 표시 합니다.

> [!TIP]
> API를 사용 하 여 예외를 만들 수도 있습니다. 예제 JSON의 경우 관련 구조에 대 한 설명은 [Azure Policy 예외 구조체](../governance/policy/concepts/exemption-structure.md)를 참조 하세요.

예외 규칙을 만들려면 다음을 수행 합니다.

1. 특정 권장 사항에 대 한 권장 사항 세부 정보 페이지를 엽니다.

1. 페이지 맨 위에 있는 도구 모음에서 **제외** 를 선택 합니다.

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="구독이 나 관리 그룹에서 제외 되는 권장 사항에 대 한 예외 규칙을 만듭니다.":::

1. **예외** 창에서 다음을 수행 합니다.
    1. 이 예외 규칙의 범위를 선택 합니다.
        - 관리 그룹을 선택 하는 경우 해당 그룹 내의 모든 구독에서 권장 사항이 제외 됩니다.
        - 권장 구성에서 하나 이상의 리소스를 제외 하기 위해이 규칙을 만드는 경우 "선택한 리소스"를 선택 하 고 목록에서 관련 항목을 선택 합니다.

    1. 이 예외 규칙의 이름을 입력 합니다.
    1. 필요에 따라 만료 날짜를 설정 합니다.
    1. 예외에 대 한 범주를 선택 합니다.
        - 타사 **에서 확인 됨 (완화 됨)** – 타사 서비스를 사용 하는 경우 Security Center 확인 되지 않습니다. 

            > [!NOTE]
            > 권장 사항을 완화 된 것으로 면제 하면 보안 점수를 고려 하지 않습니다. 하지만 비정상 리소스에 대 한 지점은 *제거* 되지 않기 때문에 점수가 증가 하 게 됩니다.

        - **위험 수락 (면제)** –이 권장 사항을 완화 하지 않는 위험을 수락 하기로 결정 한 경우
    1. 필요에 따라 설명을 입력 합니다.
    1. **만들기** 를 선택합니다.

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="구독 또는 관리 그룹에서 권장 사항을 제외 하는 예외 규칙을 만드는 단계":::

    예외가 적용 되는 경우 (최대 30 분이 걸릴 수 있음):
    - 권장 사항 또는 리소스는 보안 점수에 영향을 주지 않습니다.
    - 특정 리소스를 제외 하면 권장 사항 세부 정보 페이지의 **해당 없음** 탭에 나열 됩니다.
    - 권장 사항을 제외 하는 경우 Security Center의 권장 사항 페이지에서 기본적으로 숨겨집니다. 해당 페이지의 **권장 사항 상태** 필터에 대 한 기본 옵션이 **적용 되지 않는** 권장 사항을 제외 하는 것 이기 때문입니다. 보안 제어에서 모든 권장 사항을 제외 하는 경우에도 마찬가지입니다.

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Azure Security Center 권장 사항 페이지의 기본 필터 적용 되지 않는 권장 사항 및 보안 제어를 숨깁니다.":::

    - 권장 사항 세부 정보 페이지의 맨 위에 있는 정보 스트립은 제외 된 리소스 수를 업데이트 합니다.
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="제외 된 리소스 수":::

1. 제외 된 리소스를 검토 하려면 **해당 없음** 탭을 엽니다.

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="예외 수정":::

    각 예외에 대 한 이유는 테이블 (1)에 포함 됩니다.

    예외를 수정 하거나 삭제 하려면 (2)와 같이 줄임표 메뉴 ("...")를 선택 합니다.

1. 구독에 대 한 모든 예외 규칙을 검토 하려면 정보 스트립에서 **예외 보기** 를 선택 합니다.

    > [!IMPORTANT]
    > 하나의 권장 사항과 관련 된 특정 예외를 보려면 관련 범위 및 권장 사항 이름에 따라 목록을 필터링 합니다.

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy의 예외 페이지":::

    > [!TIP]
    > 또는 [Azure 리소스 그래프를 사용 하 여 예외에 대 한 권장 사항을 찾습니다](#find-recommendations-with-exemptions-using-azure-resource-graph).

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>구독에서 생성 되는 예외 모니터링

이 페이지의 앞부분에서 설명한 대로 예외 규칙은 구독 및 관리 그룹에서 리소스에 영향을 미치는 권장 사항을 세부적으로 제어 하는 강력한 도구입니다. 

사용자가이 기능을 연습 하는 방법을 추적 하기 위해 논리 앱 플레이 북를 배포 하는 ARM (Azure Resource Manager) 템플릿과 예외가 생성 된 경우 사용자에 게 알리는 데 필요한 모든 API 연결을 만들었습니다.

- 플레이 북에 대 한 자세한 내용은 기술 커뮤니티 블로그 게시물 [Azure Security Center에서 리소스 예외를 추적 하는 방법](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580) 을 참조 하세요.
- [Azure Security Center GitHub 리포지토리에서](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption) ARM 템플릿을 찾을 수 있습니다.
- 모든 필수 구성 요소를 배포 하려면 [이 자동화 된 프로세스를 사용](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json) 합니다.


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Azure 리소스 그래프를 사용 하 여 예외에 대 한 권장 사항 찾기

인수를 사용 하면 강력한 필터링, 그룹화 및 정렬 기능을 통해 클라우드 환경에서 리소스 정보에 즉시 액세스할 수 있습니다. Azure 구독 간에 프로그래밍 방식으로 또는 Azure Portal 내에서 정보를 쿼리하는 빠르고 효율적인 방법입니다.

예외 규칙이 있는 모든 권장 사항을 보려면 다음을 수행 합니다.

1. **Azure 리소스 그래프 탐색기** 를 엽니다.

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure 리소스 그래프 탐색기 시작 * * 권장 사항 페이지" :::

1. 다음 쿼리를 입력 하 고 **쿼리 실행** 을 선택 합니다.

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


다음 페이지에서 자세히 알아보세요.
- [Azure Resource Graph에 대한 자세한 정보](../governance/resource-graph/index.yml).
- [Azure Resource Graph Explorer를 사용하여 쿼리를 만드는 방법](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language(KQL)](/azure/data-explorer/kusto/query/)





## <a name="exemption-rule-faq"></a>예외 규칙 FAQ

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>하나의 권장 사항이 여러 정책 이니셔티브에 있는 경우 어떻게 되나요?

보안 권장 사항이 둘 이상의 정책 이니셔티브에 표시 되는 경우도 있습니다. 동일한 구독에 할당 된 동일한 권장 구성의 인스턴스가 여러 개 있는 경우 권장 사항에 대 한 예외를 만들면 편집 권한이 있는 모든 이니셔티브에 영향을 줍니다. 

예를 들어 권장 사항 * * * *은 Azure Security Center 하 여 모든 Azure 구독에 할당 된 기본 정책 이니셔티브의 일부입니다. XXXXX에도 있습니다.

이 권장 사항에 대 한 예외를 만들려고 하면 다음 두 메시지 중 하나가 표시 됩니다.

- 두 이니셔티브를 편집 하는 데 필요한 권한이 있는 경우 다음을 확인할 수 있습니다.

    *이 권장 사항은 [이니셔티브 이름으로 쉼표로 구분]의 여러 정책 이니셔티브에 포함 되어 있습니다. 모든 예외에 대 한 예외가 생성 됩니다.*  

- 두 이니셔티브에 대해 충분 한 권한이 없는 경우이 메시지가 대신 표시 됩니다.

    *모든 정책 이니셔티브에 대해 예외를 적용 하기 위한 제한 된 권한을 가지 며, 충분 한 권한이 있는 이니셔티브 에서만 예외가 생성 됩니다.*


## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 점수에 영향을 주지 않도록 권장 구성에서 리소스를 제외 하는 방법을 배웠습니다. 보안 점수에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Security Center의 보안 점수](secure-score-security-controls.md)