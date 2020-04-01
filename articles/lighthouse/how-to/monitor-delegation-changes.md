---
title: 관리 테넌트의 위임 변경 사항 모니터링
description: 고객 테넌트에서 관리 테넌트에 이르는 위임 활동을 모니터링하는 방법을 알아봅니다.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 82c41c392210e088c85af510b9698e0140f660e5
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421900"
---
# <a name="monitor-delegation-changes-in-your-managing-tenant"></a>관리 테넌트의 위임 변경 사항 모니터링

서비스 공급자는 [Azure 위임된](../concepts/azure-delegated-resource-management.md)리소스 관리를 통해 고객 구독 또는 리소스 그룹이 테넌트에 위임되거나 이전에 위임된 리소스가 제거되는 시기를 알고 있어야 할 수 있습니다.

관리 테넌트에서 [Azure 활동 로그는](../../azure-monitor/platform/platform-logs-overview.md) 테넌트 수준에서 위임 활동을 추적합니다. 이 기록된 활동에는 모든 고객 테넌트에서 추가되거나 제거된 위임이 포함됩니다.

이 항목에서는 모든 고객에 걸쳐 테넌트에 대한 위임 활동을 모니터링하는 데 필요한 권한과 이를 위한 모범 사례에 대해 설명합니다. 또한 이 데이터를 쿼리하고 보고하는 한 가지 방법을 보여 주는 샘플 스크립트도 포함되어 있습니다.

> [!IMPORTANT]
> 이러한 모든 단계는 고객 테넌트가 아닌 관리 테넌트에서 수행해야 합니다.

## <a name="enable-access-to-tenant-level-data"></a>테넌트 수준 데이터에 대한 액세스 권한 설정

