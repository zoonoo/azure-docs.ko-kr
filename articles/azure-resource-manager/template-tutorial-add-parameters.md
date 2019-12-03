---
title: 자습서 - 템플릿에 매개 변수 추가
description: Azure Resource Manager 템플릿에 매개 변수를 추가하여 다시 사용할 수 있도록 합니다.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 28c171dfa067ec9b3eff2e0d7e5d5dd0a0c274c0
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406084"
---
# <a name="tutorial-add-parameters-to-your-resource-manager-template"></a>자습서: Resource Manager 템플릿에 매개 변수 추가

[이전 자습서](template-tutorial-add-resource.md)에서는 템플릿에 스토리지 계정을 추가하여 배포하는 방법을 알아보았습니다. 이 자습서에서는 매개 변수를 추가하여 템플릿을 개선하는 방법을 알아봅니다. 이 자습서를 완료하는 데 **14분** 정도 걸립니다.

## <a name="prerequisites"></a>필수 조건

[리소스에 대한 자습서](template-tutorial-add-resource.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Resource Manager Tools 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [템플릿 도구](template-tutorial-create-first-template.md#get-tools)를 참조하세요.

## <a name="review-template"></a>템플릿 검토

이전 자습서의 끝 부분에 템플릿에는 다음 JSON이 있습니다.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json)]

이 템플릿에 문제가 있음을 알 수 있습니다. 스토리지 계정 이름이 하드 코딩되어 있습니다. 이 템플릿은 매번 동일한 스토리지 계정을 배포하는 데에만 사용할 수 있습니다. 다른 이름의 스토리지 계정을 배포하려면, 새 템플릿을 만들어야 하며, 이것은 배포를 자동화하기에 타당한 방법이 아닙니다.

## <a name="make-template-reusable"></a>템플릿을 재사용할 수 있도록 설정

템플릿을 재사용할 수 있도록, 스토리지 계정 이름을 전달하는 데 사용할 수 있는 매개 변수를 추가하겠습니다. 다음 예제에 강조 표시되어 있는 JSON은 템플릿에서 변경된 내용을 보여줍니다. **storageName** 매개 변수는 문자열로 식별됩니다. 너무 긴 이름은 피하기 위해 최대 길이는 24자로 설정되어 있습니다.

전체 파일을 복사하고 템플릿을 해당 콘텐츠로 바꿉니다.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-name/azuredeploy.json?range=1-26&highlight=4-10,15)]

## <a name="deploy-template"></a>템플릿 배포

템플릿을 배포해보겠습니다. 다음 예제는 Azure CLI 또는 PowerShell을 사용하여 템플릿을 배포합니다. 스토리지 계정 이름을 배포 명령의 값 중 하나로 제공합니다. 스토리지 계정 이름에 대해 이전 자습서에서 사용한 것과 동일한 이름을 제공합니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](template-tutorial-create-first-template.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](template-tutorial-create-first-template.md#deploy-template)에 표시된 대로 **templateFile** 변수를 템플릿 파일의 경로로 설정했다고 가정합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addnameparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addnameparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

## <a name="understand-resource-updates"></a>리소스 업데이트 이해

이전 섹션에서는 이전에 만든 것과 동일한 이름으로 스토리지 계정을 배포했습니다. 재배포에 의해 리소스가 어떻게 영향을 받는지 궁금할 수 있습니다.

리소스가 이미 있고 속성에서 변경 내용이 검색되지 않으면 아무 동작도 수행되지 않습니다. 리소스가 이미 있고 속성이 변경된 경우에는 리소스가 업데이트 됩니다. 리소스가 존재하지 않으면 만들어집니다.

템플릿을 이런 방식으로 처리하면 Azure 솔루션에 필요한 모든 리소스를 템플릿에 포함할 수 있습니다. 템플릿을 안전하게 재배포할 수 있고 필요한 경우에만 리소스가 변경되거나 생성되는 것을 알 수 있습니다. 예를 들어 스토리지 계정에 파일을 추가한 경우 해당 파일을 손실하지 않고 스토리지 계정을 다시 배포할 수 있습니다.

## <a name="customize-by-environment"></a>환경에 따라 사용자 지정

매개 변수를 사용하여 특정 환경에 맞게 조정되는 값을 제공함으로써 배포를 사용자 지정할 수 있습니다. 예를 들어 개발, 테스트 및 프로덕션 환경 중 어떤 환경에 배포하는지를 기반으로 다른 값을 전달할 수 있습니다.

이전 템플릿은 항상 Standard_LRS 스토리지 계정을 배포했습니다. 환경에 따라 다른 SKU를 배포하는 유연성이 필요할 수도 있습니다. 다음 예제는 SKU에 대한 매개 변수를 추가하기 위한 변경 내용을 보여줍니다. 전체 파일을 복사하여 템플릿에 붙여넣습니다.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json?range=1-40&highlight=10-23,32)]

**storageSKU** 매개 변수에는 기본값이 있습니다. 이 값은 배포 중에 값이 지정되지 않은 경우 사용됩니다. 허용되는 값 목록도 있습니다. 이 값은 스토리지 계정을 만드는 데 필요한 값과 일치합니다. 작동하지 않는 SKU는 템플릿 사용자가 전달하지 않도록 합니다.

## <a name="redeploy-template"></a>템플릿 다시 배포

다시 배포할 준비가 되었습니다. 기본 SKU가 **Standard_LRS**로 설정되어 있기 때문에 해당 매개 변수에 대한 값을 제공할 필요가 없습니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}"
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={your-unique-name}
```

---

템플릿의 유연성을 보기 위해 다시 배포하겠습니다. 이번에는 SKU 매개 변수를 **Standard_GRS**로 설정합니다. 새 이름을 전달하여 다른 스토리지 계정을 만들거나 같은 이름을 사용하여 기존 스토리지 계정을 업데이트할 수 있습니다. 두 옵션 모두 괜찮습니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name usedefaultsku `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU Standard_GRS
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name usedefaultsku \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=Standard_GRS storageName={your-unique-name}
```

---

마지막으로 한 가지 테스트를 더 실행하여 허용된 값에 속하지 않는 SKU를 전달하면 어떻게 되는지 살펴보겠습니다. 템플릿 사용자가 **basic**이 SKU 중 하나라고 생각하는 시나리오를 테스트합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name testskuparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{your-unique-name}" `
  -storageSKU basic
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name testskuparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageSKU=basic storageName={your-unique-name}
```

---

이 명령은 허용되는 값을 알리는 오류 메시지가 표시되면서 즉시 실패합니다. Resource Manager는 배포가 시작되기 전에 오류를 식별합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

매개 변수를 추가하여 [첫 번째 자습서](template-tutorial-create-first-template.md)에서 만든 템플릿을 개선했습니다. 다음 자습서에서는 템플릿 함수에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [템플릿 함수 추가](template-tutorial-add-functions.md)
