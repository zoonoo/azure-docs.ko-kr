---
title: 자습서 - 빠른 시작 템플릿 사용
description: Azure 빠른 시작 템플릿을 사용하여 템플릿 개발을 완료하는 방법을 알아봅니다.
author: mumian
ms.date: 10/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 99eb1581c03732691af5eaf9f482ca4ba0605863
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75471138"
---
# <a name="tutorial-use-azure-quickstart-templates"></a>자습서: Azure 빠른 시작 템플릿 사용

[Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)은 커뮤니티 제공 템플릿의 리포지토리입니다. 템플릿 개발에 샘플 템플릿을 사용할 수 있습니다. 이 자습서에서는 웹 사이트 리소스 정의를 찾아서 자체 템플릿에 추가합니다. 완료하는 데 **12분** 정도 걸립니다.

## <a name="prerequisites"></a>사전 요구 사항

[내보낸 템플릿에 대한 자습서](template-tutorial-export-template.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Resource Manager Tools 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [템플릿 도구](template-tutorial-create-first-template.md#get-tools)를 참조하세요.

## <a name="review-template"></a>템플릿 검토

이전 자습서의 끝 부분에 템플릿에는 다음 JSON이 있습니다.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/export-template/azuredeploy.json)]

이 템플릿은 스토리지 계정 및 App Service 계획을 배포하는 데 사용되지만 여기에 웹 사이트를 추가할 수도 있습니다. 미리 빌드된 템플릿을 사용하여 리소스를 배포하는 데 필요한 JSON을 신속하게 검색할 수 있습니다.

## <a name="find-template"></a>템플릿 찾기

1. [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)을 엽니다.
1. **검색**에 **deploy linux web app**(Linux 웹앱 배포)을 입력합니다.
1. 제목이 **Deploy a basic Linux web app**(기본 Linux 웹앱 배포)인 항목을 선택합니다. 찾는 데 문제가 있으면 [직접 링크](https://azure.microsoft.com/resources/templates/101-webapp-basic-linux/)를 사용하세요.
1. **GitHub에서 찾아보기**를 선택합니다.
1. **azuredeploy.json**을 선택합니다.
1. 템플릿을 검토합니다. 특히, `Microsoft.Web/sites` 리소스를 살펴봅니다.

    ![Resource Manager 템플릿 빠른 시작 웹 사이트](./media/template-tutorial-quickstart-template/resource-manager-template-quickstart-template-web-site.png)

## <a name="revise-existing-template"></a>기존 템플릿 수정

빠른 시작 템플릿을 기존 템플릿과 병합합니다.

[!code-json[](~/resourcemanager-templates/get-started-with-templates/quickstart-template/azuredeploy.json?range=1-108&highlight=32-45,49,85-100)]

웹앱 이름은 Azure에서 고유해야 합니다. 이름이 중복되지 않도록, **webAppPortalName** 변수가 **"webAppPortalName": "[concat(parameters('webAppName'), '-webapp')]"** 에서 **"webAppPortalName": "[concat(parameters('webAppName'), uniqueString(resourceGroup().id))]"** 으로 업데이트되었습니다.

`Microsoft.Web/serverfarms` 정의의 끝에 쉼표를 추가하여 `Microsoft.Web/sites` 정의와 리소스 정의를 구분합니다.

이 새 리소스에는 몇 가지 중요한 기능이 있습니다.

App Service 계획으로 설정되는 **dependsOn**이라는 요소가 있음을 알 수 있습니다. 웹 앱을 만들기 전에 App Service 계획이 있어야 하기 때문에 이 설정이 필요합니다. **dependsOn** 요소는 배포를 위해 리소스를 정렬하는 방법을 Resource Manager에 알려줍니다.

**serverFarmId** 속성은 [resourceId](template-functions-resource.md#resourceid) 함수를 사용합니다. 이 함수는 리소스의 고유 식별자를 가져옵니다. 이 경우 App Service 계획의 고유 식별자를 가져옵니다. 웹앱은 하나의 특정 App Service 계획과 연결됩니다.

## <a name="deploy-template"></a>템플릿 배포

Azure CLI 또는 Azure PowerShell을 사용하여 템플릿을 배포합니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](template-tutorial-create-first-template.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](template-tutorial-create-first-template.md#deploy-template)에 표시된 대로 **templateFile** 변수를 템플릿 파일의 경로로 설정했다고 가정합니다.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addwebapp `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storagePrefix "store" `
  -storageSKU Standard_LRS `
  -webAppName demoapp
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group deployment create \
  --name addwebapp \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storagePrefix=store storageSKU=Standard_LRS webAppName=demoapp
```

---

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

템플릿 개발에 빠른 시작 템플릿을 사용하는 방법을 알아보았습니다. 다음 자습서에서는 리소스에 태그를 추가합니다.

> [!div class="nextstepaction"]
> [태그 추가](template-tutorial-add-tags.md)
