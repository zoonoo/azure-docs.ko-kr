---
title: 템플릿 배포 what-if(미리 보기)
description: Azure 리소스 관리자 템플릿을 배포하기 전에 리소스에 어떤 변경 사항이 발생하는지 확인합니다.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: bc42585204e5cc2c3ece5293a3934fd22fe8507b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156449"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>ARM 템플릿 배포 what-if 작업(미리 보기)

ARM(Azure Resource Manager) 템플릿을 배포하기 전에 발생할 변경 내용을 미리 볼 수 있습니다. Azure Resource Manager는 템플릿을 배포하는 경우 리소스가 어떻게 변경되는지 확인할 수 있는 가정 별 작업을 제공합니다. 가상 작업으로 인해 기존 리소스가 변경되지 않습니다. 대신 지정된 템플릿이 배포되는 경우 변경 내용을 예측합니다.

> [!NOTE]
> 현재 what-if 작업이 미리 보기 상태입니다. 이를 사용하려면 [미리 보기에 가입](https://aka.ms/armtemplatepreviews)해야 합니다. 미리 보기 릴리스에서는 실제로 변경이 발생하지 않을 때 리소스가 변경될 수 있는 경우가 있습니다. 이러한 문제를 줄이기 위해 노력하고 있지만 여러분의 도움이 필요합니다. 에서 [https://aka.ms/whatifissues](https://aka.ms/whatifissues)이러한 문제를 보고하십시오.

PowerShell 명령 또는 REST API 작업을 사용하여 what-if 작업을 사용할 수 있습니다.

PowerShell에서 출력에는 다양한 유형의 변경 내용을 확인하는 데 도움이 되는 색상으로 구분된 결과가 포함되어 있습니다.

![리소스 관리자 템플릿 배포 what-if 작업 전체 리소스 페이로드 및 변경 유형](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

텍스트 오포트는 다음과 입니다.

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

## <a name="what-if-commands"></a>What-if 명령

Azure PowerShell 또는 Azure REST API를 사용하여 what-if 작업에 사용할 수 있습니다.

### <a name="azure-powershell"></a>Azure PowerShell

템플릿을 배포하기 전에 변경 내용의 미리 `-Whatif` 보기를 보려면 배포 명령에 스위치 매개 변수를 추가합니다.

* `New-AzResourceGroupDeployment -Whatif`리소스 그룹 배포용
* `New-AzSubscriptionDeployment -Whatif`및 `New-AzDeployment -Whatif` 구독 수준 배포용

또는 switch 매개 `-Confirm` 변수를 사용하여 변경 내용을 미리 보고 배포를 계속하라는 메시지가 표시될 수 있습니다.

* `New-AzResourceGroupDeployment -Confirm`리소스 그룹 배포용
* `New-AzSubscriptionDeployment -Confirm`및 `New-AzDeployment -Confirm` 구독 수준 배포용

앞의 명령은 수동으로 검사할 수 있는 텍스트 요약을 반환합니다. 프로그래밍 방식으로 검사할 수 있는 개체를 얻으려면 다음을 사용하십시오.

* `$results = Get-AzResourceGroupDeploymentWhatIf`리소스 그룹 배포용
* `$results = Get-AzSubscriptionDeploymentWhatIf`또는 `$results = Get-AzDeploymentWhatIf` 구독 수준 배포용

> [!NOTE]
> 버전 2.0.1-alpha5가 릴리스되기 전에는 `New-AzDeploymentWhatIf` 명령을 사용했습니다. 이 명령은 `Get-AzDeploymentWhatIf`에서 `Get-AzResourceGroupDeploymentWhatIf`및 `Get-AzSubscriptionDeploymentWhatIf` 명령으로 대체되었습니다. 이전 버전을 사용한 경우 해당 구문을 업데이트해야 합니다. 매개 `-ScopeType` 변수가 제거되었습니다.

### <a name="azure-rest-api"></a>Azure REST API

REST API의 경우 다음을 사용합니다.

* [배포 -](/rest/api/resources/deployments/whatif) 리소스 그룹 배포에 대한 경우
* [배포 - 구독](/rest/api/resources/deployments/whatifatsubscriptionscope) 수준 배포에 대한 구독 범위에서 What If

## <a name="change-types"></a>유형 변경

what-if 작업에는 6가지 유형의 변경 사항이 나열되어 있습니다.

- **만들기**: 리소스가 현재 존재하지 않지만 템플릿에 정의되어 있습니다. 리소스가 만들어집니다.

- **삭제**: 이 변경 유형은 배포에 [전체 모드를](deployment-modes.md) 사용할 때만 적용됩니다. 리소스가 있지만 템플릿에 정의되지 않습니다. 전체 모드를 사용하면 리소스가 삭제됩니다. [전체 모드 삭제를 지원하는](complete-mode-deletion.md) 리소스만 이 변경 유형에 포함됩니다.

- **무시**: 리소스가 존재하지만 템플릿에 정의되지 않습니다. 리소스는 배포되거나 수정되지 않습니다.

- **NoChange**: 리소스가 존재하며 템플릿에 정의됩니다. 리소스는 다시 배포되지만 리소스의 속성은 변경되지 않습니다. 이 변경 유형은 [ResultFormat을](#result-format) `FullResourcePayloads`기본값인 로 설정할 때 반환됩니다.

- **수정**: 리소스가 존재하며 템플릿에 정의됩니다. 리소스가 다시 배포되고 리소스의 속성이 변경됩니다. 이 변경 유형은 [ResultFormat을](#result-format) `FullResourcePayloads`기본값인 로 설정할 때 반환됩니다.

- **배포**: 리소스가 존재하며 템플릿에 정의됩니다. 리소스가 다시 배포됩니다. 리소스의 속성은 변경될 수도 있으며 변경되지 않을 수도 있습니다. 이 작업은 속성이 변경될지 여부를 결정하기에 충분한 정보가 없는 경우 이 변경 형식을 반환합니다. [ResultFormat이](#result-format) `ResourceIdOnly`로 설정된 경우에만 이 조건이 표시됩니다.

## <a name="result-format"></a>결과 형식

예측된 변경 사항에 대해 반환되는 세부 수준을 제어할 수 있습니다. 배포 명령 ()`New-Az*Deployment`에서 **-WhatIfResultFormat** 매개 변수를 사용합니다. 프로그래밍 방식 개체 명령`Get-Az*DeploymentWhatIf`() 에서 **ResultFormat** 매개 변수를 사용합니다.

형식 매개 변수를 **FullResourcePayloads로** 설정하여 변경될 리소스 목록과 변경될 속성에 대한 세부 정보를 가져옵니다. 형식 매개 변수를 **ResourceIdOnly로** 설정하여 변경될 리소스 목록을 가져옵니다. 기본값은 **FullResourcePayloads**입니다.  

다음 결과는 두 가지 다른 출력 형식을 보여 준다.

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

## <a name="run-what-if-operation"></a>what-if 작업 실행

### <a name="set-up-environment"></a>환경 설정

what-if의 작동 방식을 보려면 몇 가지 테스트를 실행해 보겠습니다. 먼저 가상 [네트워크를 만드는 템플릿을 배포합니다.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) 이 가상 네트워크를 사용하여 what-if에 의해 변경 내용이 보고되는 방식을 테스트합니다.

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>테스트 수정

배포가 완료되면 what-if 작업을 테스트할 준비가 된 것입니다. 이번에는 가상 [네트워크를 변경하는 템플릿을 배포합니다.](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json) 원래 태그가 하나 누락되고 서브넷이 제거되었으며 주소 접두사가 변경되었습니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

what-if 출력은 다음과 유사하게 나타납니다.

![리소스 관리자 템플릿 배포 what-if 작업 출력](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

텍스트 출력은 다음과 입니다.

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

색상이 변경 유형을 나타내기 위해 정의된 출력맨 위에 있습니다.

출력의 맨 아래에는 소유자 태그가 삭제된 것을 보여 주는 것입니다. 주소 접두사는 10.0.0.0/16에서 10.0.0.0/15로 변경되었습니다. subnet001이라는 서브넷이 삭제되었습니다. 이 변경 사항은 실제로 배포되지 않았음을 기억하십시오. 템플릿을 배포할 때 발생하는 변경 내용의 미리 보기가 표시됩니다.

삭제된 것으로 나열된 일부 속성은 실제로 변경되지 않습니다. 템플릿에 없을 때 속성이 삭제된 것으로 잘못 보고될 수 있지만 배포 중에 기본값으로 자동으로 설정됩니다. 이 결과는 what-if 응답에서 "노이즈"로 간주됩니다. 마지막으로 배포된 리소스에는 속성에 대해 설정된 값이 있습니다. what-if 작업이 성숙하면 이러한 속성이 결과에서 필터링됩니다.

## <a name="programmatically-evaluate-what-if-results"></a>프로그래밍 방식으로 what-if 결과 평가

이제 명령을 변수로 설정하여 가정별 결과를 프로그래밍 방식으로 평가해 보겠습니다.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

각 변경 사항에 대한 요약을 볼 수 있습니다.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>삭제 확인

what-if 작업은 [배포 모드를](deployment-modes.md)사용하여 지원합니다. 완료 모드로 설정하면 템플릿에 없는 리소스가 삭제됩니다. 다음 예제에서는 전체 모드에서 [정의된 리소스가 없는 템플릿을](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) 배포합니다.

템플릿을 배포하기 전에 변경 내용을 `-Confirm` 미리 보기 위해 배포 명령과 함께 스위치 매개 변수를 사용합니다. 변경 내용이 예상대로인 경우 배포를 완료할 지 확인합니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

템플릿에 리소스가 정의되지 않고 배포 모드가 완료하도록 설정되어 있으므로 가상 네트워크가 삭제됩니다.

![리소스 관리자 템플릿 배포 what-if 작업 출력 배포 모드 완료](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

텍스트 출력은 다음과 입니다.

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

예상된 변경 내용이 표시되고 배포를 실행할지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- what-if의 미리 보기 릴리스에서 잘못된 결과가 발견되면 에서 [https://aka.ms/whatifissues](https://aka.ms/whatifissues)문제를 보고하십시오.
- Azure PowerShell을 사용하여 템플릿을 배포하려면 [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포를 참조합니다.](deploy-powershell.md)
- REST가 있는 템플릿을 배포하려면 [ARM 템플릿 및 리소스 관리자 REST API를 사용하여 리소스 배포를](deploy-rest.md)참조하십시오.
