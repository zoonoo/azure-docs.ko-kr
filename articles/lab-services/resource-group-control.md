---
title: Azure DevTest Labs에서 VM에 대한 리소스 그룹 지정 | Microsoft Docs
description: Azure DevTest Labs의 랩에서 VM에 대한 리소스 그룹을 지정하는 방법을 알아봅니다.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2019
ms.author: spelluru
ms.openlocfilehash: ddda9ef2b9bb716f7cdd33aa8fe9233f6c7d8e82
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55749003"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 가상 머신에 대한 리소스 그룹 지정
랩 소유자는 랩 가상 머신을 특정 리소스 그룹에 만들도록 구성할 수 있습니다. 이 기능을 Azure 구독에 대한 리소스 그룹 제한에 도달하지 않도록 사용하세요. 이 기능을 사용하여 모든 랩 리소스를 단일 리소스 그룹 내에 통합할 수도 있습니다. 또한 이 기능은 해당 리소스 추적 및 리소스 그룹 수준에서 리소스를 관리하기 위한 [정책](../governance/policy/overview.md) 적용을 단순화합니다.

이 기능을 통해 스크립트를 사용하여 Azure 구독 내에서 모든 랩 VM에 대한 새 또는 기존 리소스 그룹을 지정할 수 있습니다. 현재 DevTest Labs는 API를 통해 이 기능을 지원합니다. 

## <a name="api-to-configure-a-resource-group-for-labs-vms"></a>랩 VM에 대한 리소스 그룹을 구성하는 API
이제 이 API를 사용하는 동안 랩 소유자에게 제공되는 옵션을 살펴보겠습니다. 

- 모든 가상 머신에 대해 **랩의 리소스 그룹**을 선택할 수 있습니다.
- 모든 가상 머신에 대해 랩의 리소스 그룹 이외의 **기존 리소스 그룹**을 선택할 수 있습니다.
- 모든 가상 머신에 대해 **새 리소스 그룹** 이름을 입력할 수 있습니다.
- 기존 동작을 계속할 수 있습니다. 즉, 랩의 각 VM에 대해 리소스 그룹이 생성됩니다.
 
이 설정은 랩에 생성된 새 가상 머신에 적용됩니다. 기존 리소스 그룹에 생성된 랩의 이전 VM은 영향을 받지 않고 계속 유지됩니다. 그러나 모든 랩 가상 머신이 하나의 공통 리소스 그룹에 놓이도록 이러한 가상 머신을 개별 리소스 그룹에서 공통 리소스 그룹으로 마이그레이션할 수 있습니다. 자세한 내용은 [새 리소스 그룹으로 리소스 이동](../azure-resource-manager/resource-group-move-resources.md)을 참조하세요. 랩에 생성된 환경은 자체의 고유한 리소스 그룹에 계속 유지됩니다.

### <a name="how-to-use-this-api"></a>이 API를 사용하는 방법:
- 이 API를 사용하는 동안 API 버전 **2018_10_15_preview**를 사용합니다. 
- 새 리소스 그룹을 지정하는 경우 구독 내의 **리소스 그룹에 대한 쓰기 사용 권한**이 있는지 확인하세요. 쓰기 사용 권한이 없는 경우 지정된 리소스 그룹에 새 가상 머신을 만들면 고장이 발생합니다. 
- 이 API를 사용하는 동안 **전체 리소스 그룹 ID**를 전달합니다. 예: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>` 리소스 그룹이 랩의 구독과 동일한 구독에 있는지 확인합니다. 

## <a name="use-powershell"></a>PowerShell 사용 
다음 예제에서는 PowerShell 스크립트를 사용하여 새 리소스 그룹에 모든 랩 가상 머신을 만드는 방법을 설명합니다.

```PowerShell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

다음 명령을 사용하여 스크립트를 호출합니다(ResourceGroup.ps1은 이전 스크립트를 포함하는 파일). 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 사용
Azure Resource Manager 템플릿을 사용하여 랩을 만드는 경우 다음 예제와 같이 Resource Manager 의 랩 속성 섹션에 있는 **vmCreationResourceGroupId** 속성을 사용합니다.

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "6e6f668f-992b-435c-bac3-d328b745cd25"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [랩에 대한 정책 설정](devtest-lab-get-started-with-lab-policies.md)
- [질문과 대답](devtest-lab-faq.md)
