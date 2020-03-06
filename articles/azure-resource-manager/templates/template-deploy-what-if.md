---
title: 템플릿 배포 정의 (미리 보기)
description: Azure Resource Manager 템플릿을 배포 하기 전에 리소스에 대해 수행 되는 변경 내용을 확인 합니다.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388540"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>리소스 관리자 템플릿 배포 가상 작업 (미리 보기)

템플릿을 배포 하기 전에 발생 하는 변경 내용을 미리 볼 수 있습니다. Azure Resource Manager은 템플릿을 배포할 때 리소스가 어떻게 변경 되는지 확인할 수 있는 가상 작업을 제공 합니다. 가상 작업은 기존 리소스를 변경 하지 않습니다. 대신, 지정 된 템플릿이 배포 되는 경우 변경 내용을 예측 합니다.

> [!NOTE]
> 가상 작업 인 경우 현재 미리 보기 상태입니다. 이를 사용하려면 [미리 보기에 가입](https://aka.ms/armtemplatepreviews)해야 합니다. 미리 보기 릴리스로 인해 실제로 변경이 발생 하지 않을 때 리소스가 변경 되는 것을 보여 주는 경우도 있습니다. 이러한 문제를 줄이기 위해 노력 하 고 있지만 도움이 필요 합니다. [https://aka.ms/whatifissues](https://aka.ms/whatifissues)에서 이러한 문제를 보고 하세요.

가상 작업은 PowerShell 명령 또는 REST API 작업에 사용할 수 있습니다.

PowerShell에서 출력에는 다양 한 유형의 변경 내용을 확인 하는 데 도움이 되는 색으로 구분 된 결과가 포함 됩니다.

![리소스 관리자 템플릿 배포 가상 작업 fullresourcepayload 및 형식 변경](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Ouptput 텍스트는 다음과 같습니다.

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>가상 명령

가상 작업에 Azure PowerShell 또는 Azure REST API를 사용할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell

템플릿을 배포 하기 전에 변경 내용에 대 한 미리 보기를 보려면 배포 명령에 `-Whatif` 스위치 매개 변수를 추가 합니다.

* 리소스 그룹 배포에 대 한 `New-AzResourceGroupDeployment -Whatif`
* 구독 수준 배포에 대 한 `New-AzSubscriptionDeployment -Whatif` 및 `New-AzDeployment -Whatif`

또는 `-Confirm` 스위치 매개 변수를 사용 하 여 변경 내용을 미리 보고 배포를 계속 하 라는 메시지를 받을 수 있습니다.

* 리소스 그룹 배포에 대 한 `New-AzResourceGroupDeployment -Confirm`
* 구독 수준 배포에 대 한 `New-AzSubscriptionDeployment -Confirm` 및 `New-AzDeployment -Confirm`

이전 명령은 수동으로 검사할 수 있는 텍스트 요약을 반환 합니다. 프로그래밍 방식으로 변경 내용을 검사할 수 있는 개체를 가져오려면 다음을 사용 합니다.

* 리소스 그룹 배포에 대 한 `$results = Get-AzResourceGroupDeploymentWhatIf`
* 구독 수준 배포에 대 한 `$results = Get-AzSubscriptionDeploymentWhatIf` 또는 `$results = Get-AzDeploymentWhatIf`

> [!NOTE]
> 버전 2.0.1-alpha5의 릴리스 이전에는 `New-AzDeploymentWhatIf` 명령을 사용 했습니다. 이 명령은 `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf`및 `Get-AzSubscriptionDeploymentWhatIf` 명령으로 대체 되었습니다. 이전 버전을 사용한 경우 해당 구문을 업데이트 해야 합니다. `-ScopeType` 매개 변수가 제거 되었습니다.

### <a name="azure-rest-api"></a>Azure REST API

REST API의 경우 다음을 사용 합니다.

* [배포-](/rest/api/resources/deployments/whatif) 리소스 그룹 배포에 대 한 What If
* 배포-구독 수준 배포에 대 한 [구독 범위에서 What If](/rest/api/resources/deployments/whatifatsubscriptionscope)

## <a name="change-types"></a>변경 유형

가상 작업은 다음과 같은 6 가지 유형의 변경 내용을 나열 합니다.

- **만들기**: 리소스가 현재 존재 하지는 않지만 템플릿에 정의 되어 있습니다. 리소스가 생성 됩니다.

- **Delete**:이 변경 형식은 배포에 [전체 모드](deployment-modes.md) 를 사용 하는 경우에만 적용 됩니다. 리소스가 있지만 템플릿에 정의 되어 있지 않습니다. 완료 모드에서는 리소스가 삭제 됩니다. [완료 모드 삭제를 지 원하는](complete-mode-deletion.md) 리소스만이 변경 형식에 포함 됩니다.

- **Ignore**: 리소스가 있지만 템플릿에 정의 되어 있지 않습니다. 리소스는 배포 또는 수정 되지 않습니다.

- **NoChange**: 리소스가 있고 템플릿에 정의 되어 있습니다. 리소스는 다시 배포 되지만 리소스의 속성은 변경 되지 않습니다. [Resultformat](#result-format) 이 기본값 인 `FullResourcePayloads`으로 설정 된 경우이 변경 유형이 반환 됩니다.

- **Modify**: 리소스가 있으며 템플릿에 정의 되어 있습니다. 리소스를 다시 배포 하면 리소스의 속성이 변경 됩니다. [Resultformat](#result-format) 이 기본값 인 `FullResourcePayloads`으로 설정 된 경우이 변경 유형이 반환 됩니다.

- **배포**: 리소스가 있으며 템플릿에서 정의 됩니다. 리소스가 다시 배포 됩니다. 리소스의 속성은 변경 될 수도 있고 변경 되지 않을 수도 있습니다. 작업은 속성이 변경 되는지 여부를 확인 하는 데 충분 한 정보가 없는 경우이 변경 형식을 반환 합니다. [Resultformat](#result-format) 이 `ResourceIdOnly`로 설정 된 경우에만이 조건이 표시 됩니다.

## <a name="result-format"></a>결과 형식

예측 된 변경 내용에 대해 반환 되는 세부 정보 수준을 제어할 수 있습니다. 배포 명령 (`New-Az*Deployment`)에서 **-whatifresultformat** 매개 변수를 사용 합니다. 프로그래밍 개체 명령 (`Get-Az*DeploymentWhatIf`)에서 **Resultformat** 매개 변수를 사용 합니다.

Format 매개 변수를 **FullResourcePayloads** 로 설정 하 여 변경할 리소스의 목록과 변경 될 속성에 대 한 세부 정보를 가져옵니다. Format 매개 변수를 **ResourceIdOnly** 로 설정 하 여 변경 될 리소스의 목록을 가져옵니다. 기본값은 **FullResourcePayloads**입니다.  

다음 결과는 두 가지 출력 형식을 보여 줍니다.

- 전체 리소스 페이로드

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- 리소스 ID만

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>What-if 작업 실행

### <a name="set-up-environment"></a>환경 설정

가 작동 하는 방식을 확인 하기 위해 일부 테스트를 실행 해 보겠습니다. 먼저 [가상 네트워크를 만드는 템플릿을](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json)배포 합니다. 이 가상 네트워크를 사용 하 여 변경 내용이 어떻게 보고 되는지 테스트할 수 있습니다.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>테스트 수정

배포가 완료 되 면 가상 작업을 테스트할 준비가 된 것입니다. 이번에는 [가상 네트워크를 변경 하는 템플릿을](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)배포 합니다. 원본 태그 중 하나가 누락 되 고, 서브넷이 제거 되 고, 주소 접두사가 변경 되었습니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

가상의 출력은 다음과 유사 하 게 표시 됩니다.

![리소스 관리자 템플릿 배포 가상 작업 출력](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

텍스트 출력은 다음과 같습니다.

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

출력의 위쪽에서 색이 변경 형식을 나타내는 것으로 정의 됩니다.

출력 아래쪽에는 태그 소유자가 삭제 되었음을 표시 합니다. 주소 접두사가 10.0.0.0/16에서 10.0.0.0/15로 변경 되었습니다. Subnet001 라는 서브넷이 삭제 되었습니다. 이 변경 내용은 실제로 배포 되지 않았습니다. 템플릿을 배포 하는 경우 발생 하는 변경 내용에 대 한 미리 보기가 표시 됩니다.

삭제 된 것으로 표시 되는 일부 속성은 실제로 변경 되지 않습니다. 속성은 템플릿에 없을 때 삭제 된 것으로 잘못 보고 될 수 있지만 배포 중에 기본값으로 자동 설정 됩니다. 이 결과는 가상 응답에서 "노이즈"로 간주 됩니다. 최종 배포 된 리소스는 속성에 대해 설정 된 값을 갖습니다. 가상 작업이 완성 됨에 따라 이러한 속성은 결과에서 필터링 됩니다.

## <a name="programmatically-evaluate-what-if-results"></a>What-if 결과를 프로그래밍 방식으로 평가

이제 명령을 변수에 설정 하 여 가상의 결과를 프로그래밍 방식으로 평가 해 보겠습니다.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

각 변경 내용에 대 한 요약을 볼 수 있습니다.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>삭제 확인

가상 작업은 [배포 모드](deployment-modes.md)사용을 지원 합니다. 완료 모드로 설정 된 경우 템플릿에 없는 리소스가 삭제 됩니다. 다음 예제에서는 전체 모드에서 [정의 된 리소스가 없는 템플릿을](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) 배포 합니다.

템플릿을 배포 하기 전에 변경 내용을 미리 보려면 배포 명령과 함께 `-Confirm` 스위치 매개 변수를 사용 합니다. 변경이 필요한 경우 배포를 완료할 것인지 확인 합니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

템플릿에 정의 된 리소스가 없고 배포 모드가 완료로 설정 되어 있기 때문에 가상 네트워크가 삭제 됩니다.

![리소스 관리자 템플릿 배포 가상 작업 출력 배포 모드 완료](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

텍스트 출력은 다음과 같습니다.

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

예상 되는 변경 내용을 확인 하 고 배포를 실행 하려는 것을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- What-if의 미리 보기 릴리스에서 잘못 된 결과가 표시 되는 경우 [https://aka.ms/whatifissues](https://aka.ms/whatifissues)에서 문제를 보고 하세요.
- Azure PowerShell를 사용 하 여 템플릿을 배포 하려면 [리소스 관리자 템플릿과 Azure PowerShell를 사용 하 여 리소스 배포](deploy-powershell.md)를 참조 하세요.
- REST를 사용 하 여 템플릿을 배포 하려면 [리소스 관리자 템플릿 및 리소스 관리자 REST API를 사용 하 여 리소스 배포](deploy-rest.md)를 참조 하세요.
