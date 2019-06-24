---
title: Azure Policy를 사용하여 VM 확장 설치 제한 | Microsoft Docs
description: Azure Policy를 사용하여 확장 배포 제한
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/23/2018
ms.author: roiyz;cynthn
ms.openlocfilehash: dfaeff5e8f90e72645293d15c454f78f17b08fb0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717674"
---
# <a name="use-azure-policy-to-restrict-extensions-installation-on-windows-vms"></a>Azure Policy를 사용하여 Windows VM의 확장 설치 제한

Windows VM에서 특정 확장을 사용하거나 설치하지 못하도록 하려면 PowerShell을 사용하여 리소스 그룹 내의 VM 확장을 제한하는 Azure 정책을 만들 수 있습니다. 

이 자습서에서는 지속적으로 최신 버전으로 업데이트되는 Cloud Shell 내의 Azure PowerShell을 사용합니다. 

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="create-a-rules-file"></a>규칙 파일 만들기

설치할 수 있는 확장을 제한하려면 확장을 식별하는 논리를 제공하는 [규칙](../../governance/policy/concepts/definition-structure.md#policy-rule)이 있어야 합니다.

이 예제에서는 Azure Cloud Shell에서 규칙 파일을 만들어 ‘Microsoft.Compute’에 의해 게시된 확장을 거부하는 방법을 보여 줍니다. 그러나 PowerShell에서 로컬로 작업하는 경우 로컬 파일을 만들고 경로($home/clouddrive)를 컴퓨터에 있는 로컬 파일의 경로로 바꿀 수도 있습니다.

[Cloud Shell](https://shell.azure.com/powershell)에서 다음을 입력합니다.

```azurepowershell-interactive
nano $home/clouddrive/rules.json
```

다음 .json을 복사하여 파일에 붙여넣습니다.

```json
{
    "if": {
        "allOf": [
            {
                "field": "type",
                "equals": "Microsoft.Compute/virtualMachines/extensions"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/publisher",
                "equals": "Microsoft.Compute"
            },
            {
                "field": "Microsoft.Compute/virtualMachines/extensions/type",
                "in": "[parameters('notAllowedExtensions')]"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}
```

완료되면 **Ctrl+O**를 누른 다음 **Enter** 키를 눌러 파일을 저장합니다. **Ctrl+X**를 눌러 파일을 닫고 종료합니다.

## <a name="create-a-parameters-file"></a>매개 변수 파일 만들기

또한 블록에 확장 목록을 전달하는 데 사용할 구조를 만드는 [매개 변수](../../governance/policy/concepts/definition-structure.md#parameters)도 필요합니다. 

이 예제에서는 Cloud Shell에서 VM용 매개 변수 파일을 만드는 방법을 보여 줍니다. 그러나 PowerShell에서 로컬로 작업하는 경우 로컬 파일을 만들고 경로($home/clouddrive)를 컴퓨터에 있는 로컬 파일의 경로로 바꿀 수도 있습니다.

[Cloud Shell](https://shell.azure.com/powershell)에서 다음을 입력합니다.

```azurepowershell-interactive
nano $home/clouddrive/parameters.json
```

다음 .json을 복사하여 파일에 붙여넣습니다.

```json
{
    "notAllowedExtensions": {
        "type": "Array",
        "metadata": {
            "description": "The list of extensions that will be denied.",
            "strongType": "type",
            "displayName": "Denied extension"
        }
    }
}
```

완료되면 **Ctrl+O**를 누른 다음 **Enter** 키를 눌러 파일을 저장합니다. **Ctrl+X**를 눌러 파일을 닫고 종료합니다.

## <a name="create-the-policy"></a>정책 만들기

정책 정의는 사용하려는 구성을 저장하는 데 사용되는 개체입니다. 정책 정의는 규칙 및 매개 변수 파일을 사용하여 정책을 정의합니다. [New-AzPolicyDefinition](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicydefinition) cmdlet을 사용하여 정책 정의를 만듭니다.

 정책 규칙 및 매개 변수는 사용자가 만든 파일이며 Cloud Shell에 .json 파일로 저장됩니다.


```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
   -Name "not-allowed-vmextension-windows" `
   -DisplayName "Not allowed VM Extensions" `
   -description "This policy governs which VM extensions that are explicitly denied."   `
   -Policy 'C:\Users\ContainerAdministrator\clouddrive\rules.json' `
   -Parameter 'C:\Users\ContainerAdministrator\clouddrive\parameters.json'
```




## <a name="assign-the-policy"></a>정책 할당

이 예제에서는 [New-AzPolicyAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azpolicyassignment)를 사용하여 리소스 그룹에 정책을 할당합니다. **myResourceGroup** 리소스 그룹에 생성된 VM은 VM 액세스 에이전트 또는 사용자 지정 스크립트 확장을 설치할 수 없습니다. 

[Get-AzSubscription | Format-Table](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription) cmdlet을 사용하여 예제의 ID 대신 사용할 구독 ID를 가져옵니다.

```azurepowershell-interactive
$scope = "/subscriptions/<subscription id>/resourceGroups/myResourceGroup"
$assignment = New-AzPolicyAssignment `
   -Name "not-allowed-vmextension-windows" `
   -Scope $scope `
   -PolicyDefinition $definition `
   -PolicyParameter '{
    "notAllowedExtensions": {
        "value": [
            "VMAccessAgent",
            "CustomScriptExtension"
        ]
    }
}'
$assignment
```

## <a name="test-the-policy"></a>정책 테스트

정책을 테스트하려면 VM 액세스 확장을 사용합니다. 다음은 실패하고 “Set-AzVMAccessExtension: 정책에서 ‘myVMAccess’ 리소스를 거부했습니다.” 메시지가 표시됩니다.

```azurepowershell-interactive
Set-AzVMAccessExtension `
   -ResourceGroupName "myResourceGroup" `
   -VMName "myVM" `
   -Name "myVMAccess" `
   -Location EastUS 
```

포털에서 암호 변경은 실패하고 “정책 위반으로 인해 템플릿 배포에 실패했습니다.” 메시지가 표시됩니다.

## <a name="remove-the-assignment"></a>할당 제거

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name not-allowed-vmextension-windows -Scope $scope
```

## <a name="remove-the-policy"></a>정책 제거

```azurepowershell-interactive
Remove-AzPolicyDefinition -Name not-allowed-vmextension-windows
```
    
## <a name="next-steps"></a>다음 단계
자세한 내용은 [Azure Policy](../../governance/policy/overview.md)를 참조하세요.
