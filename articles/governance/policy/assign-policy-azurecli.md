---
title: Azure CLI를 사용하여 비준수 리소스에 대한 정책 만들기
description: Azure CLI를 사용하여 규정 비준수 리소스를 식별하는 Azure Policy 할당을 만듭니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 76f457b7d577fb2a08bbcc386328ce4d8e59f902
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979611"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-with-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 비준수 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다.
이 빠른 시작에서는 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만드는 과정을 단계별로 안내합니다.

이 과정이 끝나면 관리 디스크를 사용하지 않는 가상 머신이 식별됩니다. 이 가상 머신은 정책 할당을 *비준수*합니다.

Azure CLI는 명령줄 또는 스크립트에서 Azure 리소스를 만들고 관리하는 데 사용됩니다. 이 가이드에서는 Azure CLI를 사용하여 Azure 환경 내에서 규정 비준수 리소스를 식별하고 정책 할당을 만듭니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

이 빠른 시작을 수행하려면 Azure CLI 버전 2.0.4 이상을 실행하여 로컬로 CLI를 설치하고 사용해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Azure CLI를 사용하여 Azure Policy Insights 리소스 공급자를 등록합니다. 리소스 공급자를 등록하면 구독이 리소스 공급자와 함께 작동하는지 확인할 수 있습니다. 리소스 공급자를 등록하려면 리소스 공급자 등록 작업을 수행할 수 있는 권한이 있어야 합니다. 이 작업은 참가자 및 소유자 역할에 포함되어 있습니다. 리소스 공급자를 등록하는 다음 명령을 실행합니다.

```azurecli-interactive
az provider register --namespace 'Microsoft.PolicyInsights'
```

리소스 공급자를 등록하고 살펴보는 방법에 대한 내용은 [리소스 공급자 및 종류](../../azure-resource-manager/resource-manager-supported-services.md)를 참조하세요.

[ARMClient](https://github.com/projectkudu/ARMClient)를 아직 설치하지 않은 경우 설치합니다. Azure Resource Manager 기반 API에 HTTP 요청을 전송하는 도구입니다.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서는 정책 할당을 만들고 **관리 디스크를 사용하지 않는 VM 감사** 정의를 할당합니다. 이 정책 정의는 정책 정의에 설정된 조건을 준수하지 않는 리소스를 식별합니다.

정책 할당을 만들려면 다음 명령을 실행합니다.

```azurecli-interactive
az policy assignment create --name 'audit-vm-manageddisks' --display-name 'Audit VMs without managed disks Assignment' --scope '<scope>' --policy '<policy definition ID>'
```

이 명령은 다음 정보를 사용합니다.

- **이름** - 할당의 실제 이름입니다. 이 예제에서는 *audit-vm-manageddisks*가 사용되었습니다.
- **표시 이름** - 정책 할당에 대한 표시 이름입니다. 이 예제에서는 ‘관리 디스크 할당이 없는 VM 감사’를 사용합니다. 
- **정책** – 할당을 만드는 데 기준으로 사용되는 정책 정의 ID입니다. 이 예제에서는 ‘관리 디스크를 사용하지 않는 VM 감사’ 정책 정의의 ID입니다.  정책 정의 ID를 가져오려면 이 명령을 실행합니다. `az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks']"`
- **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 구독에서 리소스 그룹까지 다양한 범위가 있습니다. 리소스 그룹의 이름으로 &lt;범위&gt;를 바꿉니다.

## <a name="identify-non-compliant-resources"></a>규정 비준수 리소스 식별

이 새 할당에서 준수되지 않는 리소스를 보려면 다음 명령을 실행하여 정책 할당 ID를 가져옵니다.

```azurepowershell-interactive
$policyAssignment = Get-AzPolicyAssignment | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs without managed disks Assignment' }
$policyAssignment.PolicyAssignmentId
```

정책 할당 ID에 대한 자세한 내용은 [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)를 참조하세요.

다음으로 JSON 파일로 출력되는 비준수 리소스의 리소스 ID를 가져오려면 다음 명령을 실행합니다.

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2017-12-12-preview&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

결과는 다음 예제와 유사합니다.

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

결과는 Azure Portal 보기에서 **비준수 리소스** 아래 나열된 것과 비교할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

만든 할당을 제거하려면 다음 명령을 사용합니다.

```azurecli-interactive
az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 정의를 할당했습니다.

새 리소스가 규정을 준수하는지 확인하는 정책을 할당하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./tutorials/create-and-manage.md)