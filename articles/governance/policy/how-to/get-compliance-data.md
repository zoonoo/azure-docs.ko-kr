---
title: 정책 준수 데이터 가져오기
description: Azure Policy 평가 및 효과는 준수를 결정합니다. 준수 세부 정보를 가져오는 방법을 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/01/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 428a1614889409300064420e1d3d4fbc0423a0ec
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237522"
---
# <a name="get-compliance-data-of-azure-resources"></a>Azure 리소스의 준수 데이터 가져오기

Azure Policy의 가장 큰 혜택 중 하나는 구독 및 구독의 [데이터 그룹](../../management-groups/overview.md)에 있는 리소스에 제공하는 정보 또는 컨트롤입니다. 이 컨트롤은 리소스가 잘못된 위치에 생성되거나, 일반적이고 일관된 태그 사용을 적용하거나, 적절한 구성 및 설정에 대한 기존 리소스를 감사하지 않도록 방지하는 다양한 방식으로 사용될 수 있습니다. 모든 경우에 데이터는 사용자 환경의 준수 상태를 파악할 수 있도록 Azure 정책에 의해 생성 됩니다.

정책 및 이니셔티브 할당에 의해 생성된 준수 정보에 액세스할 수 있는 여러 가지 방법이 있습니다.

- [Azure Portal](#portal) 사용
- [명령줄](#command-line) 스크립팅을 통함

준수를 보고하는 메서드를 살펴보기 전에 호환성 정보가 업데이트되는 시기 및 평가 주기를 트리거하는 빈도 및 이벤트를 살펴보겠습니다.

> [!WARNING]
> 으로 준수 상태로 보고 되는 경우 **등록 되어 있지**, 되어 있는지 확인 합니다 **Microsoft.PolicyInsights** 리소스 공급자가 등록 되어 있고 사용자는 적절 한 역할 기반 액세스 제어 ( 에 설명 된 대로 RBAC) 사용 권한 [Azure Policy에 대 한 RBAC](../overview.md#rbac-permissions-in-azure-policy)합니다.

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="evaluation-triggers"></a>평가 트리거

완료된 평가 주기의 결과는 `PolicyStates` 및 `PolicyEvents` 작업을 통해 `Microsoft.PolicyInsights` 리소스 공급자에서 사용할 수 있습니다. Azure Policy Insights REST API의 작업에 대 한 자세한 내용은 참조 하세요. [Azure Policy Insights](/rest/api/policy-insights/)합니다.

할당된 정책 및 이니셔티브의 평가는 다양한 이벤트의 결과로 발생합니다.

- 정책 또는 이니셔티브는 범위에 새로 할당됩니다. 할당이 정의된 범위에 적용되는 데 30분 정도가 걸립니다. 기능이 적용되면 새로 할당된 정책이나 이니셔티브에 대해 또는 정책이나 이니셔티브에서 사용하는 효과에 따라 해당 범위 내에서 리소스에 대한 평가 주기가 시작되고 리소스가 호환되거나 호환되지 않음으로 표시됩니다. 대량의 리소스에 대해 대규모 정책 또는 이니셔티브를 평가하는 데는 시간이 걸릴 수 있습니다. 따라서 평가 주기 완료 시점을 미리 예측할 수 없습니다. 작업이 완료되면 업데이트된 준수 결과는 포털 및 SDK에서 지원됩니다.

- 범위에 새로 할당된 정책 또는 이니셔티브는 업데이트됩니다. 이 시나리오에 대한 평가 주기 및 타이밍은 범위에 대한 새 할당과 동일합니다.

- 리소스는 Resource Manager, REST, Azure CLI 또는 Azure PowerShell을 통해 할당된 범위에 배포됩니다. 이 시나리오에서 효과 이벤트(추가, 감사, 거부, 배포) 및 개별 리소스에 대한 호환 상태 정보는 약 15분 후에 포털 및 SDK에서 사용할 수 있습니다. 이 이벤트는 다른 리소스에 대한 평가로 이어지지 않습니다.

- 표준 준수 평가 주기입니다. 24시간마다 한 번씩 할당은 자동으로 다시 계산됩니다. 많은 리소스에 대해 대규모 정책 또는 이니셔티브를 평가하는 데는 시간이 걸릴 수 있습니다. 따라서 평가 주기 완료 시점을 미리 예측할 수 없습니다. 작업이 완료되면 업데이트된 준수 결과는 포털 및 SDK에서 지원됩니다.

- [게스트 구성](../concepts/guest-configuration.md) 리소스 공급자는 관리형 리소스를 통해 규정 준수 세부 정보로 업데이트됩니다.

- 주문형 검사

### <a name="on-demand-evaluation-scan"></a>주문형 평가 검사

REST API 호출로 구독 또는 리소스 그룹에 대한 평가 검사를 시작할 수 있습니다. 이 검사는 비동기 프로세스입니다. 따라서 검사를 시작하는 REST 엔드포인트는 검사가 응답을 완료할 때까지 기다리지 않습니다. 대신, URI를 제공하여 요청된 평가의 상태를 쿼리합니다.

각 REST API URI에는 사용자가 자신의 값으로 대체해야 하는 변수가 있습니다.

- `{YourRG}` - 사용자의 리소스 그룹 이름으로 대체
- `{subscriptionId}` - 사용자의 구독 ID로 대체

검사는 구독 또는 리소스 그룹에서 리소스의 평가를 지원합니다. 다음 URI 구조를 사용하여 REST API **POST** 명령으로 범위별 검사를 시작합니다.

- 구독

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- 리소스 그룹

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

호출이 **202 수락됨** 상태를 반환합니다. 응답 헤더에는 다음과 같은 형식의 **Location** 속성이 포함되어 있습니다.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}`는 요청한 범위에 대해 정적으로 생성됩니다. 범위가 이미 주문형 검사를 실행하고 있는 경우 새 검사는 시작되지 않습니다. 대신, 새 요청은 상태에 대해 동일한 `{ResourceContainerGUID}` **위치** URI가 제공됩니다. 평가가 진행되는 동안 **위치** URI에 대한 REST API **GET** 명령은 **202 수락됨**을 반환합니다. 평가 검사가 완료되면 **200 확인** 상태를 반환합니다. 완성된 검사의 본문은 다음 상태가 포함된 JSON 응답입니다.

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>준수 작동 방식

할당에서 정책 또는 이니셔티브 규칙을 따르지 않는 리소스는 **비준수** 리소스입니다.
다음 표는 다양한 정책 효과가 결과 규정 준수 상태에 대한 조건 평가와 어떻게 작동하는지 보여줍니다.

| 리소스 상태 | 결과 | 정책 평가 | 규정 준수 상태 |
| --- | --- | --- | --- |
| exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True  | 비준수 |
| exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | 거짓 | 준수 |
| 새 문자 | Audit, AuditIfNotExist\* | True  | 비준수 |
| 새 문자 | Audit, AuditIfNotExist\* | 거짓 | 준수 |

\* Append, DeployIfNotExist 및 AuditIfNotExist 효과는 IF 문이 TRUE여야 합니다.
또한 이 효과는 비준수가 되려면 존재 조건이 FALSE가 되어야 합니다. TRUE인 경우 IF 조건이 관련 리소스에 대한 존재 조건의 평가를 트리거합니다.

예를 들어, 공용 네트워크에 노출되는 일부 저장소 계정(빨간색으로 강조 표시됨)이 있는 리소스 그룹이 ContosoRG라고 가정해 보겠습니다.

![공용 네트워크에 노출된 저장소 계정](../media/getting-compliance-data/resource-group01.png)

이 예에서는 보안 위험에 주의해야 합니다. 정책 할당을 만들었으므로 ContosoRG 리소스 그룹에서 모든 스토리지 계정에 대해 평가합니다. 이 정책 할당은 비준수 저장소 계정 3개를 감사하여 해당 상태를 **비준수**로 변경합니다.

![감사된 비준수 저장소 계정](../media/getting-compliance-data/resource-group03.png)

정책과 리소스에는 **준수** 및 **비준수** 외에도 다음의 세 가지 상태 중 하나일 수 있습니다.

- **충돌**: 충돌하는 규칙이 있는 두 개 이상의 정책이 있습니다. 예를 들어 두 개의 정책이 다른 값을 갖는 동일한 태그에 추가되어 있을 수 있습니다.
- **시작되지 않음**: 정책이나 리소스에 대한 평가 주기가 시작되지 않았습니다.
- **등록되지 않음**: Azure Policy Resource Provider가 등록되지 않았거나 로그인한 계정에 규정 준수 데이터를 읽을 권한이 없습니다.

Azure Policy를 사용 합니다 **형식** 및 **이름** 리소스 일치 하는 항목 인지 확인 하려면 정의에 필드. 리소스가 일치하는 경우 적용 가능하며 **준수** 또는 **비준수** 상태로 간주됩니다. 정의의 속성이 **type** 또는 **name**뿐이면 모든 리소스는 정책을 적용할 수 있는 리소스로 간주되어 평가됩니다.

**준수** 상태의 리소스를 _총 리소스 수_로 나누는 방법을 통해 규정 준수 비율이 결정됩니다.
_총 리소스 수_는 **준수**, **비준수** 및 **충돌** 상태의 리소스 수를 합한 값으로 정의됩니다. 전체 규정 준수 리소스 수는 **준수** 상태인 고유 리소스의 합을 모든 고유 리소스의 합으로 나눈 결과입니다. 아래 그림의 경우 정책을 적용할 수 있는 고유 리소스 20개 중 **비준수** 리소스는 1개뿐입니다. 전체 리소스 규정 준수 비율은 95%(20개 중 19)입니다.

![정책 준수 규정 준수 페이지의 예제](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>포털

Azure Portal에서는 환경에서 준수 상태를 시각화하고 이해하는 그래픽 환경을 보여줍니다. **정책** 페이지에서 **개요** 옵션은 정책 및 이니셔티브 모두에 사용할 수 있는 준수 범위에 대한 세부 정보를 제공합니다. 준수 상태 및 할당별 개수뿐만 아니라 지난 7일 동안의 준수를 표시하는 차트가 포함됩니다. **준수** 페이지에는 차트를 제외하고 이 동일한 정보 대부분이 포함되지만 추가 필터링 및 정렬 옵션을 제공합니다.

![Azure 정책 준수 페이지의 예](../media/getting-compliance-data/compliance-page.png)

정책 또는 이니셔티브가 서로 다른 범위에 할당될 수 있으므로 테이블에는 각 할당의 범위 및 할당된 정의의 형식이 포함됩니다. 각 할당의 비준수 리소스 및 비준수 정책 수도 제공됩니다. 테이블에서 정책 또는 이니셔티브를 클릭하면 해당하는 특정 배포에 대한 준수를 자세히 살펴봅니다.

![Azure 정책 준수 세부 정보 페이지의 예](../media/getting-compliance-data/compliance-details.png)

**리소스 준수** 탭의 리소스 목록에는 현재 할당에 대한 기존 리소스의 평가 상태가 표시됩니다. 탭에는 기본적으로 **비준수**로 표시되지만 필터링할 수 있습니다.
리소스 생성 요청에 의해 트리거되는 이벤트(추가, 감사, 거부, 배포)는 **이벤트** 탭에 표시됩니다.

![Azure 정책 준수 이벤트의 예](../media/getting-compliance-data/compliance-events.png)

자세한 세부 정보를 수집하려는 이벤트의 행을 마우스 오른쪽 단추로 클릭하고 **활동 로그 표시**를 선택합니다. 활동 로그 페이지가 열리고 할당 및 이벤트에 대한 세부 정보를 보여주는 검색에 대해 미리 필터링됩니다. 활동 로그는 해당 이벤트에 대한 추가 컨텍스트 및 정보를 제공합니다.

![Azure Policy 준수 활동 로그의 예](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>호환 되지 않는 이해

<a name="change-history-preview"></a>

리소스를 것으로 확인 되는 경우 **호환 되지 않는**, 여러 가지 가능한 이유가 있습니다. 리소스는 이유를 확인 **비준수** 변경 책임, 참조 또는 [호환 되지 않는 확인](./determine-non-compliance.md)합니다.

## <a name="command-line"></a>명령 줄

포털에 제공되는 동일한 정보를 REST API([ARMClient](https://github.com/projectkudu/ARMClient)에 포함) 또는 Azure PowerShell을 통해 검색할 수 있습니다. REST API에 대 한 전체 내용은 참조는 [Azure Policy Insights](/rest/api/policy-insights/) 참조 합니다. REST API 참조 페이지에는 각 작업에서 브라우저에서 직접 시도할 수 있는 녹색 '시도' 단추가 있습니다.

Azure PowerShell에서 다음 예제를 사용하려면 이 예제 코드를 사용하여 인증 토큰을 생성합니다. 그런 다음, 구문 분석할 수 있는 JSON 개체를 검색하는 예제에서 문자열로 $restUri를 바꿉니다.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Define the REST API to communicate with
# Use double quotes for $restUri as some endpoints take strings passed in single quotes
$restUri = "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04"

# Invoke the REST API
$response = Invoke-RestMethod -Uri $restUri -Method POST -Headers $authHeader

# View the response object (as JSON)
$response
```

### <a name="summarize-results"></a>결과 요약

REST API를 사용하여 컨테이너, 정의 또는 할당별로 요약을 수행할 수 있습니다. Azure 정책 Insight를 사용 하 여 구독 수준에서 요약의 예로 [구독에 대 한 요약](/rest/api/policy-insights/policystates/summarizeforsubscription):

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
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
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>리소스 쿼리

위의 예제에서 **value.policyAssignments.policyDefinitions.results.queryResultsUri**는 특정 정책 정의의 호환되지 않는 모든 리소스에 대해 샘플 URI를 제공합니다. **$filter** 값을 보면 IsCompliant는 false와 같습니다(eq). PolicyAssignmentId는 정책 정의 및 PolicyDefinitionId 자체에 대해 지정됩니다. PolicyDefinitionId가 여러 다른 범위의 여러 정책 또는 이니셔티브 할당에 존재할 수 있기 때문에 필터에 PolicyAssignmentId를 포함합니다. PolicyAssignmentId 및 PolicyDefinitionId 모두를 지정하여 찾는 결과를 명시적으로 볼 수 있습니다. 이전에는 PolicyStates로 **latest**를 사용했습니다. 이 항목은 지난 24시간의 **from** 및 **to** 시간대를 자동으로 설정합니다.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
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
        "isCompliant": false,
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
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
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

정책 이벤트를 쿼리 하는 방법에 대 한 자세한 내용은 참조는 [Azure 정책 이벤트](/rest/api/policy-insights/policyevents) 참조 문서입니다.

### <a name="azure-powershell"></a>Azure PowerShell

으로 PowerShell 갤러리에서 Azure Policy에 대 한 Azure PowerShell 모듈을 사용할 수 [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights)합니다.
PowerShellGet을 사용하면 `Install-Module -Name Az.PolicyInsights`(최신 [Azure PowerShell](/powershell/azure/install-az-ps)이 설치되어 있어야 함)를 사용하여 모듈을 설치할 수 있습니다.

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
IsCompliant                : False
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
IsCompliant                : False
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

예제: 특정 날짜 후에 발생한 호환되지 않는 가상 네트워크 리소스와 관련된 이벤트 가져오기

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
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

있는 경우는 [Log Analytics 작업 영역](../../../log-analytics/log-analytics-overview.md) 사용 하 여 `AzureActivity` 에서 합니다 [Activity Log Analytics 솔루션](../../../azure-monitor/platform/activity-log-collect.md) 구독에 연결을 확인할 수도 있습니다 사용 하 여 평가 주기에서 호환 되지 않는 결과 간단한 Kusto 쿼리 및 `AzureActivity` 테이블입니다. Azure Monitor 로그의 세부 정보를 사용하여 비준수 여부를 감시하도록 경고를 구성할 수 있습니다.


![Azure Monitor 로그를 사용 하 여 azure 정책 준수](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>다음 단계

- 예제를 검토 [Azure Policy 샘플](../samples/index.md)합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- 이해 하는 방법 [프로그래밍 방식으로 정책 만들기](programmatically-create.md)합니다.
- 설명 하는 방법 [비준수 리소스를 수정](remediate-resources.md)합니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.