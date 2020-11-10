---
title: '빠른 시작: Terraform을 사용하여 새 정책 할당'
description: 이 빠른 시작에서는 Terraform 및 HCL 구문을 사용하여 비규격 리소스를 식별하는 정책 할당을 만듭니다.
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93104924"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>빠른 시작: Terraform을 사용하여 비규격 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다.
이 빠른 시작에서는 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만들고 할당하는 과정을 단계별로 안내합니다.

이 과정이 끝나면 관리 디스크를 사용하지 않는 가상 머신이 식별됩니다. 이 가상 머신은 정책 할당을 _비준수_ 합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- 사용자 환경에 구성된 [Terraform](https://www.terraform.io/) 버전 0.12.0 이상.
  자세한 내용은 [Azure Cloud Shell을 사용하여 Terraform 구성](/azure/developer/terraform/get-started-cloud-shell)을 참조하세요.
- 이 빠른 시작에서는 Azure CLI 버전 2.13.0 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>Terraform 구성, 변수 및 출력 파일 만들기

이 빠른 시작에서는 정책 할당을 만들고 **관리 디스크를 사용하지 않는 VM 감사** (`06a78e20-9358-41c9-923c-fb736d382a4d`) 정의를 할당합니다. 이 정책 정의는 정책 정의에 설정된 조건을 준수하지 않는 리소스를 식별합니다.

먼저 Terraform 구성, 변수 및 출력 파일을 구성합니다. Azure Policy에 대한 Terraform 리소스는 [Azure 공급자](https://www.terraform.io/docs/providers/azurerm/index.html)를 사용합니다.

1. `policy-assignment`라는 새 폴더를 만들고 디렉터리를 변경합니다.

1. 다음 코드로 `main.tf`를 만듭니다.

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. 다음 코드로 `variables.tf`를 만듭니다.

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 관리 그룹에서 개별 리소스에 이르기까지 다양할 수 있습니다. `{scope}`를 다음 패턴 중 하나로 바꾸어야 합니다.

   - 구독: `/subscriptions/{subscriptionId}`
   - 리소스 그룹: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - 리소스: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. 다음 코드로 `output.tf`를 만듭니다.

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Terraform 초기화 및 계획 만들기

다음으로, Terraform을 초기화하여 필요한 공급자를 다운로드한 다음, 계획을 만듭니다.

1. [terraform init](https://www.terraform.io/docs/commands/init.html) 명령을 실행합니다. 이 명령은 Terraform 구성에서 Azure 리소스 그룹을 만드는 데 필요한 Azure 모듈을 다운로드합니다.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="azurerm 모듈 및 성공 메시지 다운로드를 보여주는 terraform init 명령 실행 스크린샷.":::

1. Terraform에 대한 [Azure CLI](/cli/azure/)를 사용하여 인증합니다. 자세한 내용은 해당 [Azure 공급자: Azure CLI를 사용하여 인증](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html)을 참조하세요.

   ```bash
   az login
   ```

1. [terraform plan](https://www.terraform.io/docs/commands/plan.html) 명령 및 **out** 매개 변수를 사용하여 실행 계획을 만듭니다.

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="terraform plan 명령 및 out 매개 변수를 실행하여 생성되는 Azure 리소스를 표시하는 스크린샷.":::

   > [!NOTE]
   > 실행 계획 및 보안 유지에 대한 자세한 내용은 [Terraform 계획: 보안 경고](https://www.terraform.io/docs/commands/plan.html#security-warning)를 참조하세요.

## <a name="apply-the-terraform-execution-plan"></a>Terraform 실행 계획 적용

마지막으로 실행 계획을 적용합니다.

[terraform apply](https://www.terraform.io/docs/commands/apply.html) 명령을 실행하고 이미 생성된 `assignment.tfplan`을 지정합니다.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="terraform apply 명령 실행 및 결과 리소스 생성의 스크린샷.":::

"적용 완료! 리소스: 1개 추가됨, 0개 변경됨, 0개 소멸됨" 메시지가 표시되면 정책 할당이 생성됩니다. `outputs.tf` 파일을 정의했으므로 _할당\_id_ 도 반환됩니다.

## <a name="identify-non-compliant-resources"></a>비규격 리소스 식별

이 새 할당에서 규정을 준수하지 않는 리소스를 보려면 `terraform apply`에서 반환된 _할당\_id_ 를 사용합니다. 이를 통해 다음 명령을 실행하여 JSON 파일로 출력되는 비규격 리소스의 리소스 ID를 가져옵니다.

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
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

만든 할당을 제거하려면 Azure CLI를 사용하거나 `terraform destroy`를 사용하여 Terraform 실행 계획을 취소합니다.

- Azure CLI

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 정의를 할당했습니다.

새 리소스가 규정을 준수하는지 확인하는 정책을 할당하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./tutorials/create-and-manage.md)
