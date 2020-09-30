---
title: 자습서 - 매개 변수 파일을 사용하여 템플릿 배포
description: Azure Resource Manager 템플릿을 배포하는 데 사용할 값이 포함된 매개 변수 파일을 사용합니다.
author: mumian
ms.date: 09/10/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurecli
ms.openlocfilehash: de72f9f32a3b08ad1742ee2055efce5b93cab899
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069512"
---
# <a name="tutorial-use-parameter-files-to-deploy-your-arm-template"></a>자습서: 매개 변수 파일을 사용하여 ARM 템플릿 배포

이 자습서에서는 [매개 변수 파일](parameter-files.md)을 사용하여 배포 중에 전달하는 값을 저장하는 방법을 알아봅니다. 이전 자습서에서는 배포 명령에 인라인 매개 변수를 사용했습니다. 이 방식은 ARM 템플릿(Azure Resource Manager 템플릿)을 테스트하는 데 적당하지만 배포를 자동화하는 경우에는 환경에 맞는 값 세트를 전달하는 것이 더 쉬울 수 있습니다. 매개 변수 파일을 사용하면 특정 환경에 대한 매개 변수 값을 쉽게 패키징할 수 있습니다. 이 자습서에서는 개발 및 프로덕션 환경에 대한 매개 변수 파일을 생성합니다. 완료하는 데 **12분** 정도 걸립니다.

## <a name="prerequisites"></a>사전 요구 사항

[태그에 대한 자습서](template-tutorial-add-tags.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Resource Manager Tools 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [템플릿 도구](template-tutorial-create-first-template.md#get-tools)를 참조하세요.

## <a name="review-template"></a>템플릿 검토

템플릿에는 배포 중에 제공할 수 있는 매개 변수가 많이 있습니다. 이전 자습서의 끝 부분에서, 템플릿은 다음과 같았습니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.json":::

이 템플릿은 잘 작동하지만 이제 템플릿에 전달하는 매개 변수를 쉽게 관리하려고 합니다.

## <a name="add-parameter-files"></a>매개 변수 파일 추가

매개 변수 파일은 템플릿과 유사한 구조의 JSON 파일입니다. 배포 중에 전달할 매개 변수 값을 이 파일에 제공합니다.

매개 변수 파일 내에서 템플릿의 매개 변수에 대한 값을 제공합니다. 매개 변수 파일의 각 매개 변수 이름은 템플릿의 매개 변수 이름과 일치해야 합니다. 이름은 대/소문자를 구분하지 않지만, 일치하는 값을 쉽게 확인하려면 템플릿에서 대/소문자를 일치시키는 것이 좋습니다.

모든 매개 변수에 값을 제공할 필요는 없습니다. 지정되지 않은 매개 변수에 기본값이 있는 경우 해당 값은 배포 중에 사용됩니다. 매개 변수에 기본값이 없고 매개 변수 파일에 지정되지 않은 경우 배포 중에 값을 제공하라는 메시지가 표시됩니다.

템플릿의 매개 변수 이름과 일치하지 않는 매개 변수 파일에 매개 변수 이름을 지정할 수 없습니다. 알 수 없는 매개 변수가 제공되면 오류가 발생합니다.

VS Code에서 다음 내용이 포함된 새 파일을 만듭니다. 이 파일을 **azuredeploy.parameters.dev.json**이라는 이름으로 저장합니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.dev.json":::

이 파일은 개발 환경에 대한 매개 변수 파일입니다. 스토리지 계정에 Standard_LRS를 사용하고, 리소스 이름에 **dev**를 접두사로 사용하고 **Environment** 태그를 **Dev**로 설정합니다.

다시, 다음 내용이 포함된 새 파일을 만듭니다. 이 파일을 **azuredeploy.parameters.prod.json**이라는 이름으로 저장합니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-tags/azuredeploy.parameters.prod.json":::

이 파일은 프로덕션 환경에 대한 매개 변수 파일입니다. 스토리지 계정에 Standard_GRS를 사용하고, 리소스 이름에 **contoso**를 접두사로 사용하고 **Environment** 태그를 **Production**으로 설정합니다. 실제 프로덕션 환경에서는 무료가 아닌 SKU로 App Service를 사용할 수 있지만 이 자습서에서는 이 SKU를 계속 사용합니다.

## <a name="deploy-template"></a>템플릿 배포

Azure CLI 또는 Azure PowerShell을 사용하여 템플릿을 배포합니다.

템플릿의 최종 테스트로, 두 가지 리소스 그룹을 새로 만들어 보겠습니다. 하나는 개발 환경용이고 다른 하나는 프로덕션 환경용입니다.

먼저 개발 환경에 배포합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{path-to-the-template-file}"
$parameterFile="{path-to-azuredeploy.parameters.dev.json}"
New-AzResourceGroup `
  -Name myResourceGroupDev `
  -Location "East US"
New-AzResourceGroupDeployment `
  -Name devenvironment `
  -ResourceGroupName myResourceGroupDev `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
templateFile="{path-to-the-template-file}"
devParameterFile="{path-to-azuredeploy.parameters.dev.json}"
az group create \
  --name myResourceGroupDev \
  --location "East US"
az deployment group create \
  --name devenvironment \
  --resource-group myResourceGroupDev \
  --template-file $templateFile \
  --parameters $devParameterFile
```

---

이제 프로덕션 환경에 배포합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$parameterFile="{path-to-azuredeploy.parameters.prod.json}"
New-AzResourceGroup `
  -Name myResourceGroupProd `
  -Location "West US"
New-AzResourceGroupDeployment `
  -Name prodenvironment `
  -ResourceGroupName myResourceGroupProd `
  -TemplateFile $templateFile `
  -TemplateParameterFile $parameterFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
prodParameterFile="{path-to-azuredeploy.parameters.prod.json}"
az group create \
  --name myResourceGroupProd \
  --location "West US"
az deployment group create \
  --name prodenvironment \
  --resource-group myResourceGroupProd \
  --template-file $templateFile \
  --parameters $prodParameterFile
```

---

> [!NOTE]
> 배포에 실패한 경우 **자세한 정보** 스위치를 사용하여 생성되는 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 **디버그** 스위치를 사용합니다.

## <a name="verify-deployment"></a>배포 확인

Azure Portal에서 리소스 그룹을 탐색하여 배포를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
1. 이 자습서에서 배포한 두 가지 새 리소스 그룹이 보입니다.
1. 리소스 그룹을 선택하고 배포된 리소스를 살펴봅니다. 해당 환경에 대한 매개 변수 파일에 지정한 값과 일치하는 것을 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다. 이 시리즈를 완료했으면, 삭제할 리소스 그룹이 세 개(myResourceGroup, myResourceGroupDev 및 myResourceGroupProd) 있습니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

축하합니다, Azure에 템플릿을 배포하는 방법을 마쳤습니다. 의견이나 제안 사항이 있으면 사용자 의견 섹션에서 알려주십시오. 감사합니다.

다음 자습서 시리즈는 템플릿 배포에 대해 자세히 설명합니다.

> [!div class="nextstepaction"]
> [로컬 템플릿 배포](./deployment-tutorial-local-template.md)
