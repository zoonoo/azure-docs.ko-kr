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
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134524"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 가상 머신에 대한 리소스 그룹 지정

랩 소유자는 랩 가상 머신을 특정 리소스 그룹에 만들도록 구성할 수 있습니다. 이 기능은 다음 시나리오에서 도움이 됩니다.

- 구독의 랩에서 더 적은 리소스 그룹을 만들도록 합니다.
- 랩이 구성한 고정된 리소스 그룹 집합 내에서 작동하도록 합니다.
- Azure 구독 내에서 리소스 그룹을 만드는 데 필요한 승인 및 제한을 해결합니다.
- 단일 리소스 그룹 내의 모든 랩 리소스를 통합하여 해당 리소스를 추적하고 [정책을](../governance/policy/overview.md) 적용하여 리소스 그룹 수준에서 리소스를 관리합니다.

이 기능을 사용하면 스크립트를 사용하여 모든 랩 VM에 대해 Azure 구독 내에서 새 리소스 그룹 또는 기존 리소스 그룹을 지정할 수 있습니다. 현재 Azure DevTest 랩은 API를 통해 이 기능을 지원합니다.

> [!NOTE]
> 모든 구독 한도는 DevTest Labs에서 랩을 만들 때 적용됩니다. 랩을 구독의 다른 리소스로 간주합니다. 리소스 그룹의 경우 제한은 [구독당 980개의 리소스 그룹입니다.](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) 

## <a name="use-azure-portal"></a>Azure Portal 사용
다음 단계에 따라 랩에서 만든 모든 VM에 대한 리소스 그룹을 지정합니다. 

1. [Azure 포털에](https://portal.azure.com)로그인합니다.
2. 왼쪽 탐색 메뉴에서 **모든 서비스를** 선택합니다. 
3. 목록에서 **개발자 테스트 랩을 선택합니다.**
4. 랩 목록에서 **랩을**선택합니다.  
5. 왼쪽 메뉴의 **설정** 섹션에서 **구성 및 정책을** 선택합니다. 
6. 왼쪽 메뉴에서 **랩 설정을** 선택합니다. 
7. **하나의 리소스 그룹의 모든 가상 컴퓨터를**선택합니다. 
8. 드롭다운 목록에서 기존 리소스 그룹을 선택(또는) 새 **만들기를**선택하고 리소스 그룹에 대한 **이름을** 입력하고 **확인을**선택합니다. 

    ![모든 랩 VM에 대한 리소스 그룹 선택](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>PowerShell 사용 
다음 예제에서는 PowerShell 스크립트를 사용하여 새 리소스 그룹의 모든 랩 가상 컴퓨터를 만드는 방법을 보여 줍니다.

```powershell
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

다음 명령을 사용하여 스크립트를 호출합니다. ResourceGroup.ps1은 이전 스크립트를 포함하는 파일입니다.

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿 사용
Azure Resource Manager 템플릿을 사용하여 랩을 만드는 경우 다음 예제와 같이 템플릿의 랩 속성 섹션에서 **vmCreationResourceGroupId** 속성을 사용합니다.

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
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>랩 VM에 대한 리소스 그룹을 구성하는 API
이 API를 사용할 때 랩 소유자로 다음 옵션이 있습니다.

- 모든 가상 컴퓨터에 대해 **랩의 리소스 그룹을** 선택합니다.
- 모든 가상 컴퓨터에 대해 랩의 리소스 그룹이 아닌 **기존 리소스** 그룹을 선택합니다.
- 모든 가상 시스템에 대해 **새 리소스 그룹** 이름을 입력합니다.
- 랩의 각 VM에 대해 리소스 그룹이 만들어지는 기존 동작을 계속 사용합니다.
 
이 설정은 랩에 생성된 새 가상 머신에 적용됩니다. 랩의 이전 VM은 자체 리소스 그룹에서 만든 상태로 유지되지 않습니다. 랩에서 생성된 환경은 계속해서 자체 리소스 그룹에 남아 있습니다.

이 API를 사용하는 방법:
- 2018_10_15_preview API **버전을**사용합니다.
- 새 리소스 그룹을 지정하는 경우 구독의 **리소스 그룹에 대한 쓰기 권한이** 있는지 확인합니다. 쓰기 권한이 없는 경우 지정된 리소스 그룹에 새 가상 컴퓨터를 만드는 데 실패합니다.
- 이 API를 사용하는 동안 **전체 리소스 그룹 ID**를 전달합니다. 예: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>` 리소스 그룹이 랩과 동일한 구독에 있는지 확인합니다. 


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [랩에 대한 정책 설정](devtest-lab-get-started-with-lab-policies.md)
- [질문과 대답](devtest-lab-faq.md)
