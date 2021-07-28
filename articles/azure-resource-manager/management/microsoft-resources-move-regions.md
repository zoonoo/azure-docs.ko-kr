---
title: Microsoft.Resources에 있는 리소스의 지역 이동
description: Microsoft.Resources 네임스페이스에 있는 리소스를 새 지역으로 이동하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 898e5efef22f76dc07395fcfcad413ef4582dafd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725875"
---
# <a name="move-microsoftresources-resources-to-new-region"></a>Microsoft.Resources 리소스를 새 지역으로 이동

기존 리소스를 새 지역으로 이동해야 할 수도 있습니다. 이 문서에서는 Microsoft.Resources 네임스페이스에 있는 두 리소스 종류인 templateSpecs 및 deploymentScripts를 이동하는 방법을 보여 줍니다.

## <a name="move-template-specs-to-new-region"></a>템플릿 사양을 새 지역으로 이동

한 지역에 있는 [템플릿 사양](../templates/template-specs.md)을 새 지역으로 이동하려는 경우 템플릿 사양을 내보내고 다시 배포할 수 있습니다.

1. 기존 템플릿 사양을 내보내는 명령을 사용합니다. 내보내려는 템플릿 사양과 일치하는 값을 매개 변수 값으로 제공합니다.

   Azure PowerShell의 경우 다음을 사용합니다.

   ```azurepowershell
   Export-AzTemplateSpec `
     -ResourceGroupName demoRG `
     -Name demoTemplateSpec `
     -Version 1.0 `
     -OutputFolder c:\export
   ```

   Azure CLI의 경우

   ```azurecli
   az template-specs export \
     --resource-group demoRG \
     --name demoTemplateSpec \
     --version 1.0 \
     --output-folder c:\export
   ```

1. 내보낸 템플릿 사양을 사용하여 새 템플릿 사양을 만듭니다. 다음 예제에서는 새 지역이 `westus`이지만 원하는 지역을 제공할 수 있습니다.

   Azure PowerShell의 경우 다음을 사용합니다.

   ```azurepowershell
   New-AzTemplateSpec `
     -Name movedTemplateSpec `
     -Version 1.0 `
     -ResourceGroupName newRG `
     -Location westus `
     -TemplateJsonFile c:\export\1.0.json
   ```

   Azure CLI의 경우

   ```azurecli
   az template-specs create \
     --name movedTemplateSpec \
     --version "1.0" \
     --resource-group newRG \
     --location "westus" \
     --template-file "c:\export\demoTemplateSpec.json"
   ```

## <a name="move-deployment-scripts-to-new-region"></a>배포 스크립트를 새 지역으로 이동

1. 새 지역으로 이동하려는 배포 스크립트가 포함된 리소스 그룹을 선택합니다.

1. [템플릿을 내보냅니다](../templates/export-template-portal.md). 내보낼 때 배포 스크립트와 필요한 다른 리소스도 모두 선택합니다.

1. 내보낸 템플릿에서 다음 속성을 삭제합니다.

   * tenantId
   * principalId
   * clientId

1. 내보낸 템플릿에서 배포 스크립트 지역에는 하드 코드된 값이 있습니다.

   ```json
   "location": "westus2",
   ```

   위치 설정을 위한 매개 변수를 허용하도록 템플릿을 변경합니다. 자세한 내용은 [ARM 템플릿에서 리소스 위치 설정](../templates/resource-location.md)을 참조하세요.

   ```json
   "location": "[parameters('location')]",
   ```

1. [내보낸 템플릿을 배포](../templates/deploy-powershell.md)하고 배포 스크립트의 새 지역을 지정합니다.

## <a name="next-steps"></a>다음 단계

* 리소스를 새 리소스 그룹이나 구독으로 이동하는 방법에 대한 자세한 내용은 [새 리소스 그룹 또는 구독으로 리소스 이동](move-resource-group-and-subscription.md)을 참조하세요.
* 리소스를 새 지역으로 이동하는 방법에 대한 자세한 내용은 [지역 간 리소스 이동](move-resources-overview.md#move-resources-across-regions)을 참조하세요.