테넌트 수준 활동 로그 데이터에 액세스하려면 루트 범위(/)에서 [모니터링 리더](../../role-based-access-control/built-in-roles.md#monitoring-reader) 기본 제공 역할을 할당해야 합니다. 이 할당은 추가 액세스 권한이 있는 전역 관리자 역할이 있는 사용자가 수행해야 합니다.

### <a name="elevate-access-for-a-global-administrator-account"></a>글로벌 관리자 계정에 대한 액세스 권한 상승

루트 범위(/)에서 역할을 할당하려면 높은 액세스 권한이 있는 전역 관리자 역할이 있어야 합니다. 이 높은 액세스는 역할 할당을 수행해야 하는 경우에만 추가된 다음 작업이 완료되면 제거되어야 합니다.

권한 상승 추가 및 제거에 대한 자세한 지침은 [액세스 상승을 참조하여 모든 Azure 구독 및 관리 그룹을 관리합니다.](../../role-based-access-control/elevate-access-global-admin.md)

액세스 권한을 상승하면 계정에 루트 범위에서 Azure의 사용자 액세스 관리자 역할이 표시됩니다. 이 역할 할당을 사용하면 디렉터리에서 모든 리소스를 보고 구독 또는 관리 그룹에서 액세스를 할당할 수 있을 뿐만 아니라 루트 범위에서 역할 할당을 수행할 수 있습니다. 

### <a name="create-a-new-service-principal-account-to-access-tenant-level-data"></a>테넌트 수준 데이터에 액세스하기 위해 새 서비스 주체 계정 만들기

액세스 권한을 높이면 테넌트 수준 활동 로그 데이터를 쿼리할 수 있도록 계정에 적절한 권한을 할당할 수 있습니다. 이 계정에는 모니터링 [리더](../../role-based-access-control/built-in-roles.md#monitoring-reader) 기본 제공 역할이 관리 테넌트의 루트 범위에 할당되어 있어야 합니다.

> [!IMPORTANT]
> 루트 범위에서 역할 할당을 부여하면 테넌트의 모든 리소스에 동일한 사용 권한이 적용됩니다.

광범위한 액세스 수준이기 때문에 이 역할을 개별 사용자나 그룹에 할당하는 것이 아니라 서비스 주 체 계정에 할당하는 것이 좋습니다. 또한 다음과 같은 모범 사례를 권장합니다.

- 이 역할을 다른 자동화에 사용되는 기존 서비스 주체에 할당하는 대신 이 함수에만 사용할 [새 서비스 주체 계정을 만듭니다.](../../active-directory/develop/howto-create-service-principal-portal.md)
- 이 서비스 주체가 위임된 고객 리소스에 액세스할 수 없는지 확인합니다.
- [인증서를 사용하여 Azure](../../active-directory/develop/howto-create-service-principal-portal.md#certificates-and-secrets) [Key Vault에 인증서를 안전하게](../../key-vault/key-vault-best-practices.md)인증하고 저장합니다.
- 서비스 주체를 대신하여 조치를 사용할 수 있는 사용자를 제한합니다.

다음 방법 중 하나를 사용하여 루트 범위를 할당합니다.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

New-AzRoleAssignment -SignInName <yourLoginName> -Scope "/" -RoleDefinitionName "Monitoring Reader"  -ApplicationId $servicePrincipal.ApplicationId 
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az role assignment create --assignee 00000000-0000-0000-0000-000000000000 --role "Monitoring Reader" --scope "/"
```

### <a name="remove-elevated-access-for-the-global-administrator-account"></a>글로벌 관리자 계정에 대한 높은 액세스 제거

서비스 주 체 계정을 만들고 루트 범위에서 모니터링 리더 역할을 할당한 후에는 이 액세스 수준이 더 이상 필요하지 므로 전역 관리자 계정에 대한 높은 액세스 권한을 [제거해야](../../role-based-access-control/elevate-access-global-admin.md#remove-elevated-access) 합니다.

## <a name="query-the-activity-log"></a>활동 로그 쿼리

모니터링 리더가 관리 테넌트의 루트 범위에 대한 액세스 권한을 가진 새 서비스 주체 계정을 만든 후에는 테넌트의 위임 활동을 쿼리하고 보고하는 데 사용할 수 있습니다. 

[이 Azure PowerShell 스크립트는](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/tools/monitor-delegation-changes) 추가되거나 제거된 위임(또는 성공하지 않은 시도)에 대한 지난 1일 간의 활동 및 보고서를 쿼리하는 데 사용할 수 있습니다. [테넌트 활동 로그](https://docs.microsoft.com/rest/api/monitor/TenantActivityLogs/List) 데이터를 쿼리한 다음 다음 값을 생성하여 추가되거나 제거된 위임에 대해 보고합니다.

- **위임된 ResourceId**: 위임된 구독 또는 리소스 그룹의 ID
- **고객테넌티드**: 고객 테넌트 ID
- **CustomerSubscriptionId**: 위임되었지만 위임된 리소스 그룹을 포함하는 구독 ID
- **CustomerDelegateStatus**: 위임된 리소스의 상태 변경(성공 또는 실패)
- **이벤트타임스탬프**: 위임 변경이 기록된 날짜 및 시간

이 데이터를 쿼리할 때 다음 사항을 염두에 두어야 합니다.

- 단일 배포에서 여러 리소스 그룹이 위임된 경우 각 리소스 그룹에 대해 별도의 항목이 반환됩니다.
- 이전 위임에 대한 변경 내용(예: 권한 구조 업데이트)은 추가 위임으로 기록됩니다.
- 위에서 설명한 것처럼 이 테넌트 수준 데이터에 액세스하려면 계정이 루트 범위(/)에 모니터링 리더 기본 제공 역할이 있어야 합니다.
- 이 데이터를 사용자 고유의 워크플로 및 보고에 사용할 수 있습니다. 예를 들어 [HTTP 데이터 수집기 API(공개 미리 보기)를](../../azure-monitor/platform/data-collector-api.md) 사용하여 REST API 클라이언트에서 Azure Monitor에 데이터를 로그한 다음 [작업 그룹을](../../azure-monitor/platform/action-groups.md) 사용하여 알림 또는 경고를 만들 수 있습니다.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Azure Lighthouse: Query Tenant Activity Log for registered/unregistered delegations for the past 1 day

$GetDate = (Get-Date).AddDays((-1))

$dateFormatForQuery = $GetDate.ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ssZ")

# Getting Azure context for the API call
$currentContext = Get-AzContext

# Fetching new token
$azureRmProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = [Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient]::new($azureRmProfile)
$token = $profileClient.AcquireAccessToken($currentContext.Tenant.Id)

$listOperations = @{
    Uri = "https://management.azure.com/providers/microsoft.insights/eventtypes/management/values?api-version=2015-04-01&`$filter=eventTimestamp ge '$($dateFormatForQuery)'"
    Headers = @{
        Authorization = "Bearer $($token.AccessToken)"
        'Content-Type' = 'application/json'
    }
    Method = 'GET'
}
$list = Invoke-RestMethod @listOperations
$showOperations = $list.value

if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/register/action")
{
    $registerOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/register/action"}
    foreach ($registerOutput in $registerOutputs)
    {
    $registerOutputdata = [pscustomobject]@{
        Event = "An Azure customer has delegated resources to your tenant";
        DelegatedResourceId = $registerOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $registerOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $registerOutput.subscriptionId;
        CustomerDelegationStatus = $registerOutput.status.value;
        EventTimeStamp = $registerOutput.eventTimestamp;
        }
        $registerOutputdata | Format-List
    }
}
if ($showOperations.operationName.value -eq "Microsoft.Resources/tenants/unregister/action") 
{
    $unregisterOutputs  = $showOperations | Where-Object -FilterScript {$_.eventName.value -eq "EndRequest" -and $_.resourceType.value -and $_.operationName.value -eq "Microsoft.Resources/tenants/unregister/action"}
    foreach ($unregisterOutput in $unregisterOutputs)
    {
    $unregisterOutputdata = [pscustomobject]@{
        Event = "An Azure customer has removed delegated resources from your tenant";
        DelegatedResourceId = $unregisterOutput.description |%{$_.split('"')[11]};
        CustomerTenantId = $unregisterOutput.description |%{$_.split('"')[7]};
        CustomerSubscriptionId = $unregisterOutput.subscriptionId;
        CustomerDelegationStatus = $unregisterOutput.status.value;
        EventTimeStamp = $unregisterOutput.eventTimestamp;
        }
        $unregisterOutputdata | Format-List
    }
}
else 
{
    Write-Output "No new delegation changes."
}   


```

## <a name="next-steps"></a>다음 단계

- [Azure 위임 리소스 관리에](../concepts/azure-delegated-resource-management.md)고객을 온보딩하는 방법에 대해 알아봅니다.
- Azure [모니터](../../azure-monitor/index.yml) 및 [Azure 활동 로그에](../../azure-monitor/platform/platform-logs-overview.md)대해 자세히 알아보기.
