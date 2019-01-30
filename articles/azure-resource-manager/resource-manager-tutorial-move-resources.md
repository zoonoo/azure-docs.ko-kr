---
title: 다른 리소스 그룹으로 Azure 리소스 이동 | Microsoft Docs
description: 한 리소스 그룹에서 다른 리소스 그룹으로, 또는 한 구독에서 다른 구독으로 Azure 리소스를 이동하는 방법에 대해 알아봅니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/19/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0ae29146b1b44f3017d37b3cebf7ec4cf39115d0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731762"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group-or-subscription"></a>자습서: 다른 리소스 그룹 또는 구독에 Azure 리소스 이동

한 리소스 그룹에서 다른 리소스 그룹으로 Azure 리소스를 이동하는 방법을 알아봅니다. 한 Azure 구독에서 다른 Azure 구독으로 Azure 리소스를 이동할 수도 있습니다. 이 자습서에서는 리소스 관리자 템플릿을 사용하여 두 개의 리소스 그룹 및 하나의 스토리지 계정을 배포합니다. 그런 다음, 하나의 리소스 그룹에서 다른 리소스 그룹으로 스토리지 계정을 이동합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 리소스를 준비합니다.
> * 리소스를 이동할 수 있는지 확인합니다.
> * 리소스를 이동하기 전의 검사 목록입니다.
> * 이동 작업의 유효성을 검사합니다.
> * 리소스를 이동합니다.
> * 리소스를 정리합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prepare-the-resources"></a>리소스 준비

템플릿이 만들어지고 [공유 스토리지 계정](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json)에 배치되었습니다. 템플릿은 두 개의 리소스 그룹 및 하나의 스토리지 계정을 정의합니다. 템플릿을 배포할 때 프로젝트 이름을 제공해야 합니다. 프로젝트 이름은 고유한 리소스 이름을 생성하는 데 사용됩니다.  다음 JSON이 템플릿에서 추출됩니다.

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

json에 정의된 위치, 두 개의 리소스 그룹은 미국 동부 및 미국 서부에 있습니다. 스토리지 계정은 미국 동부에 있습니다. 다른 위치를 사용하여 리소스를 다른 리소스 그룹으로 이동할 때 이동 작업은 리소스의 위치를 변경하지 않습니다.

**사용해 보기**를 선택하여 Cloud shell을 연 다음, Cloud shell 내에서 PowerShell 스크립트를 실행합니다.

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzureRmDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

스크립트가 성공적으로 완료될 때까지 기다린 다음, [Azure Portal](https://portal.azure.com)을 열고, 리소스 그룹 및 스토리지 계정이 예상대로 배포되었는지 확인합니다.

> [!NOTE]
> 템플릿은 두 개의 리소스 그룹을 정의하므로 이 배포는 구독 수준 배포로 간주됩니다. 포털 템플릿 배포는 구독 수준 배포를 지원하지 않습니다. 따라서 Azure PowerShell이 이 자습서에 사용됩니다. Azure CLI도 구독 수준 배포를 지원합니다. [Azure 구독에 대한 리소스 그룹 및 리소스 만들기](./deploy-to-subscription.md)를 참조하세요.

## <a name="verify-the-resource-can-be-moved"></a>리소스를 이동할 수 있는지 확인

모든 리소스를 이동할 수 있는 것은 아닙니다. 이 자습서에서 사용되는 리소스는 이동할 수 있는 스토리지 계정입니다. 리소스를 이동할 수 있는지 여부를 확인하려면 [이동할 수 있는 서비스](./resource-group-move-resources.md#services-that-can-be-moved)를 참조하세요.

## <a name="checklist-before-moving-resources"></a>리소스를 이동하기 전의 검사 목록

이 단계는 완료된 대로 이 자습서에 대한 선택 사항입니다.

리소스를 이동하기 전에 몇 가지 중요한 단계가 있습니다. [리소스를 이동하기 전의 검사 목록](./resource-group-move-resources.md#checklist-before-moving-resources)을 참조하세요.

## <a name="validate-the-move"></a>이동의 유효성 검사

이동의 유효성 검사는 완료된 대로 이 자습서에 대한 선택 사항입니다.

이동 작업 유효성 검사를 수행하면 실제로 리소스를 이동하지 않고 이동 시나리오를 테스트할 수 있습니다. 이 작업을 수행하여 이동이 성공할지 여부를 확인합니다. 자세한 내용은 [이동의 유효성 검사](./resource-group-move-resources.md#validate-move)를 참조하세요.

## <a name="move-the-resource"></a>리소스 이동

스토리지 계정은 원본 리소스 그룹(rg1) 내에 있습니다. 다음 PowerShell 스크립트를 실행하고 리소스를 대상 리소스 그룹(rg2)으로 이동합니다. 리소스를 배포할 때 사용한 동일한 프로젝트 이름을 사용해야 합니다.

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzureRmResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzureRmResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

[Azure Portal](https://portal.azure.com)을 열고, 스토리지 계정이 다른 리소스 그룹으로 이동되었는지 확인하고, 스토리지 계정 위치가 여전히 미국 동부인지 확인합니다.

리소스를 이동할 때 원본 그룹과 대상 그룹은 모두 작업 중에 잠겨 있습니다. 쓰기 및 삭제 작업은 이동이 완료될 때까지 리소스 그룹에서 차단됩니다. 이 잠금은 리소스 그룹에서 리소스를 추가, 업데이트, 삭제할 수 없음을 의미하지만 리소스가 고정되었음을 의미하지는 않습니다. 예를 들어, SQL Server와 해당 데이터베이스를 새 리소스 그룹으로 이동하는 경우 해당 데이터베이스를 사용하는 애플리케이션에는 가동 중지 시간이 발생하지 않습니다. 데이터베이스에 계속해서 읽고 쓸 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 원본 리소스 그룹 이름을 선택합니다.  
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.
5. 대상 리소스 그룹 이름을 선택합니다.  
6. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 한 리소스 그룹에서 다른 리소스 그룹으로 스토리지 계정을 이동하는 방법을 알아보았습니다. 지금까지 하나의 스토리지 계정 또는 여러 스토리지 계정 인스턴스를 다루었습니다. 다음 자습서에서는 여러 리소스 및 여러 리소스 종류가 있는 템플릿을 개발합니다. 일부 리소스에는 종속 리소스가 있습니다.

> [!div class="nextstepaction"]
> [종속 리소스 만들기](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
