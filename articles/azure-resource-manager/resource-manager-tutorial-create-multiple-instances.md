---
title: Azure Resource Manager를 사용하여 여러 리소스 인스턴스 만들기 | Microsoft Docs
description: Azure Resource Manager 템플릿을 만들어 여러 Azure 리소스 인스턴스를 만드는 방법을 알아봅니다.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b50609449d6144d2bb013d82e2eb29e94b5b01be
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754121"
---
# <a name="tutorial-create-multiple-resource-instances-with-resource-manager-templates"></a>자습서: Resource Manager 템플릿을 사용하여 여러 리소스 인스턴스 만들기

Azure Resource Manager 템플릿을 반복하여 Azure 리소스의 여러 인스턴스를 만드는 방법을 알아봅니다. 이 자습서에서는 3개의 저장소 계정 인스턴스를 만들도록 템플릿을 수정합니다.

이 자습서에서 다루는 작업은 다음과 같습니다.

> [!div class="checklist"]
> * 빠른 시작 템플릿 열기
> * 템플릿 편집
> * 템플릿 배포

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

이 문서를 완료하려면 다음이 필요합니다.

* [Visual Studio Code](https://code.visualstudio.com/) 및 [Resource Manager 도구 확장](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)

## <a name="open-a-quickstart-template"></a>빠른 시작 템플릿 열기

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)은 Resource Manager 템플릿용 저장소입니다. 템플릿을 처음부터 새로 만드는 대신 샘플 템플릿을 찾아서 사용자 지정할 수 있습니다. 이 빠른 시작에서 사용되는 템플릿은 [표준 저장소 계정 만들기](https://azure.microsoft.com/resources/templates/101-storage-account-create/)라고 합니다. 이 템플릿은 Azure Storage 계정 리소스를 정의합니다.

1. Visual Studio Code에서 **파일**>**파일 열기**를 차례로 선택합니다.
2. **파일 이름**에서 다음 URL을 붙여넣습니다.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **열기**를 선택하여 파일을 엽니다.
4. 템플릿에 'Microsoft.Storage/storageAccounts' 리소스가 정의되어 있습니다. 이 템플릿을 [템플릿 참조](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)와 비교합니다. 템플릿을 사용자 지정하기 전에 템플릿의 몇 가지 기본적인 내용을 이해하면 유용합니다.
5. **파일**>**이름으로 저장**을 차례로 선택하여 파일을 **azuredeploy.json**으로 저장합니다.

## <a name="edit-the-template"></a>템플릿 편집

기존 템플릿은 저장소 계정 1개를 만듭니다. 저장소 계정 3개를 만들도록 템플릿을 사용자 지정합니다.  

Visual Studio Code에서 다음 4개의 변경 내용을 만듭니다.

![Azure Resource Manager가 여러 인스턴스 생성](./media/resource-manager-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. 저장소 계정 리소스 정의에 `copy` 요소를 추가합니다. copy 요소에서 이 루프의 반복 횟수와 변수를 지정합니다. count 값은 양의 정수여야 하며 800을 초과할 수 없습니다.
2. `copyIndex()` 함수는 루프에서 현재 반복을 반환합니다. 인덱스를 이름 접두사로 사용합니다. `copyIndex()`는 0부터 시작합니다. 인덱스 값을 오프셋하려면 copyIndex() 함수에 값을 전달하면 됩니다. 예를 들어 *copyIndex(1)* 입니다.
3. 더 이상 사용되지 않으므로 **변수** 요소를 삭제합니다.
4. **출력** 요소를 삭제합니다. 더 이상 필요 없습니다.

완성된 템플릿은 다음과 같습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": 3
      }
    }
  ]
}
```

여러 인스턴스 만들기에 대한 자세한 내용은 [Azure Resource Manager 템플릿에서 리소스 또는 속성의 여러 인스턴스 배포](./resource-group-create-multiple.md)를 참조하세요.

## <a name="deploy-the-template"></a>템플릿 배포

배포 절차는 Visual Studio Code 빠른 시작의 [템플릿 배포](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) 섹션을 참조하세요.

세 개의 모든 저장소 계정을 나열하려면 --name 매개 변수를 생략합니다.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az storage account list --resource-group $resourceGroupName
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName
```

---

저장소 계정 이름을 템플릿의 이름 정의와 비교합니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요하지 않은 경우 리소스 그룹을 삭제하여 배포한 리소스를 정리합니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.  리소스 그룹에 총 6개의 리소스가 표시됩니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 여러 저장소 계정 인스턴스를 만드는 방법을 배웠습니다. 다음 자습서에서는 한 리소스 그룹에서 다른 리소스 그룹으로 리소스를 이동하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [리소스 이동](./resource-manager-tutorial-move-resources.md)
