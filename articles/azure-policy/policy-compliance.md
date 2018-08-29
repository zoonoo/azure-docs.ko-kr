---
title: Azure Policy에서 준수 데이터 가져오기
description: Azure Policy 평가 및 효과는 준수를 결정합니다. 준수 세부 정보를 가져오는 방법을 알아봅니다.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/29/2018
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: bd3eeb5ebb9b30ac315fee1597348f3bd34f3bb6
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42142442"
---
# <a name="getting-compliance-data"></a>준수 데이터 가져오기

Azure Policy의 가장 큰 혜택 중 하나는 구독 및 구독의 [데이터 그룹](../azure-resource-manager/management-groups-overview.md)에 있는 리소스에 제공하는 정보 또는 컨트롤입니다. 이 컨트롤은 리소스가 잘못된 위치에 생성되거나, 일반적이고 일관된 태그 사용을 적용하거나, 적절한 구성 및 설정에 대한 기존 리소스를 감사하지 않도록 방지하는 다양한 방식으로 사용될 수 있습니다. 모든 경우에 환경의 준수 상태를 이해할 수 있도록 Policy에 의해 데이터가 생성됩니다.

정책 및 이니셔티브 할당에 의해 생성된 준수 정보에 액세스할 수 있는 여러 가지 방법이 있습니다.

