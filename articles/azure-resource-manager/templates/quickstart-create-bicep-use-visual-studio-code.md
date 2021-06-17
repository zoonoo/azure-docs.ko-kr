---
title: Bicep 파일 만들기 - Visual Studio Code
description: Azure 리소스 배포를 위해 Visual Studio Code 및 Bicep 파일에 대한 Bicep 확장 사용
author: mumian
ms.date: 04/12/2021
ms.topic: quickstart
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e176173063f1b4a4fa78ac78680841a211be2871
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353473"
---
# <a name="quickstart-create-bicep-files-with-visual-studio-code"></a>빠른 시작: Visual Studio Code를 사용하여 Bicep 파일 만들기

Visual Studio Code의 Bicep 확장은 언어 지원 및 리소스 자동 완성 기능을 제공합니다. 이러한 도구는 [Bicep](./bicep-overview.md) 파일을 만들고 유효성을 검사하는 데 유용합니다. 이 빠른 시작에서는 확장을 사용하여 Bicep 파일을 처음부터 새로 만듭니다. 이렇게 하는 동안 유효성 검사 및 완성과 같은 확장 기능을 사용하게 됩니다.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

이 빠른 시작을 완료하려면 [Bicep 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)이 설치된 [Visual Studio Code](https://code.visualstudio.com/)가 필요합니다. 또한 [Azure CLI](/cli/azure/) 또는 [Azure PowerShell 모듈](/powershell/azure/new-azureps-module-az)이 설치 및 인증되어 있어야 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-a-bicep-file"></a>Bicep 파일 만들기

*azuredeploy.bicep* 이라는 새 파일을 만들고 Visual Studio Code를 사용하여 엽니다.

## <a name="add-an-azure-resource"></a>Azure 리소스 추가

bicep 파일에 기본 스토리지 계정 리소스를 추가합니다.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: 'storageaccount1' // must be globally unique
  location: 'eastus'
  tags:{
    diplayName: 'storageaccount1'
  }
  sku: {
      name: 'Standard_LRS'
      tier: 'Standard'
  }
  kind: 'Storage'
}
```

리소스 선언에는 다음 네 가지 구성 요소가 있습니다.

- **리소스**: 키워드
- **심볼 이름**(stg): 심볼 이름은 bicep 파일 전체에서 리소스를 참조하는 식별자입니다. 배포되는 리소스의 이름이 아닙니다. 리소스 이름은 **name** 속성으로 정의됩니다.  이 목록에서 네 번째 구성 요소를 참조하세요.
- **리소스 유형**(Microsoft.Storage/storageAccounts@2019-06-01): 리소스 공급자(Microsoft.Storage), 리소스 유형(storageAccounts) 및 apiVersion(2019-06-01)으로 구성됩니다. 각 리소스 공급자는 자체 API 버전을 게시하므로 이 값은 유형에 따라 다릅니다. [ARM 템플릿 참조](/azure/templates/)에서 다양한 Azure 리소스에 대한 더 많은 유형과 apiVersion을 찾을 수 있습니다.
- **속성**(= {...} 내의 모든 항목): 리소스 종류에 대한 속성을 지정합니다. 모든 리소스에는 `name` 속성이 있습니다. 대부분의 리소스에는 `location` 속성이 있으며, 이 속성은 리소스가 배포되는 지역을 설정합니다. 다른 속성은 리소스 유형 및 API 버전에 따라 다릅니다.

자세한 내용은 [Bicep 구조체](./bicep-file.md)를 참조하세요.

이름 속성에 대한 설명이 있습니다.  한 줄 주석에는 `//`를 사용하고 여러 줄 주석에는 `/* ... */`를 사용합니다.

## <a name="completion-and-validation"></a>완성 및 유효성 검사

이 확장의 가장 강력한 기능 중 하나는 Azure 스키마와의 통합입니다. Azure 스키마는 이 확장에 유효성 검사 및 리소스 인식 완성 기능을 제공합니다. 스토리지 계정을 수정하여 유효성 검사 및 완성이 작동하는 모습을 확인하겠습니다.

먼저 스토리지 계정 종류를 `megaStorage`와 같은 잘못된 값으로 업데이트합니다. 이 작업을 수행하면 `megaStorage`가 올바른 값이 아님을 나타내는 경고가 생성됩니다.

![잘못된 스토리지 구성을 보여주는 이미지](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-validation.png)

완성 기능을 사용하려면 `megaStorage`를 제거하고, 작은따옴표 안에 커서를 놓고 `ctrl` + `space`를 누릅니다. 이 작업을 수행하면 올바른 값의 완성 목록이 표시됩니다.

![확장의 자동 완성 기능을 보여주는 이미지](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-auto-completion.png)

## <a name="add-parameters"></a>매개 변수 추가

이제 매개 변수를 만들고 사용하여 스토리지 계정 이름을 지정합니다.

파일 맨 앞에 다음 코드를 추가합니다.

```bicep
@minLength(3)
@maxLength(24)
@description('Specify a storage account name.')
param storageAccountName string
```

Azure 스토리지 계정 이름은 3~24자여야 합니다. `minLength`및 `maxLength`를 사용하여 적절한 값을 제공합니다.

이제 스토리지 리소스에서 이 매개 변수를 사용하도록 name 속성을 업데이트합니다. 이렇게 하려면 작은따옴표를 포함하는 현재 스토리지 리소스 이름을 제거합니다. `ctrl` + `space`를 누릅니다. 목록에서 **storageAccountName** 매개 변수를 선택합니다. 매개 변수는 Bicep에서 해당 이름을 사용하여 직접 참조할 수 있습니다. JSON 템플릿에는 parameter() 함수가 필요합니다.

![Bicep 리소스에서 매개 변수를 사용할 때의 자동 완성을 보여 주는 이미지](./media/quickstart-create-bicep-use-visual-studio-code/azure-resource-manager-template-bicep-visual-studio-code-valid-param.png)

## <a name="deploy-the-bicep-file"></a>Bicep 파일 배포

`ctrl` + ```` ` ```` 키 조합을 사용하여 통합 Visual Studio Code 터미널을 열고 현재 디렉터리를 Bicep 파일이 있는 위치로 변경한 다음, Azure CLI 또는 Azure PowerShell 모듈을 사용하여 Bicep 파일을 배포합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group create --name arm-vscode --location eastus

az deployment group create --resource-group arm-vscode --template-file azuredeploy.bicep --parameters storageAccountName={your-unique-name}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
New-AzResourceGroup -Name arm-vscode -Location eastus

New-AzResourceGroupDeployment -ResourceGroupName arm-vscode -TemplateFile ./azuredeploy.bicep -storageAccountName "{your-unique-name}"
```

---

## <a name="clean-up-resources"></a>리소스 정리

Azure 리소스가 더 이상 필요 없으면 Azure CLI 또는 Azure PowerShell 모듈을 사용하여 빠른 시작 리소스 그룹을 삭제합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli
az group delete --name arm-vscode
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell
Remove-AzResourceGroup -Name arm-vscode
```

---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [초급 Bicep 자습서](./bicep-tutorial-create-first-bicep.md)
