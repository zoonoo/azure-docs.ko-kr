---
title: 정책 준수 데이터 가져오기
description: Azure Policy 평가 및 효과는 준수를 결정합니다. Azure 리소스의 규정 준수 세부 정보를 가져오는 방법을 알아봅니다.
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5a308a23e84587eba69951081674d3525f083441
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537953"
---
# <a name="get-compliance-data-of-azure-resources"></a>Azure 리소스의 규정 준수 데이터 가져오기

Azure Policy의 가장 큰 혜택 중 하나는 구독 및 구독의 [데이터 그룹](../../management-groups/overview.md)에 있는 리소스에 제공하는 정보 또는 컨트롤입니다. 이 컨트롤은 리소스가 잘못된 위치에 생성되거나, 일반적이고 일관된 태그 사용을 적용하거나, 적절한 구성 및 설정에 대한 기존 리소스를 감사하지 않도록 방지하는 다양한 방식으로 사용될 수 있습니다. 모든 경우에 환경의 규정 준수 상태를 이해할 수 있도록 Azure Policy에 의해 데이터가 생성됩니다.

정책 및 이니셔티브 할당에 의해 생성된 준수 정보에 액세스할 수 있는 여러 가지 방법이 있습니다.

- [Azure Portal](#portal) 사용
- [명령줄](#command-line) 스크립팅을 통함

준수를 보고하는 메서드를 살펴보기 전에 호환성 정보가 업데이트되는 시기 및 평가 주기를 트리거하는 빈도 및 이벤트를 살펴보겠습니다.

> [!WARNING]
> 준수 상태가 **등록 되지 않음**으로 보고 되는 경우, **Microsoft Policyinsights** 리소스 공급자가 등록 되어 있고, [Azure Policy의 azure rbac 권한](../overview.md#azure-rbac-permissions-in-azure-policy)에 설명 된 대로 사용자에 게 적절 한 azure rbac (역할 기반 액세스 제어) 권한이 있는지 확인 합니다.

## <a name="evaluation-triggers"></a>평가 트리거

완료된 평가 주기의 결과는 `PolicyStates` 및 `PolicyEvents` 작업을 통해 `Microsoft.PolicyInsights` 리소스 공급자에서 사용할 수 있습니다. Azure Policy Insights REST API의 작업에 대한 자세한 내용은 [Azure Policy Insights](/rest/api/policy-insights/)를 참조하세요.

할당된 정책 및 이니셔티브의 평가는 다양한 이벤트의 결과로 발생합니다.

- 정책 또는 이니셔티브는 범위에 새로 할당됩니다. 할당이 정의된 범위에 적용되는 데 30분 정도가 걸립니다. 적용 되 면 평가 주기는 새로 할당 된 정책 또는 이니셔티브에 대해 해당 범위 내의 리소스에 대해 시작 하 고, 정책 또는 이니셔티브에서 사용 되는 효과에 따라, 리소스는 규격, 비규격 또는 예외로 표시 됩니다. 대량의 리소스에 대해 대규모 정책 또는 이니셔티브를 평가하는 데는 시간이 걸릴 수 있습니다. 따라서 평가 주기 완료 시점을 미리 예측할 수 없습니다. 작업이 완료되면 업데이트된 준수 결과는 포털 및 SDK에서 지원됩니다.

- 범위에 새로 할당된 정책 또는 이니셔티브는 업데이트됩니다. 이 시나리오에 대한 평가 주기 및 타이밍은 범위에 대한 새 할당과 동일합니다.

- 리소스는 Azure Resource Manager, REST API 또는 지원 되는 SDK를 통해 할당 된 범위 내에 배포 되거나 업데이트 됩니다. 이 시나리오에서 효과 이벤트(추가, 감사, 거부, 배포) 및 개별 리소스에 대한 호환 상태 정보는 약 15분 후에 포털 및 SDK에서 사용할 수 있습니다. 이 이벤트는 다른 리소스에 대한 평가로 이어지지 않습니다.

- [정책 예외가](../concepts/exemption-structure.md) 생성, 업데이트 또는 삭제 됩니다. 이 시나리오에서는 정의 된 예외 범위에 대해 해당 할당이 평가 됩니다.

- 표준 준수 평가 주기입니다. 24시간마다 한 번씩 할당은 자동으로 다시 계산됩니다. 많은 리소스에 대해 대규모 정책 또는 이니셔티브를 평가하는 데는 시간이 걸릴 수 있습니다. 따라서 평가 주기 완료 시점을 미리 예측할 수 없습니다. 작업이 완료되면 업데이트된 준수 결과는 포털 및 SDK에서 지원됩니다.

- [게스트 구성](../concepts/guest-configuration.md) 리소스 공급자는 관리형 리소스를 통해 규정 준수 세부 정보로 업데이트됩니다.

- 주문형 검사

### <a name="on-demand-evaluation-scan"></a>주문형 평가 검사

구독 또는 리소스 그룹에 대 한 평가 검색은 Azure CLI, Azure PowerShell 또는 REST API에 대 한 호출을 사용 하 여 시작할 수 있습니다. 이 검사는 비동기 프로세스입니다.

#### <a name="on-demand-evaluation-scan---azure-cli"></a>주문형 평가 검사-Azure CLI

호환성 검사가 [az policy state trigger-scan](/cli/azure/policy/state#az-policy-state-trigger-scan) 명령으로 시작 됩니다.

기본적으로 `az policy state trigger-scan`은 현재 구독의 모든 리소스에 대한 평가를 시작합니다. 특정 리소스 그룹에 대 한 평가를 시작 하려면 **리소스 그룹** 매개 변수를 사용 합니다. 다음 예에서는 _MyRG_ 리소스 그룹에 대해 현재 구독에서 규정 준수 검사를 시작합니다.

```azurecli-interactive
az policy state trigger-scan --resource-group "MyRG"
```

**대기 안 함** 매개 변수를 계속 하기 전에 비동기 프로세스가 완료 될 때까지 기다리지 않도록 선택할 수 있습니다.

#### <a name="on-demand-evaluation-scan---azure-powershell"></a>주문형 평가 검사 - Azure PowerShell

[Start-AzPolicyComplianceScan](/powershell/module/az.policyinsights/start-azpolicycompliancescan) cmdlet을 사용하여 준수 검사가 시작됩니다.

기본적으로 `Start-AzPolicyComplianceScan`은 현재 구독의 모든 리소스에 대한 평가를 시작합니다. 특정 리소스 그룹에 대한 평가를 시작하려면 **ResourceGroupName** 매개 변수를 사용합니다. 다음 예에서는 _MyRG_ 리소스 그룹에 대해 현재 구독에서 규정 준수 검사를 시작합니다.

```azurepowershell-interactive
Start-AzPolicyComplianceScan -ResourceGroupName 'MyRG'
```

결과 출력을 제공하기 전에 비동기 호출이 완료될 때까지 PowerShell이 기다리게 하거나 백그라운드에서 [작업](/powershell/module/microsoft.powershell.core/about/about_jobs)으로 실행되게 할 수 있습니다. PowerShell 작업을 사용하여 백그라운드에서 준수 검사를 실행하려면 **AsJob** 매개 변수를 사용하고 이 예제의 `$job`처럼 값을 개체로 설정합니다.

```azurepowershell-interactive
$job = Start-AzPolicyComplianceScan -AsJob
```

`$job` 개체를 확인하여 작업 상태를 확인할 수 있습니다. 작업은 `Microsoft.Azure.Commands.Common.AzureLongRunningJob` 유형입니다. `$job` 개체에서 `Get-Member`를 사용하여 사용 가능한 속성과 메서드를 확인합니다.

규정 준수 검사를 실행하는 동안 `$job` 개체 출력을 확인하면 다음과 같은 결과가 출력됩니다.

```azurepowershell-interactive
$job

Id     Name            PSJobTypeName   State         HasMoreData     Location             Command
--     ----            -------------   -----         -----------     --------             -------
2      Long Running O… AzureLongRunni… Running       True            localhost            Start-AzPolicyCompliance…
```

규정 준수 검사가 완료되면 **상태** 속성이 _완료_로 변경됩니다.

#### <a name="on-demand-evaluation-scan---rest"></a>주문형 평가 검사 - REST

비동기 프로세스이므로 REST 엔드포인트는 검사가 응답을 완료할 때까지 기다리지 않습니다. 대신, URI를 제공하여 요청된 평가의 상태를 쿼리합니다.

각 REST API URI에는 사용자가 자신의 값으로 대체해야 하는 변수가 있습니다.

- `{YourRG}` - 사용자의 리소스 그룹 이름으로 대체
- `{subscriptionId}` - 사용자의 구독 ID로 대체

검사는 구독 또는 리소스 그룹에서 리소스의 평가를 지원합니다. 다음 URI 구조를 사용하여 REST API **POST** 명령으로 범위별 검사를 시작합니다.

- Subscription

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

- Resource group

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2019-10-01
  ```

호출이 **202 수락됨** 상태를 반환합니다. 응답 헤더에는 다음과 같은 형식의 **Location** 속성이 포함되어 있습니다.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2019-10-01
```

`{ResourceContainerGUID}`는 요청한 범위에 대해 정적으로 생성됩니다. 범위가 이미 주문형 검사를 실행하고 있는 경우 새 검사는 시작되지 않습니다. 대신, 새 요청은 상태에 대해 동일한 `{ResourceContainerGUID}`위**치** URI가 제공됩니다. 평가가 진행되는 동안 **위치** URI에 대한 REST API **GET** 명령은 **202 수락됨**을 반환합니다. 평가 검사가 완료되면 **200 확인** 상태를 반환합니다. 완성된 검사의 본문은 다음 상태가 포함된 JSON 응답입니다.

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>준수 작동 방식

할당에서 정책이 나 이니셔티브 규칙을 따르지 않는 리소스는 **비준수** 이며 _예외_는 아닙니다. 다음 표는 다양한 정책 효과가 결과 규정 준수 상태에 대한 조건 평가와 어떻게 작동하는지 보여줍니다.

| 리소스 상태 | 영향 | 정책 평가 | 규정 준수 상태 |
| --- | --- | --- | --- |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | 비준수 |
| Exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | 준수 |
| 새로 만들기 | Audit, AuditIfNotExist\* | True | 비준수 |
| 새로 만들기 | Audit, AuditIfNotExist\* | False | 준수 |

\* Modify, Append, DeployIfNotExist 및 AuditIfNotExist 효과를 적용 하려면 IF 문을 TRUE로 설정 해야 합니다. 또한 이 효과는 비준수가 되려면 존재 조건이 FALSE가 되어야 합니다. TRUE인 경우 IF 조건이 관련 리소스에 대한 존재 조건의 평가를 트리거합니다.

예를 들어, 공용 네트워크에 노출되는 일부 스토리지 계정(빨간색으로 강조 표시됨)이 있는 리소스 그룹이 ContosoRG라고 가정해 보겠습니다.

:::image type="complex" source="../media/getting-compliance-data/resource-group01.png" alt-text="Contoso R G 리소스 그룹의 공용 네트워크에 노출 되는 저장소 계정의 다이어그램" border="false":::
   Contoso R G 리소스 그룹의 5 개 저장소 계정에 대 한 이미지를 보여 주는 다이어그램  저장소 계정 1과 3은 파란색이 고 저장소 계정은 2, 4, 5는 빨강입니다.
:::image-end:::

이 예에서는 보안 위험에 주의해야 합니다. 이제 정책 할당을 만들었으므로 ContosoRG 리소스 그룹에 포함 된 모든 제외 저장소 계정에 대해 평가 됩니다. 이 정책 할당은 비준수 스토리지 계정 3개를 감사하여 해당 상태를 **비준수**로 변경합니다.

:::image type="complex" source="../media/getting-compliance-data/resource-group03.png" alt-text="Contoso R G 리소스 그룹의 공용 네트워크에 노출 되는 저장소 계정의 다이어그램" border="false":::
   Contoso R G 리소스 그룹의 5 개 저장소 계정에 대 한 이미지를 보여 주는 다이어그램 이제 저장소 계정 1과 3에는 아래 녹색의 체크 표시가 있으며, 저장소 계정 2, 4, 5에는 이제 아래에 빨간 경고 기호가 있습니다.
:::image-end:::

**규격** 및 **비호환**정책 외에도 정책 및 리소스에는 다음과 같은 네 가지 상태가 있습니다.

- **제외**: 리소스가 할당 범위 내에 있지만 [정의 된 예외가](../concepts/exemption-structure.md)있습니다.
- **충돌**: 규칙이 충돌 하는 둘 이상의 정책 정의가 있습니다. 예를 들어 두 정의는 서로 다른 값을 사용 하 여 동일한 태그를 추가 합니다.
- **시작되지 않음**: 정책이나 리소스에 대한 평가 주기가 시작되지 않았습니다.
- **등록되지 않음**: Azure Policy Resource Provider가 등록되지 않았거나 로그인한 계정에 규정 준수 데이터를 읽을 권한이 없습니다.

Azure Policy는 정의의 **type** 및 **name** 필드를 사용하여 리소스가 정의와 일치하는지를 확인합니다. 리소스가 일치 하면 해당 리소스는 적용 가능한 것으로 간주 되며 **규격**, 비규격 또는 **예외** **상태를 가집니다**. 정의에서 **형식** 또는 **이름** 중 하나가 유일한 속성인 경우 포함 된 모든 리소스와 제외 리소스는 적용 가능한 것으로 간주 되어 평가 됩니다.

규정 준수 비율은 _전체 리소스_별로 **규격** 및 **제외** 리소스를 분할 하 여 결정 됩니다. _총 리소스_ 는 **규격**, **비규격**, **제외**및 **충돌** 하는 리소스의 합계로 정의 됩니다. 전반적인 **규정 준수 번호** 는 모든 고유 리소스의 **합계로 나눈 고유** 리소스의 합계입니다. 아래 그림의 경우 정책을 적용할 수 있는 고유 리소스 20개 중 **비준수** 리소스는 1개뿐입니다.
전체 리소스 규정 준수 비율은 95%(20개 중 19)입니다.

:::image type="content" source="../media/getting-compliance-data/simple-compliance.png" alt-text="Contoso R G 리소스 그룹의 공용 네트워크에 노출 되는 저장소 계정의 다이어그램" border="false":::

> [!NOTE]
> Azure Policy의 규정 준수는 미리 보기 기능입니다. SDK 및 포털의 페이지에서 준수 속성은 사용 하도록 설정 된 이니셔티브와 다릅니다. 자세한 내용은 [규정 준수](../concepts/regulatory-compliance.md) 를 참조 하세요.

## <a name="portal"></a>포털

Azure Portal에서는 환경에서 준수 상태를 시각화하고 이해하는 그래픽 환경을 보여줍니다. **정책** 페이지에서 **개요** 옵션은 정책 및 이니셔티브 모두에 사용할 수 있는 준수 범위에 대한 세부 정보를 제공합니다. 준수 상태 및 할당별 개수뿐만 아니라 지난 7일 동안의 준수를 표시하는 차트가 포함됩니다. **준수** 페이지에는 차트를 제외하고 이 동일한 정보 대부분이 포함되지만 추가 필터링 및 정렬 옵션을 제공합니다.

:::image type="content" source="../media/getting-compliance-data/compliance-page.png" alt-text="Contoso R G 리소스 그룹의 공용 네트워크에 노출 되는 저장소 계정의 다이어그램" border="false":::

정책 또는 이니셔티브가 서로 다른 범위에 할당될 수 있으므로 테이블에는 각 할당의 범위 및 할당된 정의의 형식이 포함됩니다. 각 할당의 비준수 리소스 및 비준수 정책 수도 제공됩니다. 테이블의 정책 또는 이니셔티브를 선택 하면 해당 특정 할당에 대 한 준수를 자세히 확인할 있습니다.

:::image type="content" source="../media/getting-compliance-data/compliance-details.png" alt-text="Contoso R G 리소스 그룹의 공용 네트워크에 노출 되는 저장소 계정의 다이어그램" border="false":::

**리소스 준수** 탭의 리소스 목록에는 현재 할당에 대한 기존 리소스의 평가 상태가 표시됩니다. 탭에는 기본적으로 **비준수**로 표시되지만 필터링할 수 있습니다.
리소스 생성 요청에 의해 트리거되는 이벤트(추가, 감사, 거부, 배포)는 **이벤트** 탭에 표시됩니다.

> [!NOTE]
> AKS Engine 정책의 경우 표시된 리소스는 리소스 그룹입니다.

:::image type="content" source="../media/getting-compliance-data/compliance-events.png" alt-text="Contoso R G 리소스 그룹의 공용 네트워크에 노출 되는 저장소 계정의 다이어그램" border="false":::

[리소스 공급자 모드](../concepts/definition-structure.md#resource-provider-modes) 리소스의 경우 **리소스 규정 준수** 탭에서 리소스를 선택하거나 행을 마우스 오른쪽 단추로 클릭하고 **규정 준수 세부 정보 보기**를 선택하면 구성 요소 규정 준수 세부 정보가 열립니다. 이 페이지에는 이 리소스, 이벤트, 구성 요소 이벤트 및 변경 기록에 할당된 정책을 보기 위한 탭도 있습니다.

:::image type="content" source="../media/getting-compliance-data/compliance-components.png" alt-text="Contoso R G 리소스 그룹의 공용 네트워크에 노출 되는 저장소 계정의 다이어그램" border="false":::

자세한 세부 정보를 수집하려는 이벤트의 행을 마우스 오른쪽 단추로 클릭하고 **활동 로그 표시**를 선택합니다. 활동 로그 페이지가 열리고 할당 및 이벤트에 대한 세부 정보를 보여주는 검색에 대해 미리 필터링됩니다. 활동 로그는 해당 이벤트에 대한 추가 컨텍스트 및 정보를 제공합니다.

:::image type="content" source="../media/getting-compliance-data/compliance-activitylog.png" alt-text="Contoso R G 리소스 그룹의 공용 네트워크에 노출 되는 저장소 계정의 다이어그램" border="false":::

### <a name="understand-non-compliance"></a>규정 비준수 이해

리소스가 **규정 비준수**로 확인되는 데는 여러 가지 원인이 있을 수 있습니다. 리소스가 **규정 비준수**로 확인되거나 변경의 원인을 확인하려면 [비준수 확인](./determine-non-compliance.md)을 참조하세요.

## <a name="command-line"></a>명령 줄

포털에서 사용할 수 있는 정보는 REST API (with [ARMClient](https://github.com/projectkudu/ARMClient)포함), Azure PowerShell 및 Azure CLI를 사용 하 여 검색할 수 있습니다. REST API에 대한 자세한 전체 내용은 [Azure Policy Insight](/rest/api/policy-insights/) 참조를 참조하세요. REST API 참조 페이지에는 각 작업에서 브라우저에서 직접 시도할 수 있는 녹색 '시도' 단추가 있습니다.

ARMClient 또는 유사 도구를 사용하여 REST API 예제에 대한 Azure 인증을 처리합니다.

### <a name="summarize-results"></a>결과 요약

REST API를 사용하여 컨테이너, 정의 또는 할당별로 요약을 수행할 수 있습니다. Azure Policy Insight의 [구독에 대한 요약](/rest/api/policy-insights/policystates/summarizeforsubscription)을 사용하여 구독 수준에서 요약의 예제는 다음과 같습니다.

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2019-10-01
```

출력은 구독을 요약합니다. 다음 출력에서 요약된 준수는 **value.results.nonCompliantResources** 및 **value.results.nonCompliantPolicies** 아래에 있습니다. 이 요청은 각 할당에 호환되지 않는 숫자 및 정의 정보를 구성하는 각 할당을 포함한 추가 세부 정보를 제공합니다. 계층 구조의 각 정책 개체는 해당 수준에서 추가 세부 정보를 가져오는 데 사용할 수 있는 **queryResultsUri**를 제공합니다.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant'",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>리소스 쿼리

위의 예제에서 **value.policyAssignments.policyDefinitions.results.queryResultsUri**는 특정 정책 정의의 호환되지 않는 모든 리소스에 대해 샘플 URI를 제공합니다. **$Filter** 값을 살펴보면 ComplianceState가 ' 비준수 '로, PolicyAssignmentId가 정책 정의에 지정 된 다음 PolicyDefinitionId 자체에 대해 지정 됩니다. PolicyDefinitionId가 여러 다른 범위의 여러 정책 또는 이니셔티브 할당에 존재할 수 있기 때문에 필터에 PolicyAssignmentId를 포함합니다. PolicyAssignmentId 및 PolicyDefinitionId 모두를 지정하여 찾는 결과를 명시적으로 볼 수 있습니다. 이전에는 PolicyStates로 **latest**를 사용했습니다. 이 항목은 지난 24시간의 **from** 및 **to** 시간대를 자동으로 설정합니다.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=ComplianceState eq 'NonCompliant' and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

아래 예제 응답은 간단히 나타내기 위해 호환되지 않는 단일 리소스로 잘렸습니다. 자세한 응답은 리소스, 정책 또는 이니셔티브, 할당에 대한 일부 데이터를 제공합니다. 또한 정책 정의에 전달된 할당 매개 변수를 확인할 수도 있습니다.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "ComplianceState": "NonCompliant",
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>이벤트 보기

리소스가 생성되거나 업데이트되면 정책 평가 결과가 생성됩니다. 생성된 결과를 _정책 이벤트_라고 합니다. 다음 URI를 사용하여 구독과 관련된 최신 정책 이벤트를 볼 수 있습니다.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2019-10-01
```

결과는 다음 예제와 유사합니다.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

정책 이벤트를 쿼리하는 방법에 대한 자세한 내용은 [Azure Policy 이벤트](/rest/api/policy-insights/policyevents) 참조 문서를 참조합니다.

### <a name="azure-cli"></a>Azure CLI

Azure Policy에 대 한 [Azure CLI](/cli/azure/what-is-azure-cli) 명령 그룹은 REST 또는 Azure PowerShell에서 사용할 수 있는 대부분의 작업을 다룹니다. 사용 가능한 명령의 전체 목록은 [Azure CLI Azure Policy 개요](/cli/azure/policy)를 참조 하세요.

예제: 가장 많은 수의 호환되지 않는 리소스를 포함하는 가장 많이 할당된 정책에 대한 상태 요약 가져오기

```azurecli-interactive
az policy state summarize --top 1
```

응답의 위쪽 부분은 다음 예제와 같습니다.

```json
{
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
    "odataid": null,
    "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
            "policyDefinitions": [{
                "effect": "audit",
                "policyDefinitionGroupNames": [
                    ""
                ],
                "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
                "policyDefinitionReferenceId": "",
                "results": {
                    "nonCompliantPolicies": null,
                    "nonCompliantResources": 398,
                    "policyDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "policyGroupDetails": [{
                        "complianceState": "noncompliant",
                        "count": 1
                    }],
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$from=2020-07-14 14:01:22Z&$to=2020-07-15 14:01:22Z and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8' and PolicyDefinitionId eq '/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a'",
                    "resourceDetails": [{
                            "complianceState": "noncompliant",
                            "count": 398
                        },
                        {
                            "complianceState": "compliant",
                            "count": 4
                        }
                    ]
                }
            }],
    ...
```

예제: 가장 최근에 평가된 리소스에 대한 상태 레코드 가져오기(기본값은 내림차순 타임스탬프 기준).

```azurecli-interactive
az policy state list --top 1
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "Compliant",
    "effectiveParameters": "",
    "isCompliant": true,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/securitycenterbuiltin",
    "policyAssignmentName": "SecurityCenterBuiltIn",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "auditifnotexists",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionName": "aa633080-8b72-40c4-a2d7-d00c03e80bed",
    "policyDefinitionReferenceId": "identityenablemfaforownerpermissionsmonitoring",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "security center",
    "policySetDefinitionId": "/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionName": "1f3afdf9-d0c9-4c3d-847f-89da613e70a8",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "",
    "resourceId": "/subscriptions/{subscriptionId}",
    "resourceLocation": "",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Resources/subscriptions",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.903433+00:00"
  }
]
```

예제: 호환되지 않는 모든 가상 네트워크 리소스에 대한 세부 정보 가져오기

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'"
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

예제: 특정 날짜 후에 발생한 호환되지 않는 가상 네트워크 리소스와 관련된 이벤트 가져오기

```azurecli-interactive
az policy state list --filter "ResourceType eq 'Microsoft.Network/virtualNetworks'" --from '2020-07-14T00:00:00Z'
```

```json
[
  {
    "complianceReasonCode": "",
    "complianceState": "NonCompliant",
    "effectiveParameters": "",
    "isCompliant": false,
    "managementGroupIds": "{managementgroupId}",
    "odatacontext": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
    "odataid": null,
    "policyAssignmentId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/e0704696df5e4c3c81c873e8",
    "policyAssignmentName": "e0704696df5e4c3c81c873e8",
    "policyAssignmentOwner": "tbd",
    "policyAssignmentParameters": "",
    "policyAssignmentScope": "/subscriptions/{subscriptionId}",
    "policyAssignmentVersion": "",
    "policyDefinitionAction": "audit",
    "policyDefinitionCategory": "tbd",
    "policyDefinitionGroupNames": [
      ""
    ],
    "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/microsoft.authorization/policydefinitions/2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionName": "2e3197b6-1f5b-4b01-920c-b2f0a7e9b18a",
    "policyDefinitionReferenceId": "",
    "policyDefinitionVersion": "",
    "policyEvaluationDetails": null,
    "policySetDefinitionCategory": "",
    "policySetDefinitionId": "",
    "policySetDefinitionName": "",
    "policySetDefinitionOwner": "",
    "policySetDefinitionParameters": "",
    "policySetDefinitionVersion": "",
    "resourceGroup": "RG-Tags",
    "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Microsoft.Network/virtualNetworks/RG-Tags-vnet",
    "resourceLocation": "westus2",
    "resourceTags": "tbd",
    "resourceType": "Microsoft.Network/virtualNetworks",
    "subscriptionId": "{subscriptionId}",
    "timestamp": "2020-07-15T08:37:07.901911+00:00"
  }
]
```

### <a name="azure-powershell"></a>Azure PowerShell

Azure Policy용 Azure PowerShell 모듈은 PowerShell 갤러리에서 [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights)로 사용할 수 있습니다. PowerShellGet을 사용하면 `Install-Module -Name Az.PolicyInsights`(최신 [Azure PowerShell](/powershell/azure/install-az-ps)이 설치되어 있어야 함)를 사용하여 모듈을 설치할 수 있습니다.

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

모듈에는 다음 cmdlet이 있습니다.

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

예제: 가장 많은 수의 호환되지 않는 리소스를 포함하는 가장 많이 할당된 정책에 대한 상태 요약 가져오기

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

예제: 가장 최근에 평가된 리소스에 대한 상태 레코드 가져오기(기본값은 내림차순 타임스탬프 기준).

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

예제: 호환되지 않는 모든 가상 네트워크 리소스에 대한 세부 정보 가져오기

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

예: 특정 날짜 이후에 발생 한 비규격 가상 네트워크 리소스와 관련 된 이벤트 가져오기, CSV 개체로 변환 및 파일로 내보내기

```azurepowershell-interactive
$policyEvents = Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2020-09-19'
$policyEvents | ConvertTo-Csv | Out-File 'C:\temp\policyEvents.csv'
```

개체의 출력은 `$policyEvents` 다음과 같습니다.

```output
Timestamp                  : 9/19/2020 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
ComplianceState            : NonCompliant
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

**PrincipalOid** 필드는 Azure PowerShell cmdlet `Get-AzADUser`를 통해 특정 사용자를 가져오는 데 사용할 수 있습니다. **{principalOid}** 를 앞의 예제에서 가져온 응답으로 바꿉니다.

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor 로그

구독에 연결 된 활동 로그 분석 솔루션에서 [Log Analytics 작업 영역](../../../azure-monitor/log-query/log-query-overview.md) 을 사용 하는 경우 `AzureActivity` 단순 kusto 쿼리 및 테이블을 사용 하 여 새 리소스와 업데이트 된 리소스를 평가 하 여 비호환 결과를 볼 수도 있습니다 [Activity Log Analytics solution](../../../azure-monitor/platform/activity-log.md) `AzureActivity` . Azure Monitor 로그의 세부 정보를 사용하여 비준수 여부를 감시하도록 경고를 구성할 수 있습니다.

:::image type="content" source="../media/getting-compliance-data/compliance-loganalytics.png" alt-text="Contoso R G 리소스 그룹의 공용 네트워크에 노출 되는 저장소 계정의 다이어그램" border="false":::

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책을 생성](programmatically-create.md)하는 방법을 이해합니다.
- [규정 비준수 리소스를 수정](remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