- [Azure Portal](#portal) 사용
- [명령줄](#command_line) 스크립팅을 통함

준수를 보고하는 메서드를 살펴보기 전에 호환성 정보가 업데이트되는 시기 및 평가 주기를 트리거하는 빈도 및 이벤트를 살펴보겠습니다.

> [!WARNING]
> 준수 상태가 **‘해당 없음’** 으로 보고된 경우 [여기](azure-policy-introduction.md#rbac-permissions-in-azure-policy)에 설명된 대로 **Microsoft.PolicyInsights** 리소스 공급자가 등록되어 있고 사용자에게 적절한 RBAC(역할 기반 액세스 제어) 권한이 있는지 확인합니다.

## <a name="evaluation-triggers"></a>평가 트리거

완료된 평가 주기의 결과는 `PolicyStates` 및 `PolicyEvents` 작업을 통해 `Microsoft.PolicyInsights` 리소스 공급자에 반영됩니다. Policy Insights REST API의 옵션 및 기능에 대한 자세한 내용은 [Policy Insights](/rest/api/policy-insights/)를 참조하세요.

할당된 정책 및 이니셔티브의 평가는 다양한 이벤트의 결과로 발생합니다.

- 정책 또는 이니셔티브는 범위에 새로 할당됩니다. 이 경우에 할당이 정의된 범위에 적용되는 데 30분 정도가 걸립니다. 기능이 적용되면 새로 할당된 정책이나 이니셔티브에 대해 또는 정책이나 이니셔티브에서 사용하는 효과에 따라 해당 범위 내에서 리소스에 대한 평가 주기가 시작되고 리소스가 호환되거나 호환되지 않음으로 표시됩니다. 광범위한 리소스 범위에 대해 평가된 큰 정책 또는 이니셔티브는 시간이 걸릴 수 있습니다. 따라서 평가 주기가 완료되는 시기를 미리 정의하지 않습니다. 작업이 완료되면 업데이트된 준수 결과는 포털 및 SDK에서 지원됩니다.
- 범위에 새로 할당된 정책 또는 이니셔티브는 업데이트됩니다. 이 시나리오에 대한 평가 주기 및 타이밍은 범위에 대한 새 할당과 동일합니다.
- 리소스는 Resource Manager, REST, Azure CLI 또는 Azure PowerShell을 통해 할당된 범위에 배포됩니다. 이 시나리오에서 효과 이벤트(추가, 감사, 거부, 배포) 및 개별 리소스에 대한 호환 상태 정보는 약 15분 후에 포털 및 SDK에서 사용할 수 있습니다. 이 이벤트는 다른 리소스에 대한 평가로 이어지지 않습니다.
- 표준 준수 평가 주기입니다. 24시간마다 한 번씩 할당은 자동으로 다시 계산됩니다. 광범위한 리소스 범위에 대해 평가된 큰 정책 또는 이니셔티브는 시간이 걸릴 수 있습니다. 따라서 평가 주기가 완료되는 시기를 미리 정의하지 않습니다. 작업이 완료되면 업데이트된 준수 결과는 포털 및 SDK에서 지원됩니다.

## <a name="how-compliance-works"></a>준수 작동 방식

할당에서 리소스가 정책 또는 이니셔티브 규칙을 따르지 않으면 비준수 리소스입니다. 다음 표는 다양한 정책 효과가 결과 규정 준수 상태에 대한 조건 평가와 어떻게 작동하는지 보여줍니다.

| 리소스 상태 | 결과 | 정책 평가 | 규정 준수 상태 |
| --- | --- | --- | --- |
| exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | True | 비준수 |
| exists | Deny, Audit, Append\*, DeployIfNotExist\*, AuditIfNotExist\* | False | 준수 |
| 새로 만들기 | Audit, AuditIfNotExist\* | True | 비준수 |
| 새로 만들기 | Audit, AuditIfNotExist\* | False | 준수 |

\* Append, DeployIfNotExist 및 AuditIfNotExist 효과는 IF 문이 TRUE여야 합니다.
또한 이 효과는 비준수가 되려면 존재 조건이 FALSE가 되어야 합니다. TRUE인 경우 IF 조건이 관련 리소스에 대한 존재 조건의 평가를 트리거합니다.

예를 들어, 공용 네트워크에 노출되는 일부 저장소 계정(빨간색으로 강조 표시됨)이 있는 리소스 그룹이 ContosoRG라고 가정해 보겠습니다.

![공용 네트워크에 노출된 저장소 계정](media/policy-insights/resource-group01.png)

이 예에서는 보안 위험에 주의해야 합니다. 정책 할당을 만들었으므로 ContosoRG 리소스 그룹에서 모든 저장소 계정에 대해 평가합니다. 비준수 저장소 계정 3개를 감사한 뒤 결국 해당 상태를 **비준수**로 변경합니다.

![감사된 비준수 저장소 계정](media/policy-insights/resource-group03.png)

## <a name="portal"></a>포털

Azure Portal에서는 환경에서 준수 상태를 시각화하고 이해하는 그래픽 환경을 보여줍니다. **정책** 페이지에서 **개요** 옵션은 정책 및 이니셔티브 모두에 사용할 수 있는 준수 범위에 대한 세부 정보를 제공합니다. 준수 상태 및 할당별 개수뿐만 아니라 지난 7일 동안의 준수를 표시하는 차트가 포함됩니다. **준수** 페이지에는 차트를 제외하고 이 동일한 정보 대부분이 포함되지만 추가 필터링 및 정렬 옵션을 제공합니다.

![정책 준수 페이지](media/policy-compliance/compliance-page.png)

정책 또는 이니셔티브가 서로 다른 범위에 할당될 수 있으므로 테이블에서 해당 범위에 할당된 각 할당의 범위 및 정의의 형식을 적어둡니다. 호환되지 않는 정책 및 각 할당에 호환되지 않는 리소스 수가 제공됩니다. 테이블에서 정책 또는 이니셔티브를 클릭하면 해당하는 특정 배포에 대한 준수를 자세히 살펴봅니다.

![정책 준수 세부 정보](media/policy-compliance/compliance-details.png)

**호환 되지 않는 리소스** 탭의 리소스 목록이 현재 할당에 기존 리소스의 평가 상태를 반영하는 반면 리소스를 만드는 요청에 의해 트리거되는 이벤트(추가, 감사, 거부, 배포)는 **이벤트** 탭 아래에 표시됩니다.

![정책 준수 이벤트](media/policy-compliance/compliance-events.png)

자세한 세부 정보를 수집하려는 이벤트의 행을 마우스 오른쪽 단추로 클릭하고 **활동 로그 표시**를 선택합니다. 활동 로그 페이지가 열리고 할당 및 이벤트에 대한 세부 정보를 보여주는 검색에 대해 미리 필터링됩니다. 활동 로그는 해당 이벤트에 대한 추가 컨텍스트 및 정보를 제공합니다.

![정책 준수 활동 로그](media/policy-compliance/compliance-activitylog.png)

## <a name="command-line"></a>명령줄

REST API 또는 REST API에서 Azure PowerShell를 직접 사용하여 포털에서 제공되는 동일한 정보를 검색할 수 있습니다([ARMClient](https://github.com/projectkudu/ARMClient)에 포함). REST API에 대한 자세한 전체 내용은 [Policy Insights](/rest/api/policy-insights/) 참조를 참조하세요. REST API 참조 페이지에는 각 작업에서 브라우저에서 직접 시도할 수 있는 녹색 '시도' 단추가 있습니다.

Azure PowerShell에서 다음 예제를 사용하려면 이 예제 코드를 사용하여 인증 토큰을 생성합니다. 그런 다음, 구문 분석할 수 있는 JSON 개체를 검색하는 예제에서 원하는 문자열로 $restUri를 바꿉니다.

```azurepowershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
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

요약은 REST API를 사용하여 관리 그룹, 구독, 리소스 그룹, 리소스, 이니셔티브, 정책, 구독 수준 할당 또는 리소스 그룹 수준 할당에서 수행될 수 있습니다. Policy Insight의 [구독에 대한 요약](/rest/api/policy-insights/policystates/summarizeforsubscription)을 사용하여 구독 수준에서 요약의 예제는 다음과 같습니다.

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

위의 예제를 사용하면 **value.policyAssignments.policyDefinitions.results.queryResultsUri**는 특정 정책 정의에 대한 호환되지 않는 모든 리소스를 가져오기 위해 샘플 URI를 제공했습니다. **$filter** 값을 보면 IsCompliant는 false와 같습니다(eq). PolicyAssignmentId는 정책 정의 및 PolicyDefinitionId 자체에 대해 지정됩니다.
PolicyDefinitionId가 다양한 범위의 여러 정책 또는 이니셔티브 할당에 존재할 수 있기 때문에 필터에 PolicyAssignmentId를 포함합니다. PolicyAssignmentId 및 PolicyDefinitionId 모두를 지정하여 찾는 결과를 명시적으로 볼 수 있습니다. 이전에 PolicyStates에 **최신**을 사용했습니다(구독에 대한 요약 연산자에서 **policyStatesSummaryResource**에 허용된 값만 해당). 여기서는 지난 24시간의 **시작** 및 **종료** 기간을 자동으로 설정합니다.

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

아래 예제 응답은 호환되지 않는 단일 리소스를 간단하게 나타내도록 잘라냈습니다(@odata.count가 실제로 15이며 위의 예제에서 호환되지 않는 리소스의 수와 일치함). 자세한 응답은 리소스, 정책(또는 이니셔티브) 및 할당에 대한 일부 데이터를 제공합니다. 또한 정책 정의에 전달된 할당 매개 변수를 확인할 수도 있습니다.

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

정책 이벤트를 쿼리하는 방법에 대한 자세한 내용은 [정책 이벤트](/rest/api/policy-insights/policyevents) 참조 문서를 참조합니다.

### <a name="azure-powershell"></a>Azure PowerShell

Policy용 Azure PowerShell 모듈은 PowerShell 갤러리에서 [AzureRM.PolicyInsights](https://www.powershellgallery.com/packages/AzureRM.PolicyInsights)로 사용할 수 있습니다. PowerShellGet을 사용하면 `Install-Module -Name AzureRM.PolicyInsights`(최신 [Azure PowerShell](/powershell/azure/install-azurerm-ps)이 설치되어 있어야 함)를 사용하여 모듈을 설치할 수 있습니다.

```powershell
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name AzureRM.PolicyInsights

# Import the downloaded module
Import-Module AzureRM.PolicyInsights

# Login with Connect-AzureRmAccount if not using Cloud Shell
Connect-AzureRmAccount
```

모듈에는 세 개의 cmdlet이 있습니다.

- `Get-AzureRmPolicyStateSummary`
- `Get-AzureRmPolicyState`
- `Get-AzureRmPolicyEvent`

예제: 가장 많은 수의 호환되지 않는 리소스를 포함하는 가장 많이 할당된 정책에 대한 상태 요약 가져오기

```powershell
PS > Get-AzureRmPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

예제: 가장 최근에 평가된 리소스에 대한 상태 레코드 가져오기(기본값은 내림차순 타임스탬프 기준).

```powershell
PS > Get-AzureRmPolicyState -Top 1

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

```powershell
PS > Get-AzureRmPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

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

```powershell
PS > Get-AzureRmPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

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

**PrincipalOid** 필드는 Azure PowerShell cmdlet `Get-AzureRmADUser`를 통해 특정 사용자를 가져오는 데 사용할 수 있습니다. **{principalOid}** 를 앞의 예제에서 가져온 응답으로 바꿉니다.

```powershell
PS > (Get-AzureRmADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="log-analytics"></a>Log Analytics

`AzureActivity` 솔루션이 구독에 연결된 [Log Analytics](../log-analytics/log-analytics-overview.md) 작업 영역이 있는 경우 간단한 Kusto 쿼리 및 `AzureActivity` 테이블을 사용하여 평가 주기에서 호환되지 않는 결과를 볼 수도 있습니다. Log Analytics에서 호환되지 않는 세부 정보를 사용하여 특정 리소스, 리소스 그룹 또는 지난 24시간 동안 10개 초과의 호환되지 않는 항목의 임계값에 대한 비호환성을 감시하도록 경고를 구성할 수 있습니다.

![Log Analytics를 사용하여 정책 준수](media/policy-compliance/compliance-loganalytics.png)

## <a name="next-steps"></a>다음 단계

- [Policy 정의 구조](policy-definition.md)를 검토합니다.
- [정책 효과 이해](policy-effects.md)를 검토합니다.
- [Azure 관리 그룹으로 리소스 구성](../azure-resource-manager/management-groups-overview.md)을 포함하는 관리 그룹을 검토합니다.