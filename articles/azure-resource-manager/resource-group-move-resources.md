---
title: Azure 리소스를 새 구독 또는 리소스 그룹으로 이동 | Microsoft Docs
description: Azure Resource Manager를 사용하여 리소스를 새 리소스 그룹 또는 구독으로 이동합니다.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/19/2019
ms.author: tomfitz
ms.openlocfilehash: 69cd6031111c72d54cb87975c2040078a9965821
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035542"
---
# <a name="move-resources-to-a-new-resource-group-or-subscription"></a>새 리소스 그룹 또는 구독으로 리소스 이동

이 문서에서는 Azure 리소스를 다른 Azure 구독 또는 동일한 구독의 다른 리소스 그룹으로 이동하는 방법을 보여 줍니다. Azure Portal, Azure PowerShell, Azure CLI 또는 REST API를 사용하여 리소스를 이동할 수 있습니다.

이동 작업 동안 원본 그룹과 대상 그룹 모두 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 리소스 그룹에서 차단됩니다. 이 잠금은 리소스 그룹에서 리소스를 추가, 업데이트, 삭제할 수 없음을 의미하지만 리소스가 고정되었음을 의미하지는 않습니다. 예를 들어, SQL Server와 해당 데이터베이스를 새 리소스 그룹으로 이동하는 경우 해당 데이터베이스를 사용하는 애플리케이션에는 가동 중지 시간이 발생하지 않습니다. 데이터베이스에 계속해서 읽고 쓸 수 있습니다.

리소스를 이동 하면 새 리소스 그룹 또는 구독으로 이동 됩니다. 리소스의 위치는 변경 되지 않습니다.

## <a name="checklist-before-moving-resources"></a>리소스를 이동하기 전의 검사 목록

리소스를 이동하기 전에 몇 가지 중요한 단계가 있습니다. 이러한 조건을 확인하면 오류를 방지할 수 있습니다.

1. 이동 하려는 리소스는 이동 작업을 지원 해야 합니다. 이동을 지 원하는 리소스 목록은 [리소스에 대 한 이동 작업 지원](move-support-resources.md)을 참조 하세요.

1. 일부 서비스에는 리소스를 이동할 때 특정 제한 사항이 나 요구 사항이 있습니다. 다음 서비스를 이동 하는 경우 이동 하기 전에 지침을 확인 하세요.

   * [App Services 이동 지침](./move-limitations/app-service-move-limitations.md)
   * [Azure DevOps Services 이동 지침](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json)
   * [클래식 배포 모델 이동 지침](./move-limitations/classic-model-move-limitations.md) -클래식 계산, 클래식 저장소, 클래식 가상 네트워크 및 Cloud Services
   * [네트워킹 이동 지침](./move-limitations/networking-move-limitations.md)
   * [Recovery Services 이동 지침](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)
   * [Virtual Machines 이동 지침](./move-limitations/virtual-machines-move-limitations.md)

1. 원본 및 대상 구독이 활성 상태여야 합니다. 사용하지 않도록 설정된 계정을 사용하도록 설정하는 과정에서 문제가 발생하면 [Azure 지원 요청을 작성](../azure-supportability/how-to-create-azure-support-request.md)하세요. 문제 유형으로 **구독 관리**를 선택합니다.

