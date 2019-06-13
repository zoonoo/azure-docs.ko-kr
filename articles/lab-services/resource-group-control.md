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
ms.openlocfilehash: 574cc0c41ce645c71302178afcf6e7deaec69d8e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476090"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Azure DevTest Labs에서 랩 가상 머신에 대한 리소스 그룹 지정

랩 소유자는 랩 가상 머신을 특정 리소스 그룹에 만들도록 구성할 수 있습니다. 이 기능은 다음 시나리오에서 도움이 됩니다.

- 구독의 랩에서 더 적은 리소스 그룹을 만들도록 합니다.
- 고정된 된 집합을 구성 하는 리소스 그룹 내에서 작동 랩 경우
- Azure 구독 내에서 리소스 그룹을 만드는 데 필요한 승인 및 제한을 해결합니다.
- 이러한 리소스를 추적 하 고 적용을 간소화 하기 위해 단일 리소스 그룹 내에서 모든 랩 리소스를 통합 [정책을](../governance/policy/overview.md) 리소스 그룹 수준에서 리소스를 관리 합니다.

이 기능을 사용 하 여 모든 랩 Vm에 대 한 Azure 구독 내에서 새 또는 기존 리소스 그룹을 지정 하는 스크립트를 사용할 수 있습니다. 현재 Azure DevTest Labs API 통해이 기능을 지원 합니다.

> [!NOTE]
> 모든 구독 제한에는 DevTest Labs에서 랩을 만들 때 적용 됩니다. 랩의 다른 리소스에 구독으로 간주 합니다. 리소스 그룹의 경우 해당 제한은 [980 리소스 그룹 구독 당](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)합니다. 

## <a name="use-azure-portal"></a>Azure Portal 사용
랩에서 만들어진 모든 Vm에 대 한 리소스 그룹을 지정 하려면 다음이 단계를 수행 합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 선택 **모든 서비스** 왼쪽된 탐색 메뉴에서. 
3. 선택 **DevTest Labs** 목록에서.
4. 랩 목록에서 선택 하 **랩**합니다.  
5. 선택 **구성 및 정책** 에 **설정** 왼쪽 메뉴의 섹션입니다. 
6. 선택 **랩 설정** 왼쪽된 메뉴에서. 
7. 선택 **리소스 그룹의 모든 virtual machines**합니다. 
8. 드롭 다운 목록 (또는) 선택에서 기존 리소스 그룹을 선택 **새로 만들기**를 입력 한 **이름** 선택한 리소스 그룹에 대 한 **확인**합니다. 

    ![모든 랩 Vm에 대 한 리소스 그룹을 선택 합니다.](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>PowerShell 사용 
다음 예제에서는 새 리소스 그룹의 모든 랩 가상 머신을 만드는 PowerShell 스크립트를 사용 하는 방법을 보여 줍니다.

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

다음 명령을 사용 하 여 스크립트를 호출 합니다. ResourceGroup.ps1는 앞의 스크립트를 포함 하는 파일입니다.

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용 하 여
Azure Resource Manager 템플릿의 랩을 만들려면를 사용 하는 경우는 **vmCreationResourceGroupId** 다음 예제에서와 같이 템플릿의 랩 속성 섹션에 있는 속성:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>랩 Vm에 대 한 리소스 그룹을 구성 하는 API
다음과 같은 옵션이 있습니다 랩 소유자는이 API를 사용 하는 경우:

- 선택 된 **랩의 리소스 그룹** 모든 virtual machines에 대 한 합니다.
- 선택 된 **기존 리소스 그룹** 이외의 모든 virtual machines에 대 한 랩의 리소스 그룹입니다.
- 입력 한 **새 리소스 그룹** 모든 virtual machines에 대 한 이름입니다.
- 랩의 각 VM에 대 한 리소스 그룹을 작성 하는 기존 동작을 사용 하 여 계속 합니다.
 
이 설정은 랩에 생성된 새 가상 머신에 적용됩니다. 자체 리소스 그룹에 생성 된 랩의 이전 Vm을 받지 않습니다. 랩에서 만들어진 환경 자체 리소스 그룹에 계속 됩니다.

이 API를 사용하는 방법:
- API 사용 하 여 버전 **2018_10_15_preview**합니다.
- 새 리소스 그룹을 지정 했는지 확인 **리소스 그룹에 대 한 쓰기 권한이** 구독에 있습니다. 사용자에 게 쓰기 권한이 없기에 지정된 된 리소스 그룹에서 새 virtual machines 만들기가 실패 합니다.
- 이 API를 사용하는 동안 **전체 리소스 그룹 ID**를 전달합니다. 예: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>` 랩으로 동일한 구독에 리소스 그룹 인지 확인 합니다. 


## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [랩에 대한 정책 설정](devtest-lab-get-started-with-lab-policies.md)
- [질문과 대답](devtest-lab-faq.md)