1. 원본 및 대상 구독은 동일한 [Azure Active Directory 테넌트](../active-directory/develop/quickstart-create-new-tenant.md) 내에 있어야 합니다. 두 구독이 모두 동일한 테넌트 ID를 갖는지 확인하려면 Azure PowerShell 또는 Azure CLI를 사용합니다.

   Azure PowerShell의 경우 다음을 사용합니다.

   ```azurepowershell-interactive
   (Get-AzSubscription -SubscriptionName <your-source-subscription>).TenantId
   (Get-AzSubscription -SubscriptionName <your-destination-subscription>).TenantId
   ```

   Azure CLI의 경우

   ```azurecli-interactive
   az account show --subscription <your-source-subscription> --query tenantId
   az account show --subscription <your-destination-subscription> --query tenantId
   ```

   원본 및 대상 구독에 대한 테넌트 ID가 다른 경우 다음 메서드를 사용하여 테넌트 ID를 조정합니다.

   * [Azure 구독의 소유권을 다른 계정으로 이전](../billing/billing-subscription-transfer.md)
   * [Azure Active Directory에 Azure 구독을 연결하거나 추가하는 방법](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. 이동되는 리소스의 리소스 공급자가 대상 구독에 등록되어야 합니다. 그러지 않으면 **구독이 리소스 형식에 대해 등록되지 않았음**을 알리는 오류 메시지가 표시됩니다. 해당 리소스 종류와 함께 사용된 적이 없는 새 구독으로 리소스를 이동할 때 이 오류가 표시될 수 있습니다.

   PowerShell의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

   ```azurepowershell-interactive
   Set-AzContext -Subscription <destination-subscription-name-or-id>
   Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
   ```

   리소스 공급자를 등록하려면 다음을 사용합니다.

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
   ```

   Azure CLI의 경우 다음 명령을 사용하여 등록 상태를 가져옵니다.

   ```azurecli-interactive
   az account set -s <destination-subscription-name-or-id>
   az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
   ```

   리소스 공급자를 등록하려면 다음을 사용합니다.

   ```azurecli-interactive
   az provider register --namespace Microsoft.Batch
   ```

1. 리소스를 이동시키는 계정에는 적어도 다음과 같은 권한이 있어야 합니다.

   * 원본 리소스 그룹에 대한 **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action**
   * 대상 리소스 그룹에 대한 **Microsoft.Resources/subscriptions/resourceGroups/write**

1. 리소스를 이동하기 전에 리소스를 이동하려는 구독에 대한 구독 할당량을 확인합니다. 리소스 이동 시 구독이 해당 한계를 초과하는 경우 할당량 증가를 요청할 수 있는지 여부를 검토해야 합니다. 제한의 목록 및 증가 요청 방법은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.

1. **구독 간 이동의 경우 리소스와 해당 종속 리소스는 동일한 리소스 그룹에 위치 해야 하며 함께 이동 해야 합니다.** 예를 들어 관리 디스크가 있는 VM은 다른 종속 리소스와 함께 VM 및 관리 디스크를 함께 이동 해야 합니다.

   리소스를 새 구독으로 이동 하는 경우 리소스에 종속 리소스가 있는지와 동일한 리소스 그룹에 있는지 여부를 확인 합니다. 리소스가 동일한 리소스 그룹에 없는 경우 리소스를 동일한 리소스 그룹으로 통합할 수 있는지 확인 합니다. 그렇다면 리소스 그룹 간에 이동 작업을 사용 하 여 이러한 모든 리소스를 동일한 리소스 그룹으로 가져옵니다.

   자세한 내용은 [구독 간 이동 시나리오](#scenario-for-move-across-subscriptions)를 참조 하십시오.

## <a name="scenario-for-move-across-subscriptions"></a>구독 간 이동 시나리오

한 구독에서 다른 구독으로 리소스를 이동 하는 과정은 다음 3 단계로 진행 됩니다.

![구독 간 이동 시나리오](./media/resource-group-move-resources/cross-subscription-move-scenario.png)

설명을 위해 종속 리소스가 하나만 있습니다.

* 1단계: 종속 리소스가 서로 다른 리소스 그룹에 분산 된 경우 먼저 하나의 리소스 그룹으로 이동 합니다.
* 2단계: 리소스 및 종속 리소스를 원본 구독에서 대상 구독으로 함께 이동 합니다.
* 3단계: 필요에 따라 대상 구독 내의 다른 리소스 그룹에 종속 리소스를 재배포할 수 있습니다. 

## <a name="validate-move"></a>이동 유효성 검사

[이동 작업 유효성 검사](/rest/api/resources/resources/validatemoveresources)를 수행하면 실제로 리소스를 이동하지 않고 이동 시나리오를 테스트할 수 있습니다. 이 작업을 사용 하 여 이동이 성공 하는지 확인 합니다. 이동 요청을 보내면 유효성 검사가 자동으로 호출 됩니다. 결과를 predetermine 해야 하는 경우에만이 작업을 사용 합니다. 이 작업을 실행하려면 다음이 필요합니다.

* 원본 리소스 그룹의 이름
* 대상 리소스 그룹의 리소스 ID
* 이동할 각 리소스의 리소스 ID
* 계정에 대한 [액세스 토큰](/rest/api/azure/#acquire-an-access-token)

다음 요청을 보냅니다.

```HTTP
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2019-05-10
Authorization: Bearer <access-token>
Content-type: application/json
```

다음 요청 본문을 사용합니다.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

요청 서식이 올바르게 지정되면 작업은 다음을 반환합니다.

```HTTP
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 상태 코드는 유효성 검사 요청이 수락되었음을 나타내지만 이동 작업의 성공 여부는 아직 확인하지 않은 것입니다. `location` 값에는 장기 실행 작업의 상태를 확인하는 데 사용하는 URL이 포함되어 있습니다.  

상태를 확인하려면 다음 요청을 보냅니다.

```HTTP
GET <location-url>
Authorization: Bearer <access-token>
```

작업이 실행되는 동안에는 202 상태 코드가 계속 수신됩니다. 다시 시도하기 전에 `retry-after` 값에 지정된 시간(초) 동안 대기합니다. 이동 작업 유효성 검사가 성공적으로 수행되면 204 상태 코드가 수신됩니다. 이동 유효성 검사가 실패할 경우 다음과 같은 오류 메시지가 수신됩니다.

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="use-the-portal"></a>포털 사용

리소스를 이동하려면 해당 리소스가 포함된 리소스 그룹을 선택한 후 **이동** 단추를 선택합니다.

![리소스 이동](./media/resource-group-move-resources/select-move.png)

리소스를 새 리소스 그룹으로 이동할지 또는 새 구독으로 이동할지를 선택합니다.

이동할 리소스와 대상 리소스 그룹을 선택합니다. 이러한 리소스에 대해 스크립트를 업데이트해야 함을 승인하고 **확인**을 선택합니다. 이전 단계에서 구독 편집 아이콘을 선택한 경우 대상 구독도 선택해야 합니다.

![대상 선택](./media/resource-group-move-resources/select-destination.png)

**알림**에서 이동 작업이 실행 중임을 볼 수 있습니다.

![이동 상태 표시](./media/resource-group-move-resources/show-status.png)

완료되면 결과를 알려 줍니다.

![이동 결과 표시](./media/resource-group-move-resources/show-result.png)

오류가 발생 하는 경우 [Azure 리소스를 새 리소스 그룹 또는 구독으로 이동 문제 해결](troubleshoot-move.md)을 참조 하세요.

## <a name="use-azure-powershell"></a>Azure PowerShell 사용

다른 리소스 그룹 또는 구독에 기존 리소스를 이동하려면 [Move-AzResource](/powershell/module/az.resources/move-azresource) 명령을 사용합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다.

```azurepowershell-interactive
$webapp = Get-AzResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

새 구독으로 이동하려면 `DestinationSubscriptionId` 매개 변수 값을 포함합니다.

오류가 발생 하는 경우 [Azure 리소스를 새 리소스 그룹 또는 구독으로 이동 문제 해결](troubleshoot-move.md)을 참조 하세요.

## <a name="use-azure-cli"></a>Azure CLI 사용

기존 리소스를 다른 리소스 그룹 또는 구독으로 이동하려면 [az resource move](/cli/azure/resource?view=azure-cli-latest#az-resource-move) 명령을 사용합니다. 이동할 리소스에 대한 리소스 ID를 제공합니다. 다음 예제에서는 여러 리소스를 새 리소스 그룹으로 이동하는 방법을 보여 줍니다. `--ids` 매개 변수에서 이동할 리소스 ID를 쉼표로 구분한 목록을 제공합니다.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

새 구독으로 이동하려면 `--destination-subscription-id` 매개 변수를 제공합니다.

오류가 발생 하는 경우 [Azure 리소스를 새 리소스 그룹 또는 구독으로 이동 문제 해결](troubleshoot-move.md)을 참조 하세요.

## <a name="use-rest-api"></a>REST API 사용

기존 리소스를 다른 리소스 그룹 또는 구독으로 이동 하려면 [리소스 이동](/rest/api/resources/Resources/MoveResources) 작업을 사용 합니다.

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

요청 본문에서 대상 리소스 그룹 및 이동할 리소스를 지정합니다.

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

오류가 발생 하는 경우 [Azure 리소스를 새 리소스 그룹 또는 구독으로 이동 문제 해결](troubleshoot-move.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이동을 지 원하는 리소스 목록은 [리소스에 대 한 이동 작업 지원](move-support-resources.md)을 참조 하세요.
